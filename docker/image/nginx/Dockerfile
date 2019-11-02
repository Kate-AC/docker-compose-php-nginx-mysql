FROM alpine:3.9
MAINTAINER Kate-AC

RUN apk --update add \
  nginx \
  && rm -rf /var/cache/apk/*

RUN mkdir -p /run/nginx
RUN mkdir -p /project/Hoge

WORKDIR /project/Hoge

COPY ./docker/config/nginx/default.conf /etc/nginx/conf.d/default.conf

CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
