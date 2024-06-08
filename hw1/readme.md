# Запускаем Kafka
<ol>
<li> Устанавливаем kafka и zookeeper через docker-compose</li>

~~~
version: '3.9'
name: kafka-homework
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.3
    container_name: zookeeper
    environment:
      ZOOKEEPER_SERVER_ID: 1
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
  kafka-1:
    image: confluentinc/cp-kafka:7.4.3
    container_name: kafka-1
    ports:
      - "9092:9092"
      - "19092:19092"
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092,PLAINTEXT_INTERNAL://broker:19092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_INTERNAL:PLAINTEXT
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "true"
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
~~~

~~~
docker-compose create
~~~
<li> Запускаем контейнеры и сервисы</li>

~~~
docker-compose up
~~~

![docker](/imgs/docker.png)
    

<li> Создаём тему</li>

Запускаем консоль bash контейнера kafka-1

~~~
   > docker exec -it kafka-1 bash
~~~

Создаем топик
~~~
   $ cd $KAFKA_HOME/bin
   $ kafka-topics --create --topic quickstart --bootstrap-server localhost:9092
   Created topic quickstart.
   $ kafka-topics --describe --topic quickstart --bootstrap-server localhost:9092
   Topic: quickstart       TopicId: MFDPqZTSQbKDSrDpFxindA PartitionCount: 1       ReplicationFactor: 1    Configs:
        Topic: quickstart       Partition: 0    Leader: 1       Replicas: 1     Isr: 1   
~~~
![docker](/imgs/docker_create.png)

<li> Запишем что-нибудь в тему</li>

~~~   
   $ kafka-console-producer --topic quickstart --bootstrap-server localhost:9092
   This is my first event
   This is my second event
~~~

![docker](/imgs/docker_send.png)


<li> Прочитаем записи</li>

~~~
   $ kafka-console-consumer --topic quickstart --from-beginning --bootstrap-server localhost:9092 --from-beginning
   This is my first event
   This is my second event
~~~
![docker](/imgs/docker_recive.png)

</ol>