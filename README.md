# Replicator security configuration demos

## Purpose 

This repository provides docker-compose environments representing various Replicator security configurations. 

## Non replicator components

All environments contain:

* A 3 node source cluster containing srcKafka1, srcKafka2 and srcKafka3
* A 3 node destination cluster containing destKafka1, destKafka2, destKafka3
* A single Connect worker that will run replicator
* A source client container (srcKafkaClient) that creates a source topic and provides test data
* A destination client container (destKafkaClient) that installs Replicator

In all containers, minimal non-replicator configuration is supplied.

## Test Data

The data from testData/testData.txt is loaded into the source topic, this can be modified to suit testing requirements.

## Creating and verifying the environments

In all environments no actions are taken on the destination brokers. Because of this we can verify the environment by consuming from the test topic on the destination cluster.

* Unsecured

  This can be started with:
  ```
  docker-compose -f docker-compose_unsecure.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  kafka-console-consumer --bootstrap-server localhost:11091 --topic testTopic --from-beginning
  ```

* Source Cluster with SSL encryption

  This can be started with:
  ```
  docker-compose -f docker-compose_source_ssl_encryption.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  kafka-console-consumer --bootstrap-server localhost:11091 --topic testTopic --from-beginning
  ```
  
* Destination Cluster with SSL encryption

  This can be started with:
  ```
  docker-compose -f docker-compose_dest_ssl_encryption.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  echo ssl.truststore.location=/etc/kafka/secrets/kafka.destKafkaClient.truststore.jks >> consumer.properties
  echo ssl.truststore.password=confluent >> consumer.properties 
  echo ssl.keystore.location=/etc/kafka/secrets/kafka.destKafkaClient.keystore.jks >> consumer.properties
  echo ssl.keystore.password=confluent >> consumer.properties 
  echo ssl.key.password=confluent >> consumer.properties 
  echo security.protocol=ssl >> consumer.properties
  kafka-console-consumer --bootstrap-server localhost:11091 --topic testTopic --consumer.config consumer.properties --from-beginning
  ```

* Source Cluster with SSL authentication

  This can be started with:
  ```
  docker-compose -f docker-compose_source_ssl_auth.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  kafka-console-consumer --bootstrap-server localhost:11091 --topic testTopic --from-beginning
  ```

* Destination Cluster with SSL authentication

  This can be started with:
  ```
  docker-compose -f docker-compose_dest_ssl_auth.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  echo ssl.truststore.location=/etc/kafka/secrets/kafka.destKafkaClient.truststore.jks >> consumer.properties
  echo ssl.truststore.password=confluent >> consumer.properties 
  echo ssl.keystore.location=/etc/kafka/secrets/kafka.destKafkaClient.keystore.jks >> consumer.properties
  echo ssl.keystore.password=confluent >> consumer.properties 
  echo ssl.key.password=confluent >> consumer.properties 
  echo security.protocol=ssl >> consumer.properties
  kafka-console-consumer --bootstrap-server localhost:11091 --topic testTopic --consumer.config consumer.properties --from-beginning
  ```

* Source Cluster with SASL Plain authentication

  This can be started with:
  ```
  docker-compose -f docker-compose_source_sasl_plain_auth.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  kafka-console-consumer --bootstrap-server localhost:11091 --topic testTopic --from-beginning
  ```

* Destination Cluster with SASL Plain authentication

  This can be started with:
  ```
  docker-compose -f docker-compose_source_sasl_plain_auth.yml up -d
  ```
  
  And verified as follows:
  ```
  docker-compose exec destKafka1 bash
  echo security.protocol=SASL_PLAINTEXT > consumer.properties
  echo sasl.mechanism=PLAIN >> consumer.properties
  echo sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username=\"client\" password=\"client-secret\"\; >> consumer.properties
  kafka-console-consumer --bootstrap-server localhost:11091 --consumer.config consumer.properties --topic testTopic --from-beginning
  ```