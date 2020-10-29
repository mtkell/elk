FROM ubuntu

RUN apt-get update && apt-get upgrade -y

# Installing and setting Java
RUN cd /opt && curl --retry 8 -s -L -O https://download.java.net/java/GA/jdk15.0.1/51f4f36ad4ef43e39d0dfdbaf6549e32/9/GPL/openjdk-15.0.1_linux-aarch64_bin.tar.gz && cd -
RUN mkdir /opt/jdk-15.0.1 && chmod 0775 /opt/jdk-15.0.1 && tar zxf /opt/openjdk-15.0.1_linux-aarch64_bin.tar.gz -C /opt/jdk-15.0.1 --strip-components=1 
RUN update-alternatives --install /usr/bin/java java /opt/jdk-15.0.1/bin/java 1 && \
    update-alternatives --display java
ENV JAVA_HOME /opt/jdk-15.0.1

# Setting the environment
ENV PATH /usr/share/logstash:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/jdk-11.0.5/bin:\$PATH
RUN groupadd -g 1000 logstash && \
    adduser -u 1000 -g 1000 -d /usr/share/logstash logstash
WORKDIR /usr/share/logstash
RUN chown -R 1000:0 /opt/jdk-15.0.1
RUN java -version


# Downloading and Installing Logstash (TAR)
RUN cd /opt && curl --retry 8 -s -L -O https://artifacts.elastic.co/downloads/logstash/logstash-oss-7.9.3.tar.gz && cd -
RUN tar zxf /opt/logstash-oss-7.9.3.tar.gz --strip-components=1

# Configuring Logstash
RUN mkdir -p config data fix
RUN chmod 0775 config data fix
COPY config/logstash.yml config/log4j2.properties config/
RUN chmod 0660 config/logstash.yml config/log4j2.properties
RUN chmod 0775 /usr/share/logstash && \
    chgrp 0 /usr/share/logstash
# Replace OpenJDK's built-in CA certificate keystore with the one from the OS
# vendor. The latter is superior in several ways.
# REF: https://github.com/elastic/logstash-docker/issues/171
RUN ln -sf /etc/pki/ca-trust/extracted/java/cacerts /opt/jdk-15.0.1/lib/security/cacerts

ENV ELASTIC_CONTAINER true

EXPOSE 5044/tcp
EXPOSE 9600/tcp
EXPOSE 1025/tcp

# FIX Logstash issue 10755
# see: https://github.com/elastic/logstash/issues/10755
#COPY --chown=1000:0 fix/fix-issue-10755.sh fix
#RUN chgrp 0 /usr/share/logstash/fix/fix-issue-10755.sh && \
#    chmod g=u /etc/passwd && \
#    chmod 0775 /usr/share/logstash/fix/fix-issue-10755.sh
#RUN /usr/share/logstash/fix/fix-issue-10755.sh

CMD [ "/usr/share/logstash/bin/logstash","-f", "/usr/share/logstash/config/logstash.conf" ]