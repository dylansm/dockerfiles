FROM zcalusic/debian-stretch as builder
MAINTAINER Zlatko Čalušić <zcalusic@bitsync.net>

ARG BAMBOO_VERSION

ENV JAVA_HOME /usr/java
ENV BAMBOO_VERSION $BAMBOO_VERSION
ENV BAMBOO_INSTALL_DIR /opt/atlassian/bamboo

RUN mkdir "$JAVA_HOME" \
    && wget --dot-style=mega -O- --header "Cookie: oraclelicense=a" "http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/server-jre-8u151-linux-x64.tar.gz" | tar xz --strip-components=1 -C "$JAVA_HOME" \
    && mkdir -p "$BAMBOO_INSTALL_DIR" \
    && wget --dot-style=mega -O- "https://downloads.atlassian.com/software/bamboo/downloads/atlassian-bamboo-${BAMBOO_VERSION}.tar.gz" | tar xz --strip-components=1 -C "$BAMBOO_INSTALL_DIR" \
    && sed -i -e 's/port="8085"/port="8085" secure="${catalinaConnectorSecure}" scheme="${catalinaConnectorScheme}" proxyName="${catalinaConnectorProxyName}" proxyPort="${catalinaConnectorProxyPort}"/' "$BAMBOO_INSTALL_DIR/conf/server.xml"

FROM zcalusic/debian-stretch
MAINTAINER Zlatko Čalušić <zcalusic@bitsync.net>

ARG URL
ARG VCS_URL
ARG VCS_REF
ARG BUILD_DATE
ARG BAMBOO_VERSION

LABEL org.label-schema.name="Atlassian Bamboo in Docker" \
      org.label-schema.description="Run Atlassian Bamboo CI, deployment and release management software in a Docker container" \
      org.label-schema.vendor="Zlatko Čalušić" \
      org.label-schema.license="MIT" \
      org.label-schema.url=$URL \
      org.label-schema.vcs-url=$VCS_URL \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.schema-version="1.0"

ENV BAMBOO_VERSION $BAMBOO_VERSION
ENV BAMBOO_INSTALL_DIR /opt/atlassian/bamboo
ENV BAMBOO_HOME /var/atlassian/application-data/bamboo
ENV BAMBOO_USER daemon
ENV BAMBOO_GROUP daemon
ENV JAVA_HOME /usr/java
ENV PATH $JAVA_HOME/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

COPY --from=builder $JAVA_HOME $JAVA_HOME
COPY --from=builder $BAMBOO_INSTALL_DIR $BAMBOO_INSTALL_DIR

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       git \
       openssh-client \
    && rm -rf /var/lib/apt/lists/* \
    && chown "$BAMBOO_USER:$BAMBOO_GROUP" "$BAMBOO_INSTALL_DIR/conf" "$BAMBOO_INSTALL_DIR/logs" "$BAMBOO_INSTALL_DIR/temp" "$BAMBOO_INSTALL_DIR/work"

EXPOSE 8085

COPY entrypoint.sh /entrypoint.sh

CMD [ "/entrypoint.sh" ]
