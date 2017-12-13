FROM wodby/drupal-nginx:8-1.13-3.0.0
MAINTAINER OVH-UX <github@ovh.net>

ARG DOCKER_USER=1000
ARG DOCKER_USER_GROUP=1001
ARG CERT_COUNTRY=FR
ARG CERT_STATE=Nord
ARG CERT_CITY=Roubaix
ARG CERT_FQDN=gw2sdev-docker.ovh.net

# Switch to root
# @see https://github.com/wodby/php/commit/d4c0b703310471b3555a360e343f6d7551162d37
USER root

# Override permissions with user/group of the host
RUN echo http://dl.alpinelinux.org/alpine/edge/community/ >> /etc/apk/repositories
RUN apk --no-cache add shadow && usermod -u $DOCKER_USER www-data && groupmod -g $DOCKER_USER_GROUP www-data

## Change files with old uid/gid
RUN find / -group 82 -exec chgrp -h $DOCKER_USER_GROUP {} \; && find / -user 82 -exec chown -h $DOCKER_USER {} \;

# Install OpenSSL
RUN apk --no-cache add openssl

# Generate self-signed certificate
RUN openssl req \
  -x509 -nodes -days 365 -sha256 \
  -subj "/C=${CERT_COUNTRY}/ST=${CERT_STATE}/L=${CERT_CITY}/CN=${CERT_FQDN}" \
  -newkey rsa:2048 -keyout /etc/ssl/certs/nginx.key -out /etc/ssl/certs/nginx.crt

# Re-switch to www-data
# @see https://github.com/wodby/php/commit/d4c0b703310471b3555a360e343f6d7551162d37

USER www-data

# Add configuration for SSL
COPY server.conf.tpl /etc/gotpl/
COPY 30-drupal-nginx-ssl.sh /docker-entrypoint-init.d/
