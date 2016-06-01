FROM ruby:2.2.0
MAINTAINER Vinicius Mucuge <viniciusmucuge@gmail.com>

ENV APP hello-world
ENV VERSION v0.2
ENV REDIS_HOST redis
ENV REDIS_PORT 6379
ENV LOAD_BALANCER lb
ENV APP_DIR /opt/3scale/hello-app

ENV PROXY 10.99.164.219:3128
ENV https_proxy http://$PROXY
ENV http_proxy http://$PROXY
ENV no_proxy "localhost, 127.0.0.1, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16"

RUN apt-get update && apt-get install -y curl \
                                         git  \
                                         ruby \
                                         ruby-dev \
                                         build-essential \
                                         nodejs \
                                         rubygems

RUN mkdir -p $APP_DIR
WORKDIR $APP_DIR
ADD Gemfile $APP_DIR/Gemfile
ADD Gemfile.lock $APP_DIR/Gemfile.lock
RUN bundle install
ADD hello-world.rb $APP_DIR/

EXPOSE 4567

ENTRYPOINT [ "ruby", "/opt/3scale/hello-app/hello-world.rb" ]
