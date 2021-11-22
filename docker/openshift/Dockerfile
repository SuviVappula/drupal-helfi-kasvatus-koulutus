ARG DRUPAL_DOCKER_TAG=8.0
FROM ghcr.io/city-of-helsinki/drupal-docker-base:${DRUPAL_DOCKER_TAG}

COPY / /var/www/html/
WORKDIR /var/www/html
RUN composer install --no-progress --profile --prefer-dist --no-interaction --no-dev --optimize-autoloader
RUN composer dump-autoload --optimize

# Copy deploy script
COPY docker/openshift/entrypoints/20-deploy.sh /entrypoints
RUN chmod +x /entrypoints/20-deploy.sh

# Copy cron scripts
RUN mkdir /crons
COPY docker/openshift/crons/ /crons
RUN chmod +x /crons/*

ENV ELASTIC_APM_SHA256SUM dfcbb89b348621f96f775938b7b60cc809a4bf6147ced1531ebacc20b929bbd0
RUN \
  curl -fSL https://github.com/elastic/apm-agent-php/releases/download/v1.3.1/apm-agent-php_1.3.1_all.apk > apm-agent-php.apk && \
  echo "$ELASTIC_APM_SHA256SUM *apm-agent-php.apk" | sha256sum -c -

RUN apk add --allow-untrusted apm-agent-php.apk

# Copy nginx overrides.
COPY docker/openshift/custom.locations /etc/nginx/conf.d/custom.locations
