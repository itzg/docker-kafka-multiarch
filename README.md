
# Apache Zookeeper and Kafka Docker Images

## Zookeeper

This image is primarily intended to support [itzg/kafka](https://hub.docker.com/r/itzg/kafka) until non-zookeeper support is finalized.

### Environment variables

Any of [the configuration parameters](https://zookeeper.apache.org/doc/r3.3.3/zookeeperAdmin.html#sc_configuration) can be set by translating the property name from camelCase to SCREAMING_SNAKE_CASE and prefixing with "ZOOKEEPER_", such as `ZOOKEEPER_CLIENT_PORT`.

### Ports

- `2181`

### Volumes

- `/var/lib/zookeeper/data`

## Kafka

### Environment variables

Any of [the broker configuration properties](https://kafka.apache.org/documentation/#brokerconfigs) can be set by translating the property name from dot-delimited to SCREAMING_SNAKE_CASE with a prefix of `KAFKA_`.

The following variables are common ones to change:

- `KAFKA_ZOOKEEPER_CONNECT` = zk:2181
- `KAFKA_BROKER_ID` = 0
- `KAFKA_ADVERTISED_LISTENERS` = "EXTERNAL://localhost:9092,INTERNAL://${HOSTNAME}:9093"

> NOTE: the protocol mappings `INTERNAL` and `EXTERNAL` are pre-declared with the `INTERNAL` identifier is used for inter-broker communication. The listener configuration is also pre-declared as "EXTERNAL://0.0.0.0:9092,INTERNAL://0.0.0.0:9093"

### Ports

- `9092`

### Volumes

- `/var/lib/kafka/data`

## Example composition

```yaml
version: "3"

services:
  zk:
    image: itzg/zookeeper
    volumes:
      - zk:/var/lib/zookeeper/data
  kafka-0:
    depends_on:
      - zk
    image: itzg/kafka
    environment:
      KAFKA_BROKER_ID: "0"
      KAFKA_ADVERTISED_LISTENERS: "EXTERNAL://localhost:9092,INTERNAL://kafka-0:9093"
    ports:
      - "9092:9092"
    volumes:
      - kafka-0:/var/lib/kafka/data

volumes:
  zk: {}
  kafka-0: {}
```