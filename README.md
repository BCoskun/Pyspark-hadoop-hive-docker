# Pyspark-hadoop-hive-docker

Two steps to create a pyspark hadoop cluster using docker.
1. Build the image
2. Run docker compose command to start the cluster.

Base image used are as below:
* tensorflow/tensorflow:2.12.0-gpu-jupyter
* jupyter/minimal-notebook:python-3.8
* postgres:11


## Software

* [Hadoop 3.3.6](https://hadoop.apache.org/)

* [Hive 3.1.3](http://hive.apache.org/)

* [Spark 3.3.4](https://spark.apache.org/)

## Quick Start

To deploy the cluster, run:
```
make
docker-compose up
```

## Access interfaces with the following URL

### Hadoop

ResourceManager: http://localhost:8088

NameNode: http://localhost:9870

HistoryServer: http://localhost:19888

Datanode1: http://localhost:9864
Datanode2: http://localhost:9865

NodeManager1: http://localhost:8042
NodeManager2: http://localhost:8043

### Spark
master: http://localhost:8080

worker1: http://localhost:8081
worker2: http://localhost:8082

history: http://localhost:18080

### Hive
URI: jdbc:hive2://localhost:10000

### Jupyter Notebook
URL: http://localhost:8888

Source of these code is from below github repository, below code did not worked for me and I need to make quite changes to make it work.
https://github.com/myamafuj/hadoop-hive-spark-docker

