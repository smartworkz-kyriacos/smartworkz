+++
author = "David Bros"
bg_image = "images/banner/banner-1.jpg"
categories = ["Elasticsearch"]
date = 2022-11-04T23:00:00Z
description = ""
image = "/images/elasticsearch.jpg"
tags = []
title = "Elasticsearch"
type = "post"

+++
If you claim to be a Data Engineer or a DevOps engineer then you surely have heard of **Elasticsearch**, this technology has been gaining traction for all the right reasons.

Although you can use Elasticsearch only for storage, it is highly recommended to use it alongside it's brothers: Logstash and Kibana, we will get to those in other posts.

---

**What is Elasticsearch**

Elasticsearch is a NoSQL document database (JSON), its environment revolves around 3 main concepts:

- Nodes: An Elasticsearch node is a server which stores data, it does this through shards.
- Shards: Shards are a storage abstraction that provides easier access to your data as well as support for replication.
- Indices: Indices are the second storage abstraction, they are what you are going to interact with when fetching or aggregating data from the Elasticsearch API.

<br>

<center>

![Elastic Node](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vnizubkxockvhpi2rqsv.png)

</center>

<br>

Where Elasticsearch shines:

- Data Streaming Environments: Together with Logstash
- Data Analytics Enviornments: Together with an Analytics Frontend such as Kibana
- Data Lake: Use the Elasticsearch REST API, compatible with any data analytics processing application.

Why does it shine in these environments:

- Fast and efficient I/O operations: Provided by sharding and indexing.
- Aggregation support: Elasticsearch has an incredibly flexible REST API that you can use from any client to make custom aggregations and data structures.
- Kibana: Kibana is a front end application that fully integrates with Elasticsearch, it uses the Elasticsearch REST API to support all sorts of graph visualizations.`<br>`

**What version are we setting up**

Elasticsearch has a payed and free version, you'll learn how to set up the free version.

---

**Step 1, Docker**:

For ease of usage, let's use Docker. As mentioned in the beginning, we'll use docker and CentOS 7.

Pull the image:

`docker pull centos:7`

You can find the image [here](https://hub.docker.com/_/centos)

Run the container with SYS Admin permissions and a mounted volume:

`docker run -id --cap-add=SYS_ADMIN --name=elasticsearch-centos7 -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos:7 /sbin/init`

_We run the container with SYS ADMIN permissions because we need systemctl to work inside the container_

Connect to the container:

`docker exec -it elasticsearch-centos7 /bin/bash`

---

**Step 2, Machine Setup**

Update the machine:

`yum update -y && yum upgrade -y`

Install a text editor (any):

`yum install vim -y`

---

**Step 3, Install Elasticsearch**

Install the elasticsearch repo under **/etc/yum.repos.d/elasticsearch.repo**:

`vim /etc/yum.repos.d/elasticsearch.repo`

Paste the following text:

> [elasticsearch]

> name=Elasticsearch repository for 8.x packages

> baseurl=https://artifacts.elastic.co/packages/8.x/yum

> gpgcheck=1

> gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch

> enabled=0

> autorefresh=1

> type=rpm-md

**Remember to save!**

You can find the latest version of this configuration [here](https://www.elastic.co/guide/en/elasticsearch/reference/8.1/rpm.html#rpm-repo)

Install Elasticsearch:

`yum install --enablerepo=elasticsearch elasticsearch -y`

There are other options to download the repository, but this is the easiest.

Reload the daemon

`systemctl daemon-reload`

Enable and Start elasticsearch:

``systemctl enable elasticsearch && systemctl start elasticsearch``

---

**Step 4, Verify**

Check the service is running

`systemctl status elasticsearch`

![ElasticSearch Service Running](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xkkl6d4dlhqje3grxfjd.png)

Reset your password for user **elastic**

`/usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic`

Confirm that elasticsearch works by querying the Elasticsearch API (https enabled by default):

`curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic https://localhost:9200`

![ElasticSearch API Repsponse](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xp6cxmfqe5od8tinwwn8.png)

You are now the proud owner of a free ElasticSearch Node!

![It just works](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3wwi35zlkd9g6y15k2f0.gif)

Follow me for part 2, where we'll set up Logstash in 5 minutes!
