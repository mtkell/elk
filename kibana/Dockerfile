FROM ubuntu

RUN apt-get update && apt-get upgrade -y

# Setting the environment
ENV PATH /usr/share/kibana:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:\$PATH
RUN addgroup -gid 1000 kibana && \
    adduser -u 1000 -g 1000 -d /usr/share/kibana kibana
WORKDIR /usr/share/kibana


# Downloading and Installing Kibana (TAR)
RUN cd /opt && curl --retry 8 -s -L -O https://artifacts.elastic.co/downloads/kibana/kibana-oss-7.9.3-linux-arm64.tar.gz && cd -
RUN tar zxf /opt/kibana-oss-7.5.1-linux-arm64.tar.gz --strip-components=1

# Configuring Kibana
RUN mkdir -p config
RUN chmod 0775 config
COPY config/kibana.yml config/
RUN chmod 0660 config/kibana.yml                    
RUN chmod 0775 /usr/share/kibana && \
    chgrp 0 /usr/share/kibana

ENV ELASTIC_CONTAINER true

EXPOSE 5601/tcp

CMD [ "/usr/share/kibana/bin/kibana" , "--allow-root" ]