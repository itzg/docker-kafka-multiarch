
FROM eclipse-temurin:17-jdk as builder

ARG KAFKA_VERSION=3.1.0
ADD https://dlcdn.apache.org/kafka/${KAFKA_VERSION}/kafka_2.13-${KAFKA_VERSION}.tgz /packages/

RUN tar -C /opt -xf /packages/kafka_2.13-${KAFKA_VERSION}.tgz

RUN mv /opt/kafka_2.13-${KAFKA_VERSION} /opt/kafka

FROM eclipse-temurin:17-jdk as main

COPY --from=builder /opt /opt
ENV PATH=$PATH:/opt/kafka/bin

COPY cp-compat /etc/confluent/docker

COPY init /init

VOLUME /var/lib/kafka/data

EXPOSE 9092

WORKDIR /opt/kafka
STOPSIGNAL SIGTERM

CMD ["/init/kafka"]
