# 需要帮助对接火花

> 原文：<https://dev.to/yashwanth2804/need-help-dockerizing-spark-2oc5>

**需要帮助**

我一直在 docker 上工作，在那里我必须运行 spark 应用程序。我试着使用 docker repository spark 图像，但是遇到了问题，所以我试着自己做。

它的工作，但每次运行其下载火花，我失去了以前运行的工作日志。

*我的需求*

1.  有没有可能有一个单独的火花形象，并提供应用程序. jar 给它。

2.  我能不能不在 docker 中写日志，而是把它指向主机文件系统。

*Docker 文件*

```
FROM alpine

ENV SPARK_VERSION=2.2.0
ENV HADOOP_VERSION=2.7

RUN apk add tar
RUN apk add aria2
RUN mkdir spark
RUN cd spark
WORKDIR /spark

#copy app.properties to docker
COPY app.properties .

# copy /home/exa9/SparkSubmit/App/target/App-0.0.1-SNAPSHOT.jar

ADD target/App-0.0.1-SNAPSHOT.jar app.jar

#Downloading Apache Spark and extracting

RUN aria2c -x16 http://archive.apache.org/dist/spark/spark-2.2.0/spark-2.2.0-bin-hadoop2.7.tgz

RUN apk add --no-cache curl bash openjdk8-jre \

      && tar -xvzf spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz

WORKDIR /spark/spark-2.2.0-bin-hadoop2.7/bin
CMD ./spark-submit --class com.Spark.Test.SparkApp.App --master local[*]  /spark/app.jar /spark/app.properties 
```