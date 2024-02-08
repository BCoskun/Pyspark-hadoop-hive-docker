FROM jupyter/minimal-notebook:python-3.8
USER root

RUN apt-get -y purge --auto-remove python3.10
RUN sed -i -e "s|http://archive.ubuntu.com|http://us.archive.ubuntu.com|g" /etc/apt/sources.list 
RUN apt-get -y update  
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends \
      sudo \
      openjdk-8-jdk \
      curl \
      gnupg \
      procps \
      coreutils \
      libc6-dev 
RUN rm -rf /var/lib/apt/lists/*

#ARG USERNAME=jovyan
ARG USERNAME=bcoskun    
ARG GROUPNAME=users
ARG UID=1000
ARG GID=100

RUN sudo useradd -m -g $GROUPNAME $USERNAME

RUN sudo ls /home

RUN echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
 && chmod 0440 /etc/sudoers.d/$USERNAME

USER $USERNAME
RUN whoami

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/

# Hadoop
ARG HADOOP_VERSION=3.3.6
ARG HADOOP_URL=https://www.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz
ENV HADOOP_HOME=/opt/hadoop

# Still looking jovyan and if the user changed it doesn't work properly, but why?
RUN set -x 
RUN curl -kfsSL https://archive.apache.org/dist/hadoop/common/KEYS -o /tmp/hadoop-KEYS 
RUN gpg --import /tmp/hadoop-KEYS 

RUN sudo mkdir $HADOOP_HOME  
RUN sudo chown $USERNAME:$GROUPNAME -R $HADOOP_HOME 
RUN  curl -kfsSL $HADOOP_URL -o /tmp/hadoop.tar.gz 
RUN  curl -kfsSL $HADOOP_URL.asc -o /tmp/hadoop.tar.gz.asc 
RUN gpg --verify /tmp/hadoop.tar.gz.asc 
RUN tar -xf /tmp/hadoop.tar.gz -C $HADOOP_HOME --strip-components 1 
RUN mkdir $HADOOP_HOME/logs 
RUN rm /tmp/hadoop*

ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV PATH=$HADOOP_HOME/sbin:$HADOOP_HOME/bin:$PATH
ENV LD_LIBRARY_PATH=$HADOOP_HOME/lib/native:$LD_LIBRARY_PATH

# Spark
ARG SPARK_VERSION=3.3.4
ARG SPARK_URL=https://archive.apache.org/dist/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop3.tgz
ENV SPARK_HOME=/opt/spark

RUN set -x \
 && curl -kfsSL https://archive.apache.org/dist/spark/KEYS -o /tmp/spark-KEYS  \
 && gpg --import /tmp/spark-KEYS \
 && sudo mkdir $SPARK_HOME \
 && sudo chown $USERNAME:$GROUPNAME -R $SPARK_HOME \
 && curl -kfsSL $SPARK_URL -o /tmp/spark.tgz \
 && curl -kfsSL $SPARK_URL.asc -o /tmp/spark.tgz.asc \
 && gpg --verify /tmp/spark.tgz.asc \
 && tar -xf /tmp/spark.tgz -C $SPARK_HOME --strip-components 1 \
 && rm /tmp/spark* \
 && curl -kfsSL https://jdbc.postgresql.org/download/postgresql-42.3.2.jar -o $SPARK_HOME/jars/postgresql-42.3.2.jar

ENV PYTHONHASHSEED=1
ENV PYSPARK_PYTHON=python3
ENV SPARK_CONF_DIR=$SPARK_HOME/conf
ENV PATH=$SPARK_HOME/sbin:$SPARK_HOME/bin:$PATH

# Hive
ARG HIVE_VERSION=3.1.3
ARG HIVE_URL=https://archive.apache.org/dist/hive/hive-$HIVE_VERSION/apache-hive-$HIVE_VERSION-bin.tar.gz
ENV HIVE_HOME=/opt/hive

RUN set -x \
 && curl -kfsSL https://archive.apache.org/dist/hive/KEYS -o /tmp/hive-KEYS  \
 && gpg --import /tmp/hive-KEYS \
 && sudo mkdir $HIVE_HOME \
 && sudo chown $USERNAME:$GROUPNAME -R $HIVE_HOME \
 && curl -kfsSL $HIVE_URL -o /tmp/hive.tar.gz \
 && curl -kfsSL $HIVE_URL.asc -o /tmp/hive.tar.gz.asc \
 && gpg --verify /tmp/hive.tar.gz.asc \
 && tar -xf /tmp/hive.tar.gz -C $HIVE_HOME --strip-components 1 \
 && rm /tmp/hive*

#RUN sudo rm $HIVE_HOME/lib/guava-*.jar \
# && sudo cp $HADOOP_HOME/share/hadoop/hdfs/lib/guava-*.jar $HIVE_HOME/lib/
#RUN mv /opt/hive/lib/log4j-slf4j-impl-2.6.2.jar /opt/hive/lib/log4j-slf4j-impl-2.6.2.jar.bak

ENV HIVE_CONF_DIR=$HIVE_HOME/conf
ENV PATH=$HIVE_HOME/sbin:$HIVE_HOME/bin:$PATH

# Config
COPY --chown=$USERNAME:$GROUPNAME conf/core-site.xml $HADOOP_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/hdfs-site.xml $HADOOP_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/yarn-site.xml $HADOOP_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/mapred-site.xml $HADOOP_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/workers $HADOOP_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/spark-defaults.conf $SPARK_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/log4j.properties $SPARK_CONF_DIR/
COPY --chown=$USERNAME:$GROUPNAME conf/hive-site.xml $HIVE_CONF_DIR/

RUN ln $HADOOP_CONF_DIR/workers $SPARK_CONF_DIR/ \
 && ln $HIVE_CONF_DIR/hive-site.xml $SPARK_CONF_DIR/

# Entry point
COPY entrypoint.sh /usr/local/sbin/entrypoint.sh
RUN sudo ls /usr/local/sbin/
RUN sudo chmod a+x /usr/local/sbin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
