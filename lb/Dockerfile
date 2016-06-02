FROM haproxy

MAINTAINER 3Scale Dev <developer@3scale.com>

# ENV PROXY YOURPROXY:PORT
# ENV https_proxy http://$PROXY
# ENV http_proxy http://$PROXY
# ENV no_proxy "localhost, 127.0.0.1, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16"

ENV APP_1 ""
ENV APP_2 ""


# Add files.
ADD haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg
RUN /usr/local/sbin/haproxy -c -f /usr/local/etc/haproxy/haproxy.cfg

EXPOSE 80
EXPOSE 70
EXPOSE 8080
