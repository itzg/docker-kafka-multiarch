
FROM eclipse-temurin:17-jdk as builder

ARG ZK_VERSION=3.8.0
ADD https://dlcdn.apache.org/zookeeper/zookeeper-${ZK_VERSION}/apache-zookeeper-${ZK_VERSION}-bin.tar.gz /packages/

RUN tar -C /opt -xf /packages/apache-zookeeper-${ZK_VERSION}-bin.tar.gz

RUN mv /opt/apache-zookeeper-${ZK_VERSION}-bin /opt/zookeeper

FROM eclipse-temurin:17-jdk as main

COPY --from=builder /opt /opt
ENV ZOOBINDIR=/opt/zookeeper/bin
ENV PATH=$PATH:$ZOOBINDIR

COPY init /init

VOLUME /var/lib/zookeeper/data
EXPOSE 2181
STOPSIGNAL SIGTERM

WORKDIR /opt/zookeeper

ENTRYPOINT ["/init/zookeeper"]
