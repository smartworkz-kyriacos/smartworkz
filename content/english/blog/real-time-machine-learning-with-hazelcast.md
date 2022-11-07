+++
author = "Kyriakos Antoniadis"
bg_image = "/images/banner/banner-1.jpg"
categories = ["Redis", "Hazelcast"]
date = 2022-11-05T23:00:00Z
description = ""
image = "/images/hazelcast.jpg"
tags = []
title = "Real-time ML with Hazelcast"
type = "post"

+++
### Introduction

Real-Time Machine Learning is a technique of continuously improving a machine learning model by training it with real-time rich data. Real-time machine learning models can be deployed to production using event-driven architectures in which rich data streams are fed into these models by combining data-at-rest with data-in-motion. However, deploying real-time machine learning models has its challenges: such as complexity, scalability and performance. In this seminar, Fawaz will address those challenges and demonstrate the best practices for real-time machine learning models, written in Python, Java or using remote services, from training to inference and deployment with ultra-low latency at scale and speed, using the Hazelcast platform.

### Install Hazelcast Client

Before moving forward, we should install the Hazelcast client. Hazelcast clients allow you to extend the benefits of operational in-memory computing to your application. There are clients for six different languages including Java, Go, and C#. These clients are open-source and supported by Hazelcast. After installing the client, you can directly use it by simply adding an `import hazelcast` line.

***

    %pip install hazelcast-python-client

***

To use the Hazelcast client, you need to have a running Hazelcast cluster. You can quickly create a new account in Hazelcast Viridian and create a cluster. After creating the cluster, we need your cluster name, discovery token and SSL password token to connect from this notebook. You can find these tokens from **Connect Client > Advanced Setup** tab in the Hazelcast Viridian dashboard. Also, please download the certificate ZIP file by clicking "Download Keystore file", we will need SSL certificate files to establish a secure connection with the cluster.

![](/images/giphy.gif)

***

### Hazelcast Viridian Authentication Tokens

Run this cell from the top-left corner and paste your Viridian tokens to textboxes that will show up. It will ask for your cluster name, discovery token and Keystore password. Also, a file upload button will be shown up for you to upload the ZIP file that contains your certificates.

\#@title Hazelcast Viridian Authentication Tokens #@markdown Run this cell from the top-left corner and paste your Viridian tokens to textboxes that will show up. It will ask for your cluster name, discovery token and Keystore password. Also, a file upload button will be shown up for you to upload the ZIP file that contains your certificates.

    from google.colab import files
    from getpass import getpass
    import random
    import string
    import zipfile
    import textwrap
    
    CLUSTER_NAME = getpass('Your cluster name: ')
    DISCOVERY_TOKEN = getpass('Your discovery token: ')
    SSL_PASSWORD = getpass('Your keystore/trustore password token: ')
    
    uploaded = files.upload()
    path = "/content/" + list(uploaded.keys())[0]
    destination = "/content/hz-keys-"+''.join(random.choice(string.ascii_lowercase) for i in range(10))
    
    !unzip {path} -d {destination}
    
    CA_PATH  =destination+"/ca.pem"
    CERT_PATH=destination+"/cert.pem"
    KEY_PATH =destination+"/key.pem"

***

Instead of hard-coding data entries to our Map, we will use an API source to pull large amounts of data to our cluster. We decided to use a movie API named **The Movie Database (TMDB)** which returns in-depth movie and actor data for us as JSON objects. To be able to send a request to this API, you will need an API key. You can create it in 1-2 minutes from their website. Note that you need to create an account first and then go to **Settings > API > Request an API Key**. It may ask for some information about your project; providing short descriptions like "Experimenting API requests" is enough.

