ARG JAVA_VERSION=17
ARG BASE_IMAGE=public.ecr.aws/ews-network/amazoncorretto:$JAVA_VERSION

FROM $BASE_IMAGE as repobuiler
ADD https://packages.confluent.io/rpm/7.0/confluent.repo /etc/yum.repos.d/confluent.repo
RUN sed "4 i gpgkey=https://packages.confluent.io/rpm/7.0/archive.key" /etc/yum.repos.d/confluent.repo -i
RUN sed "5 i gpgcheck=1" /etc/yum.repos.d/confluent.repo -i
RUN sed "s/\$releasever//g" /etc/yum.repos.d/confluent.repo -i

FROM $BASE_IMAGE
COPY --from=repobuiler /etc/yum.repos.d/confluent.repo /etc/yum.repos.d/confluent.repo/
RUN rpm --import https://packages.confluent.io/rpm/7.0/archive.key && \
    yum upgrade -y --security && \
    yum install curl which shadow-utils -y && \
    yum install confluent-schema-registry confluent-schema-registry-plugins -y && \
    yum clean all ; rm -rfv /var/cache/yum

COPY --chown=999:999 schema-registry/schema-registry.properties /etc/schema-registry/schema-registry.properties
USER cp-schema-registry
ENV LOG_DIR=/var/log/confluent/schema-registry
ENTRYPOINT ["/usr/bin/schema-registry-start"]
CMD ["/etc/schema-registry/schema-registry.properties"]
