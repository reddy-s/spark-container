# Spark Cluster in Docker Containers

This project will help you quickly deploy a spark cluster in containers.

## How to build the image?

You can build the image using the `docker build` command. First `cd` into the directory and then run the following command:

```sh
docker build -t reddy-s/spark:1.0 spark
```

> Note: Always make sure you specify a verison as it would help track your live application versions

## How to run the contianers?

The containers can be run using the compose file.

```sh
docker-compose up -d
```

> Note: the `-d` notation will run the containers in background

This would start the following:

* Spark Master
```yaml
master:
    image: reddy-s/spark:1.0
    command: /bin/bash -c "/home/lib/spark-2.2.1-bin-hadoop2.7/bin/spark-class org.apache.spark.deploy.master.Master"
    environment:
      - MASTER_PORT_7077_TCP_ADDR=172.18.0.2
      - MASTER_PORT_7077_TCP_PORT=7077
    volumes:
        - type: volume
        source: cluster-data
        target: /home/data
    ports:
        - "8080:8080"
        - "7077:7077"
```

* Spark Worker
```yaml 
slave:
    image: reddy-s/spark:1.0
    command: /bin/bash -c "/home/lib/spark-2.2.1-bin-hadoop2.7/bin/spark-class org.apache.spark.deploy.worker.Worker 172.18.0.2:7077"
    environment:
      - MASTER_PORT_7077_TCP_ADDR=172.18.0.2
      - MASTER_PORT_7077_TCP_PORT=7077
    volumes:
      - type: volume
        source: cluster-data
        target: /home/data
    depends_on:
      - master
    links:
      - master
```

* A Postgres database to read and persist data if needed
```yaml
db:
    image: manoj10/postgres
    ports:
      - "5431:5430"
```

## How to run your spark jobs?

You can find a sample [Spark Job](https://gist.github.com/reddy-s/cbfc8d527dc8ec9c6b5e1d988fcbde3d) (no where close to a production one) for you to test the cluster.

You can interact with any of the workers by running the following command
```sh
docker exec -it slave_1 /bin/bash
```
Run your spark job with the following command
```sh
./bin/spark-submit \
    --master spark://$MASTER_PORT_7077_TCP_ADDR:$MASTER_PORT_7077_TCP_PORT \
    /home/data/sparkjob.py
```