[https://www.themoviedb.org/signup](https://www.themoviedb.org/signup "https://www.themoviedb.org/signup")

***

### Connect to API

Run this cell from the top-left corner and paste your API key to the textbox.

***

Now, we have ready data source and data storage and now it's the turn of the Python client. We need to install the Hazelcast Python client to use the functionality of Hazelcast from our Python application. You can embed Hazelcast inside your program by simply inserting a module, `hazelcast-python-client`and utilising all the methods using this package.

***

### Imports

***

    import hazelcastimport requests

***

### Connect To Hazelcast Cluster

***

We are ready to connect our cluster from the Python program. Hazelcast uses config settings to find your cluster. We are providing our Viridian tokens for configuration to connect Hazelcast Cloud. After seeing the `Connection successful.` the message, we can create mappings for our data.

Reminder: If you want to connect to a local cluster in Jupyter Notebook, you should download the notebook file and remove the Viridian configuration options inside `hazelcast.HazelcastClient()` functional call.

***

    hazelcast.discovery.HazelcastCloudDiscovery._CLOUD_URL_BASE = "api.viridian.hazelcast.com"
    client = hazelcast.HazelcastClient(
    cluster_name=CLUSTER_NAME,
    cloud_discovery_token=DISCOVERY_TOKEN,
    statistics_enabled=True,
    ssl_enabled=True,
    ssl_cafile=CA_PATH,
    ssl_certfile=CERT_PATH,
    ssl_keyfile=KEY_PATH,
    ssl_password=SSL_PASSWORD
    )
    print("Connection successful. You are connected to your Viridian cluster named %s." % CLUSTER_NAME)

***

### Create Mapping between your data and Map

***

Here is the critical part. We will insert the data from TMDB API into our Hazelcast Cluster. You can do this in two ways. Firstly, you can get a map by calling `hazelcast.get_map()` method and use `put()`, `remove()` and other methods of the returned distributed map object. Secondly, you can create a mapping for your data schema using SQL syntax and it directly creates both a map and mapping. We preferred the second way to show SQL functions in this notebook.

In the mapping query, you should specify the fields of the data schema. It can be both primitive types or complex types. Using this mapping, we will execute SQL queries on our maps. You can select any column you want to map; not all fields are mandatory. For more information about mapping, you can visit [https://docs.hazelcast.com/hazelcast/5.1/sql/mapping-to-maps](https://docs.hazelcast.com/hazelcast/5.1/sql/mapping-to-maps "https://docs.hazelcast.com/hazelcast/5.1/sql/mapping-to-maps")

Now, execute the following cells to create mappings to execute SQL queries against your data.

***

    query = """    
    	CREATE OR REPLACE MAPPING movies (        
        	__key INT,         
            id INT,        
            title VARCHAR,        
            description VARCHAR,        
            popularity DOUBLE,        
            release_date VARCHAR,        
            vote_average DOUBLE,        
            vote_count INT    
        )    
        TYPE IMap OPTIONS(        
            'keyFormat' = 'int',        
             'valueFormat' = 'json-flat'    
        )
        """
    client.sql.execute(query).result()
    print("Mapping for 'movies' has been created.")

***

    query = """    
    	CREATE OR REPLACE MAPPING casts (
        	__key INT,        
            movie_id INT,        
            actor_id INT    
     	)    
     	TYPE IMap OPTIONS(
          'keyFormat' = 'int',        
        	'valueFormat' = 'json-flat'    
     	)
     """
     client.sql.execute(query).result()
     print("Mapping for 'cast' has been created.")

***

    query = """    
    	CREATE OR REPLACE MAPPING actors (
        	__key INT,        
            gender INT,        
            id INT,        
            name VARCHAR,        
            popularity DOUBLE    
    	)   
        TYPE IMap OPTIONS(
        	'keyFormat' = 'int',        
            'valueFormat' = 'json-flat'    
        )
        """
        client.sql.execute(query).result()
        print("Mapping for 'actors' has been created.")

***

    query = """    
    	CREATE OR REPLACE MAPPING reviews (
        	__key INT,        
            movie_id INT,        
            author VARCHAR,        
            rating DOUBLE,        
            content VARCHAR    
         )    
         TYPE IMap OPTIONS(
         	'keyFormat' = 'int',        
            	'valueFormat' = 'json-flat'    
         )
         """
         client.sql.execute(query).result()
         print("Mapping for 'reviews' has been created.")

***

You can control your mappings by opening Management Center for your cluster. Select your cluster from your Viridian dashboard and then click on "Management Center". It will open the Management Center in a new tab, you can inspect your cluster properties from this page. Click on "SQL Browser" at the top-right of the page to open the SQL prompt.

![](/images/giphy(1).gif)

***

### Load Data from API

***

Now, we are ready to pull our data from the TMDB API and load them to our Hazelcast Cluster. To do that, we need to send requests to our API provider. The following cells send API requests and insert results into appropriate maps.

**This step may take time, depending on your connection speed and server latency. As long as there is no error and an `[*]` is present near to cell, do not worry.**

***

    key = 1
    for i in range(1,25):
        data = requests.get("https://api.themoviedb.org/3/movie/top_rated?api_key=%s&language=en-US&page=%d" % (API_KEY, i)).json()
        for m in data['results']:
          query = """SINK INTO movies(__key, id, title, description, popularity, release_date, vote_average, vote_count)
                     VALUES(?, ?, ?, ?, ?, ?, ?, ?)""" 
          client.sql.execute(query,
                             key, 
                             m["id"], 
                             m["original_title"].replace("'","''"),
                             m["overview"].replace("'","''"), 
                             m["popularity"], 
                             m["release_date"], 
                             m["vote_average"], 
                             m["vote_count"]).result()
          key += 1
    print("Movie data has been loaded.")

***

    key = 0; cast_key = 0;for i in range(1,50):    data = requests.get("https://api.themoviedb.org/3/person/popular?api_key=%s&language=en-US&page=%d" % (API_KEY,i)).json()    for a in data['results']:        query = """SINK INTO actors(__key, gender, id, name, popularity)                   VALUES(?, ?, ?, ?, ?)"""        client.sql.execute(query,                           key,                            a["gender"],                            a["id"],                           a["name"],                           a["popularity"]).result()        key += 1        for c in a['known_for']:            if c['media_type'] == 'movie':                query = """SINK INTO casts(__key, movie_id, actor_id)                          VALUES(?, ?, ?)"""                client.sql.execute(query,                                   cast_key,                                   c['id'],                                   a['id']).result()                cakey = 0; cast_key = 0;
    for i in range(1,50):
        data = requests.get("https://api.themoviedb.org/3/person/popular?api_key=%s&language=en-US&page=%d" % (API_KEY,i)).json()
        for a in data['results']:
    
            query = """SINK INTO actors(__key, gender, id, name, popularity)
                       VALUES(?, ?, ?, ?, ?)"""
            client.sql.execute(query,
                               key, 
                               a["gender"], 
                               a["id"],
                               a["name"],
                               a["popularity"]).result()
            key += 1
            for c in a['known_for']:
                if c['media_type'] == 'movie':
                    query = """SINK INTO casts(__key, movie_id, actor_id)
                              VALUES(?, ?, ?)"""
                    client.sql.execute(query,
                                       cast_key,
                                       c['id'],
                                       a['id']).result()
                    cast_key += 1
    print("Actor and Cast data has been loaded.")st_key += 1print("Actor and Cast data has been loaded.")

***

    key = 0
    with client.sql.execute("SELECT id FROM movies WHERE __key< %d "% 200).result() as result:
        for row in result:
          id = row['id']
    
          url = "https://api.themoviedb.org/3/movie/%d/reviews?api_key=%s&language=en-US&page=1" % (id, API_KEY)
          data = requests.get(url).json()
    
          for r in data['results']:
    
            query = """SINK INTO reviews(__key, movie_id, author, rating, content)
                       VALUES(?, ?, ?, ?, ?)"""
            client.sql.execute(query,
                              key,
                              id, 
                              r['author_details']['username'], 
                              r['author_details']['rating'], 
                              r['content'].replace("'","''")).result()
            key += 1
    
    print("Review data has been loaded.")

***

After the loading data operations, there should be approximately

* 900 entry in `actors` map
* 2400 entry in `casts` map
* 500 entry in `movies` map
* 300 entry in `reviews` map

You can control your Map size and content from the Management Center.

![](/images/giphy(2).gif)

***

### The fun part: SQL queries

In the following examples, we will observe different SQL functions supported by Hazelcast and execute them to process our data quickly. You can edit the queries directly and experiment with SQL support hands-on. Fasten the belts!

***

#### Movies released before 2015 and high vote average with cote count > 20K

In this first query, we will use essential features of SQL, like the `WHERE` clause. We have some filters to select movies among 4K entries, like `vote_count`, `vote_average`, and `release_date`. You can use `AS` keyword _o specify the column name of res_ult. Without Hazelcast, typically, we need to iterate over our map and check whether the current entry fits into our definition or not. Also, this approach requires an additional structure to store our selected entries. With the inclusive SQL support of Hazelcast, we can translate our filters to a SQL query and execute it on our map.

***

    query = """
        SELECT m.title AS name, m.vote_average AS average, m.vote_count AS cont
        FROM movies m
        WHERE m.vote_count > 20000 AND m.vote_average > 7 AND m.release_date < '2015-01-01'
        ORDER BY m.popularity DESC
    """
    
    result = client.sql.execute(query).result()
    
    for row in result:
        print('%s has vote average is %.2f with %s voting.' % (row['name'], row['average'], row['cont']) )

***

### Here are all-star cast movies for you

Sometimes you may want to watch a movie with many star actors! Easy to find those movies among thousands of entries! You need to `JOIN` your two maps on `movie id` and filter those with a total actor popularity point larger than 500. You can use `SUM()` the function to find the total value of a column for a group, in this case, for every movie since we are grouping according to `title` of movie. You don't have to deal with any loops, control statements, or additional storage!

***

    query = """
        SELECT m.title AS title, m.release_date AS r_date, SUM(a.popularity) AS point
        FROM movies m
        JOIN casts c ON m.id = c.movie_id
        JOIN actors a ON c.actor_id = a.id
        GROUP BY m.title, m.release_date
        HAVING SUM(a.popularity) > 150
        ORDER BY point DESC
    """
    
    result = client.sql.execute(query).result()
    
    for row in result:
            print("{0:30} {1:30} Total Popularity Point: {2}".format(row['title'], row['r_date'], row['point']))

***

### Get the movies that get the highest review ratings from users

Now, we will try to get the top ten movies with average ratings higher than eight and at least reviewed by five different users. Note that `reviews` these are separate maps and different than `vote` which is a column of `movies` table. It looks complicated, right? Typically, you must traverse the two maps to square. First, get all the reviews for a movie, calculate the average rating, store results in another data structure, and repeat it for all the movies. In the end, you must sort the results in descending order and get the highest ten movies.

Don't give up, we are there for you! Using the Hazelcast SQL support, you can use the power of SQL functions and aggregate operations like `ORDER BY()`, `LIMIT()`, and `AVG()` on your Map! You need to write your SQL query as a string and then execute it on your map with just one line, `sql.Execute()`. It's that easy! This operation returns a table, which consists of rows. You can traverse inside results by a simple `for` loop.

***

    query = """
        SELECT m.title as name, AVG(r.rating) as rating
        FROM movies m
        JOIN reviews r ON r.movie_id = m.id
        GROUP BY m.title
        HAVING AVG(r.rating) > 8 AND COUNT(*) > 5
        ORDER BY rating DESC
        LIMIT 10
    """
    
    result = client.sql.execute(query).result()
    
    for row in result:
            print("Movie Name: %s - Average Rating: %.2f" % (row['name'], row.get_object('rating')))

***

### Curious about user reviews?

There are many detailed reviews in our database. You can find a randomly selected entry for a movie or actor you are curious about. You can use the `LIKE` keyword for searching a specific keyword in our Map. For example, we searched for reviews that contain the "recommend" keyword and got one between them randomly using the `RAND()` function. You can increase `LIMIT` to get more reviews and add multiple keywords.

You can try the same thing with movie descriptions to find specific movie information, just use `movie.description` column!

***

    query = """
        SELECT m.title, r.author, r.rating, r.content
        FROM movies m, reviews r
        WHERE r.movie_id=m.id AND 
              r.content LIKE '%recommend%' AND 
              r.rating IS NOT NULL
        ORDER BY RAND()
        LIMIT 1
    """
    
    result = client.sql.execute(query).result()
    
    for row in result:
            print(("Movie Name: %s") % row.get_object('title'))
            print("-"*75)
    
            print("Review by %s rating is %s/10" % (row.get_object('author'), row.get_object('rating')))
            print("-"*75)
            print(textwrap.fill(row.get_object('content'), 75))

***

### Conclusion

***

Most of the data produced by applications are wasted due to the high workload of processing, especially in real-time. As you can see from the examples, even if it is a simple map search, the algorithmic complexity and effort to execute the filters are tremendous.

Hazelcast offers a simple solution to evaluate and process all your data more easily and quickly as a real-time data processing platform. Using Hazelcast's SQL engine, you can skip all of the details and directly work on the value of your customer's data. You can infer much information without dealing with hundreds of line codes and slow executions.

You can follow our website and social media channels to learn more about the Hazelcast ecosystem. We would love to answer your questions over our Community channels, find links here: [https://hazelcast.com/dev-community/](https://hazelcast.com/dev-community/ "https://hazelcast.com/dev-community/"), also feel free to reach out at [developer-relations@hazelcast.com](mailto:developer-relations@hazelcast.com).

Having a more in-depth look at the workings of the library, we highly recommend you read this article by the author of Hazelcast himself:

[Fawaz Ghali](https://www.fawazghali.com/aboutme/)

[At the BLIPZ](https://www.blipz.io/speakers/fawaz-ghali)