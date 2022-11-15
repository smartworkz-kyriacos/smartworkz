+++
author = "Sander Kerkdijk"
bg_image = "/images/banner/banner-1.jpg"
categories = ["Python", "Pandas", "Polars"]
date = 2022-11-05T23:00:00Z
description = ""
image = "/images/pandaspolars.jpg"
tags = []
title = "Working with Polars"
type = "post"

+++
### **Introduction**

Excerpt reference from [Robin van den Brink](https://medium.com/analytics-vidhya/introduction-to-polars-ee9e638dc163)

Pandas is the most popular library when it comes to working with structured data. The reason behind this is the panda’s powerful tool called DataFrame. A DataFrame is a table where each column represents a different type of data(sometimes called a field). The columns have names. Each row represents a record or entity.

In this article, we look at an alternative for Pandas that is almost 3 times faster. [Polars](https://github.com/pola-rs) is one of the lesser-known libraries and was only released a few months ago. Pandas is still one of the best tools out there for data manipulation and analysis, and in no way Polars can replace it, at least for the time being. I just wanted to share this library to make my readers know about an alternative they can try out for fun.

Polars is a DataFrame library written in the [Rust](https://www.rust-lang.org/) programming language and uses [Apache Arrow](https://arrow.apache.org/) as a foundation. Polars utilizes parallelization of data computing on multiple cores of the device, and this is what gives Polars its speed. The goal of Polars is to give a much more fluid and swift experience than pandas, and I think it does a pretty decent job. [H2Oai’s ](https://h2oai.github.io/db-benchmark/)DB benchmark shows that it is the fastest DataFrame library.

Here is an excerpt taken from the [Polars book](https://pola-rs.github.io/polars-book/user-guide/index.html):

> The goal of `Polars` is to provide a lightning fast `DataFrame` library that utilizes all available cores on your machine. Unlike tools such as dask -- which tries to parallelize existing single-threaded libraries like `NumPy` and `Pandas` -- `Polars` is written from the ground up, designed for parallelization of queries on `DataFrame`s.

In this article, we present an introduction to the Polars library covering most of the basic stuff. For we will use the [Wine Reviews dataset](https://www.kaggle.com/zynicide/wine-reviews) which you can find on Kaggle.

### **Installing Polars**

Polars can be installed using Pypi using the following code:

    pip install polars

### **Importing libraries**

Polars offers many functionalities that are similar to Pandas, so it won’t be a problem for anyone to switch over.

    import polars as pl
    import matplotlib.pyplot as plt
    %matplotlib inline

### **Loading Dataset**

    data = pl.read_csv("../winemag-data_first150k.csv")
    print(type(data))
    > <class 'polars.frame.DataFrame'>

Let us start with a basic Data Analysis.

### **Getting familiar with the dataset**

    data.shape
    > (150930, 11)
    
    data.columns

![](/images/96375p7.png)

    data.dtypes

![](/images/29576p8.png)

    data.head()

![](/images/75570p9.png)

As you can see this is a huge dataset. with over 11 columns and 150k+ entries, we have a lot of data to analyze. The columns I am interested in are Country, points, and price. Let us see what we can find.

### **Null Values**

Before moving forward we have to take care of the null values if present. We can find the null values easily using _null_count()_.

    data.null_count()

![](/images/63342p10.png)

Therefore around 13.5k entries are missing values for the price column. We can either drop these rows since it’s less than 10% of the whole dataset, but we can put some other value like the _mean_:

    data['price'] = data['price'].fill_none('mean')

### **Performing Analysis**

Now we dig a little deeper and look into some statistical analysis. This can help us gain some insightful knowledge of the dataset.

Our goal is to compare how price_s_ and points vary from country to country.

    # Analyses of wine prices
    print(f'Median price: {data["price"].median()}')
    print(f'Average price: {data["price"].mean()}')
    print(f'Maximum price: {data["price"].max()}')
    print(f'Minimum price: {data["price"].min()}')

![](/images/19918p11.png)

    # Analyses of wine points
    print(f'Median points: {data["points"].median()}')
    print(f'Average points: {data["points"].mean()}')
    print(f'Maximum points: {data["points"].max()}')
    print(f'Minimum points: {data["points"].min()}')

![](/images/69478p12.png)

Thus we can see that a wine can be as cheap as 4 dollars but still have great taste. Now let’s see which countries sell wine.

    countries = data['country'].unique().to_list()
    print(f'There are {len(countries)} countries in the list')
    >There are 49 countries in the list

Scrolling through the dataset, we can see that there are 2 strange values in the column _country_. These are an undefined country (“”) and another country called ‘US-France’:

    print(data[(data['country'] == '') | (data['country'] == 'US-France')])

![](/images/53330p13.png)

Since there are just 6 entries with these weird values, so I think it’s safe if we dropped the rows.

    data = data[(data['country'] != '') & (data['country'] != 'US-France')]

Now we look into countries which have the best and the costliest wines.

    #wines with high points
    print(data.groupby('country').select('points').mean().sort(by_column='points_mean', reverse=True))

![](/images/38521p14.png)

    #Wines which are costly
    print(data.groupby('country').select('price').max().sort(by_column='price_max', reverse=True))

![](/images/35986p15.png)

Thus we can see that England has one of the best wines, but the costliest one is from France.

## Plotting with Polars

It is always nice to have some visualizations to have a better understanding of the data. Pandas have inbuilt plotting capabilities, but the majority use Matplotlib and Seaborn since it’s much easier to use and offer a wider variety of plots.

    top_15_countries = data.groupby('country').select('points').mean().sort(by_column='points_mean', reverse=True)[:15][0]
    df_top15 = pl.DataFrame({'country': top_15_countries}).join(data, on='country', how='left')
    fig, ax = plt.subplots(figsize=(15, 5))
    for i, x in enumerate(df_top15['country'].unique()):
        ax.boxplot(df_top15[df_top15['country'] == x]['points'], labels=[str(x)], positions=[i])
    plt.xticks(rotation=90)
    plt.xlabel('Countries')
    plt.ylabel('Average points')
    plt.show()

![](/images/16050p16.png)

## Notes

Polars is still an ongoing project and in the early stages of development. If you are interested in having a more in-depth look at the workings of the library, we highly recommend you read this article by the creator of Polars himself:

[I wrote one of the fastest DataFrame Libraries | Ritchie Vink](https://www.ritchievink.com/blog/2021/02/28/i-wrote-one-of-the-fastest-dataframe-libraries/)

[At the BLIPZ](https://www.blipz.io/speakers/ritchie-vink)