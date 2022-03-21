
FROM eclipse-temurin:17-jdk as builder

RUN DEBIAN_FRONTEND=noninteractive \
    apt-get install -y \
      curl \
    && apt-get clean

ARG ZK_VERSION=3.8.0
RUN curl  -fsSL -o /tmp/zookeeper.tgz https://dlcdn.apache.org/zookeeper/zookeeper-${ZK_VERSION}/apache-zookeeper-${ZK_VERSION}-bin.tar.gz \
    && tar -C /opt -xf /tmp/zookeeper.tgz \
    && mv /opt/apache-zookeeper-${ZK_VERSION}-bin /opt/zookeeper

FROM eclipse-temurin:17-jdk as main

COPY --from=builder /opt /opt
ENV ZOOBINDIR=/opt/zookeeper/bin
ENV PATH=$PATH:$ZOOBINDIR

COPY init /init

VOLUME /var/lib/zookeeper/data
EXPOSE 2181
STOPSIGNAL SIGTERM

WORKDIR /opt/zookeeper

HEALTHCHECK --start-period=5s --interval=10s --retries=3 CMD /init/healthcheck

CMD ["/init/zookeeper"]
