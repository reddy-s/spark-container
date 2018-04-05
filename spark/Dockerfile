FROM centos

RUN yum -y update && \
    yum -y install java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4 && \
    yum -y install wget && \
    yum -y install python && \
    mkdir /home/lib

WORKDIR /home/lib

RUN wget https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    pip install py4j==0.10.4 && \
    pip install pandas && \
    pip install requests && \
    pip install datetime

ENV JAVA_HOME /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.151-1.b12.el7_4.x86_64/jre
ENV SCALA_VERSION 2.12.4
ENV SPARK_VERSION 2.2.1
ENV HADOOP_VERSION 2.7

RUN echo "cloning from 'https://downloads.lightbend.com/scala/$SCALA_VERSION/scala-$SCALA_VERSION.tgz'" && \
    wget https://downloads.lightbend.com/scala/$SCALA_VERSION/scala-$SCALA_VERSION.tgz && \
    tar -xvf scala-$SCALA_VERSION.tgz && \
    rm -rf scala-$SCALA_VERSION.tgz && \
    echo "cloning from 'http://apache.mirror.anlx.net/spark/spark-$SPARK_VERION/spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION.tgz'" && \
    wget http://apache.mirror.anlx.net/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION.tgz && \
    tar -xvf spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION.tgz && \
    rm -rf spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION.tgz && \
    mkdir /home/sparkuser

ENV SCALA_HOME /home/lib/scala-$SCALA_VERSION
ENV SPARK_HOME /home/lib/spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION
ENV PATH $PATH:$JAVA_HOME/bin:$SCALA_HOME/bin
ENV PYTHONPATH /home/lib/spark-$SPARK_VERSION-bin-hadoop$HADOOP_VERSION/python

WORKDIR $SPARK_HOME

CMD ["/home/lib/spark-2.2.1-bin-hadoop2.7/bin/spark-class", "org.apache.spark.deploy.worker.Worker $MASTER_PORT_7077_TCP_ADDR:$MASTER_PORT_7077_TCP_PORT"]
# CMD ["/home/lib/spark-2.2.1-bin-hadoop2.7/bin/spark-class", "org.apache.spark.deploy.master.Master"]