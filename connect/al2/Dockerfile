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
    yum upgrade -y --security && \
    yum install curl which -y shadow-utils -y && \
    yum install confluent-server confluent-hub-client -y && \
    yum clean all ; rm -rfv /var/cache/yum

RUN mkdir "/var/log/kafka"; chown -R 994:999 /var/log/kafka

COPY --chown=994:999 kafka-connect/connect-distributed.properties /etc/
USER cp-kafka-connect
ENTRYPOINT ["/usr/bin/connect-distributed"]
CMD ["/etc/kafka/connect-distributed.properties"]
