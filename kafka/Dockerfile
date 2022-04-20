FROM eclipse-temurin:17-jdk as builder

RUN DEBIAN_FRONTEND=noninteractive \
    apt-get install -y \
      curl \
    && apt-get clean

ARG UPSTREAM_VERSION=3.1.0
RUN curl -fsSL -o /tmp/kafka.tgz https://dlcdn.apache.org/kafka/${UPSTREAM_VERSION}/kafka_2.13-${UPSTREAM_VERSION}.tgz \
    && tar -C /opt -xf /tmp/kafka.tgz \
    && mv /opt/kafka_2.13-${UPSTREAM_VERSION} /opt/kafka

FROM eclipse-temurin:17-jdk as main

COPY --from=builder /opt /opt
ENV PATH=$PATH:/opt/kafka/bin
RUN for s in /opt/kafka/bin/*.sh; do ln -s "$s" "${s%.sh}"; done

COPY cp-compat /etc/confluent/docker

COPY init /init

VOLUME /var/lib/kafka/data

EXPOSE 9092

WORKDIR /opt/kafka
STOPSIGNAL SIGTERM

HEALTHCHECK --start-period=5s --interval=10s --retries=3 CMD /init/healthcheck

CMD ["/init/kafka"]
