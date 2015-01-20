FROM ubuntu:14.04
MAINTAINER Ho Yan Leung <hy.leung@gmail.com>
ENV REFRESHED_AT 2014-12-28
ENV CONTRIB_VERSION 1.4.2

RUN apt-get -yqq update
RUN apt-get -yqq install wget
RUN wget -O - http://packages.elasticsearch.org/GPG-KEY-elasticsearch | apt-key add -
RUN echo 'deb http://packages.elasticsearch.org/logstash/1.4/debian stable main' > /etc/apt/sources.list.d/logstash.list

RUN apt-get -yqq update
RUN apt-get -yqq install logstash

WORKDIR /tmp

#install the contrib package
RUN wget --quiet http://download.elasticsearch.org/logstash/logstash/logstash-contrib-$CONTRIB_VERSION.tar.gz
RUN tar zxf logstash-contrib-$CONTRIB_VERSION.tar.gz
RUN cp -a /tmp/logstash-contrib-$CONTRIB_VERSION/. /opt/logstash

#clean up
RUN rm /tmp/logstash-contrib-$CONTRIB_VERSION.tar.gz
RUN rm -rf /tmp/logstash-contrib-$CONTRIB_VERSION

WORKDIR /opt/logstash

ENTRYPOINT ["bin/logstash"]
CMD ["--config=/etc/conf/logstash.conf"]
