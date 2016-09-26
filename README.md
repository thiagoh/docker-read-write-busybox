# Using --volumes-from

## First step

To See this work, build the docker images

```shell
cd docker-writer
docker build -t thiagoh/docker-writer .

cd ../docker-reader
docker build -t thiagoh/docker-reader .
```

## First way of using --volumes-from

Create an image which will store the data

```shell
docker run --name data-store -v /my-data busybox true
```
Write some data to the data store

```shell
docker run --volumes-from data-store  thiagoh/docker-writer
```
Read the data from the data store

```shell
docker run -it --volumes-from data-store thiagoh/docker-reader
```
Open another shell to keep writting to the data-store as you want
```shell
docker run -it --volumes-from data-store busybox /bin/sh

/ $ i=0;while [ $i -lt 10 ];do echo "$i line $(date)" >> /my-data/readable-file; i=$(($i+1));done;
```

The ouput in the reader image (thiagoh/docker-reader) will be something like this
```shell
0 line Mon Sep 26 21:00:22 UTC 2016
1 line Mon Sep 26 21:00:22 UTC 2016
2 line Mon Sep 26 21:00:22 UTC 2016
3 line Mon Sep 26 21:00:22 UTC 2016
4 line Mon Sep 26 21:00:22 UTC 2016
5 line Mon Sep 26 21:00:22 UTC 2016
6 line Mon Sep 26 21:00:22 UTC 2016
7 line Mon Sep 26 21:00:22 UTC 2016
8 line Mon Sep 26 21:00:22 UTC 2016
9 line Mon Sep 26 21:00:22 UTC 2016
```

## Second way of using --volumes-from

then run the `docker-writer`

```shell
docker run -it thiagoh/docker-writer
```

copy the docker image ID

```shell
docker ps 

CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                               NAMES
101640a35fd8        thiagoh/docker-writer      "sh"                     2 seconds ago       Up 1 seconds                                            hopeful_ramanujan
```

then run the `docker-reader`

```shell
docker run -it --volumes-from 101640a35fd8 thiagoh/docker-reader
```

This project was inspired in

 - [Tiny Docker Pieces, Loosely Joined](http://www.offermann.us/2013/12/tiny-docker-pieces-loosely-joined.html)
 - [Advanced Docker Volumes](http://crosbymichael.com/advanced-docker-volumes.html)
 - [Creating and mounting a data volume container](https://docs.docker.com/engine/tutorials/dockervolumes/#/creating-and-mounting-a-data-volume-container)
 - [How to deal with persistent storage (e.g. databases) in docker](http://stackoverflow.com/questions/18496940/how-to-deal-with-persistent-storage-e-g-databases-in-docker?rq=1)