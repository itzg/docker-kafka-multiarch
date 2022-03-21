These images are intended to be configuration-compatible with the [confluentinc/cp-zookeeper](https://hub.docker.com/r/confluentinc/cp-zookeeper) and [confluentinc/cp-kafka](https://hub.docker.com/r/confluentinc/cp-kafka) images; however, unlike those images, these are built as multi-architecture images for both amd64 and arm64 in order to be compatible with Mac OS on M1 Apple Silicon and the usual x86 hardware.

## Zookeeper

[![Docker Image Version (latest semver)](https://img.shields.io/docker/v/itzg/zookeeper?label=Image:%20itzg/zookeeper)](https://hub.docker.com/r/itzg/zookeeper)

This image is primarily intended to support [itzg/kafka](https://hub.docker.com/r/itzg/kafka) until non-zookeeper support is finalized.

### Environment variables

Any of [the configuration parameters](https://zookeeper.apache.org/doc/r3.3.3/zookeeperAdmin.html#sc_configuration) can be set by translating the property name from camelCase to SCREAMING_SNAKE_CASE and prefixing with "ZOOKEEPER_", such as `ZOOKEEPER_CLIENT_PORT`.

The following variables are pre-declared:

- `ZOOKEEPER_CLIENT_PORT` = 2181
- `ZOOKEEPER_DATA_DIR` = /var/lib/zookeeper/data
- `ZOOKEEPER_TICK_TIME` = 1000
- `SERVER_JVMFLAGS` = -Xmx256m

### Ports

- `2181`

### Volumes

- `/var/lib/zookeeper/data`

## Kafka

[![Docker Image Version (latest semver)](https://img.shields.io/docker/v/itzg/kafka?label=Image:%20itzg/kafka)](https://hub.docker.com/r/itzg/kafka)

### Environment variables

Any of [the broker configuration properties](https://kafka.apache.org/documentation/#brokerconfigs) can be set by translating the property name from dot-delimited to SCREAMING_SNAKE_CASE with a prefix of `KAFKA_`.

The following variables are pre-declared:

- `KAFKA_ZOOKEEPER_CONNECT` = zk:2181
- `KAFKA_BROKER_ID` = 0
- `KAFKA_LOG_DIRS` = /var/lib/kafka/data
- `KAFKA_ADVERTISED_LISTENERS` = PLAINTEXT://localhost:9092,BROKER://${HOSTNAME}:9093
- `KAFKA_LISTENERS` = PLAINTEXT://0.0.0.0:9092,BROKER://0.0.0.0:9093
- `KAFKA_LISTENER_SECURITY_PROTOCOL_MAP` = BROKER:PLAINTEXT,PLAINTEXT:PLAINTEXT
- `KAFKA_INTER_BROKER_LISTENER_NAME` = BROKER
- `KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR` = 1
- `KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR` = 1
- `BROKER_HEAP_OPTS` = -Xmx1G

> NOTE: variables referenced in the Apache wrapper scripts are not converted into broker properties.

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
      KAFKA_ADVERTISED_LISTENERS: "PLAINTEXT://localhost:9092,BROKER://kafka-0:9093"
    ports:
      - "9092:9092"
    volumes:
      - kafka-0:/var/lib/kafka/data

volumes:
  zk: {}
  kafka-0: {}
```