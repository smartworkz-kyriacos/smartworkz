+++
author = "David Bros"
bg_image = ""
categories = ["Logstash"]
date = 2022-11-04T23:00:00Z
description = ""
image = "/images/logstash.jpg"
tags = []
title = "Logstash"
type = "post"

+++
This is a continuation of the [previous](https://dev.to/thehunter896/elk-elasticsearch-in-5-minutes-5bfn) article Elasticsearch in 5 minutes, Logstash is the second essential tool for Data Engineering. Like Elasticsearch it is developed and maintained by the Elastic team and it also has a free version available (which will cover all your needs).

In this article we will learn what Logstash is and how it works, we will also set up a Logstash processor and play with some of the plugins it has to offer. All in under 5 minutes!

For this article, I have set up a [GitHub Repository](https://github.com/TheHunter896/logstash-in-five-minutes) where you can find all the files needed to complete some of the steps as well as the images within this article.

***

**What is Logstash**

Logstash is a data processor, it acts as the pipeline between your databases or nodes and other resources, which could be other databases, nodes or applications.

![Logstash Diagram](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rznkk8h6pj1jz3o7pnol.png)

However, the true power of Logstash comes in its data manipulation functions: Transforming and Filtering operations.

**Where Logstash Shines**

* Data Processing
* Data Streaming Environments
* Data Analytics Environments

**Why does it shine in these environments**

* High compatibility with input and output resources: Logstash can read from almost anything, including Cloud applications, message Brokers and WebSockets. Here is the list of [input](https://www.elastic.co/guide/en/logstash/current/input-plugins.html) and [output](https://www.elastic.co/guide/en/logstash/current/output-plugins.html) plugins.
* Fast and flexible transformation functions: Transform data structures with simple JSON-defined operations.
* Filtering: Huge range of filtering functions are at your disposal, you can use this in different pipelines to create multiple sources from one raw source.

**What version are we setting up**

We will be setting up a free Logstash processor version 8. x

***

**Step 1, Docker**

For ease of use, we will be using a Centos7 Docker container.

Pull the image:

`docker pull centos:7`

You can find the image [here](https://hub.docker.com/_/centos)

Run the container with SYS Admin permissions and a mounted volume:

`docker run -id --cap-add=SYS_ADMIN --name=logstash-centos7 -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos:7 /sbin/init`

_We run the container with SYS ADMIN permissions because we need systemctl to work inside the container_

Connect to the container:

`docker exec -it logstash-centos7 /bin/bash`

***

**Step 2, Machine Setup**

Update the machine:

`yum update -y && yum upgrade -y`

Install a text editor (any):

`yum install vim -y`

***

**Step 3, Install Logstash**

Install the GPG key for elastic:

`sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch`

Add this logstash repo under **/etc/yum.repo.d/logstash.repo**:

> \[logstash-8.x\]

> name=Elastic repository for 8.x packages

> baseurl=https://artifacts.elastic.co/packages/8.x/yum

> gpgcheck=1

> gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch

> enabled=1

> autorefresh=1

> type=rpm-md

**Remember to save!**

Download and install the repo

`yum install logstash`

Start logstash

`systemctl start logstash`

Verify logstash is running

`systemctl status logstash`

![Running Logstash Service](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c1phahtfbynr0clyxopm.png)

Now stop logstash, we'll restart it later down the line

`systemctl stop logstash`

***

**Step 4, set up an elastic node**

We will be using Elasticsearch as input for our pipelines, so you will need to set up another Docker image with an elastic node, you can find a guide [here](https://dev.to/thehunter896/elk-elasticsearch-in-5-minutes-5bfn) if you don't have one already running.

Annotate the IPs of your containers

By default Docker sets up a network called a bridge which containers can use to communicate between themselves, you can see the IPs by first seeing your container IDs, and then inspecting the container:

`docker ps`

![Docker ps](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/va7g20z5c61rom0udpm6.png)

`docker inspect {docker container id}`

Docker inspect will yield a very large JSON, search for the field IPAddress

![IPAddress field](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1lx68bfe8aa5tvguhaec.png)

Annotate the IPs for both containers, we'll use them in the next step.

Create a **test_pipeline** index and insert sample data, you need to do this inside your Elasticsearch container. You can copy [this](https://github.com/TheHunter896/logstash-in-five-minutes/blob/master/sample_data.txt) sample data and paste it under **/tmp/sample_data.json**

`vim /tmp/sample_data.json`

![Create index](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h71jokw58qimimk9jp1h.png)

Check the index has been created correctly

`curl -X GET --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic https://localhost:9200/_cat/indices`

![Cat Indices](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fibuadm4rlyuno8e66vw.png)

Index this sample data we previously saved

`curl -X POST --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic -H 'Content-Type: application/nx-ndjson' https://localhost:9200/_cat/indices --data-binary @/tmp/sample_data.json`

![Index Documents](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c16j27f6fsd7hddfpixb.png)

Create another index called **test_pipeline_output**

Due to time constraints (hint on the title) we are going to take some shortcuts: Disabling SSL and Setting our replicas to 0.

To disable SSL you need only change **xpac.security.enabled** to **false** under the file **/etc/elasticsearch/elasticsearch.yml**

![XPACK Security Deactivation](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rscxjr78gbh9e6p247az.png)

Restart elasticsearch

`systemctl restart elasticsearch`

Now set your replicas to 0, do this step for both **test_pipeline** and **test_pipeline_output** indices:

`curl -X PUT -u elastic http://localhost:9200/**test_pipeline**/_settings -H 'Content-Type: application/json' --data '{"index": {"number_of_replicas": 0}}'`

***

**Step 5, set up a logstash pipeline**

Copy [this](https://github.com/TheHunter896/logstash-in-five-minutes/blob/master/conf/pipeline.conf) configuration under

**/etc/logstash/conf.d/pipeline.conf**

The purpose of this pipeline is to get the messages from **index test_pipeline** to another index called **test_pipeline_output**

The only thing left now is to restart logstash in our logstash docker container.

`systemctl restart logstash`

Let it run for a couple of minutes, after that check indices in your Elasticsearch node:

`curl -X GET --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic http://localhost:9200/_cat/indices/`

!\[Indices after logstash\](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/i2gpik7x9jl47lq4ysrd.png

)

In the image you can see that the output index has 100 items, while the original has 10, this is because logstash will continuously run this pipeline every few seconds.

In any case, with this article, we have successfully set up a Logstash processor and moved data around with a very basic setup.

![It just works](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3wwi35zlkd9g6y15k2f0.gif)

Follow me for part 3, where we'll set up a Kibana UI in 5 minutes!