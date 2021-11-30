ARG JAVA_VERSION=11
ARG BASE_IMAGE=public.ecr.aws/ews-network/amazoncorretto:$JAVA_VERSION

FROM $BASE_IMAGE as repobuiler
ADD https://packages.confluent.io/rpm/7.0/confluent.repo /etc/yum.repos.d/confluent.repo
RUN sed "4 i gpgkey=https://packages.confluent.io/rpm/7.0/archive.key" /etc/yum.repos.d/confluent.repo -i
RUN sed "5 i gpgcheck=1" /etc/yum.repos.d/confluent.repo -i
RUN sed "s/\$releasever//g" /etc/yum.repos.d/confluent.repo -i

FROM $BASE_IMAGE
COPY --from=repobuiler /etc/yum.repos.d/confluent.repo /etc/yum.repos.d/confluent.repo
RUN rpm --import https://packages.confluent.io/rpm/7.0/archive.key && \
    yum upgrade -y --exclude=*devel && \
    yum install curl which install shadow-utils -y && \
    yum install confluent-ksqldb -y && \
    yum clean all ; rm -rfv /var/cache/yum

COPY --chown=999:999 ksqldb/ksql-server.properties /etc/ksqldb/ksql-server.properties
RUN mkdir -p /var/ksql/statestore; chown 999:999 -R /var/ksql/statestore
USER cp-ksql
ENV LOG_DIR=/var/log/confluent/ksql
ENV KSQL_CONFIG_DIR=/etc/ksqldb
ENV KSQL_LOG4J_OPTS="-Dlog4j.configuration=file:$KSQL_CONFIG_DIR/log4j.properties"
VOLUME ["/var/ksql/statestore"]
ENTRYPOINT ["/usr/bin/ksql-server-start"]
CMD ["/etc/ksqldb/ksql-server.properties"]
