ARG CADDY_VERSION=2
ARG PHP_VERSION=8
ARG PHP_EXTENSIONS=
ARG PHP_DEV_EXTENSIONS=
ARG COMPOSER_VERSION=lts
ARG NODE_VERSION=lts
ARG MARIADB_VERSION=11

###############################################################################
### [STAGE] composer
###############################################################################
FROM composer:$COMPOSER_VERSION as composer

###############################################################################
# [STAGE] php-dev
###############################################################################
FROM symfonysail/php-dev:$PHP_VERSION AS php-dev
SHELL ["/bin/bash", "-euxo", "pipefail", "-c"]

ARG PHP_EXTENSIONS
ARG PHP_DEV_EXTENSIONS
ARG NODE_VERSION

# Copy composer binary
COPY --link --from=composer /usr/bin/composer /usr/local/bin/

RUN \
    # Install PHP extensions
    EXTENSIONS=$(echo "$PHP_EXTENSIONS $PHP_DEV_EXTENSIONS" | sed 's/,/ /g; s/[[:space:]]\{2,\}/ /g; s/^[[:space:]]*//g; s/[[:space:]]*$//g;'); \
    if [ -n "$EXTENSIONS" ]; then \
        install-php-extensions $EXTENSIONS; \
    fi; \
    # Install Node.js, update npm and install Yarm
    curl -fsSL https://deb.nodesource.com/setup_"$NODE_VERSION".x | bash -; \
    apt-get install -y --no-install-recommends nodejs; \
    npm update -g npm; \
    npm install -g yarn; \
    # Cleanup
    rm -rf /root/.npm; \
    rm -rf /var/lib/apt/lists/*

###############################################################################
# [STAGE] caddy-dev
###############################################################################
FROM symfonysail/caddy-base:$CADDY_VERSION as caddy-dev
SHELL ["/bin/ash", "-euxo", "pipefail", "-c"]

###############################################################################
# [STAGE] mariadb-dev
###############################################################################
FROM symfonysail/mariadb-base:$MARIADB_VERSION as mariadb-dev
SHELL ["/bin/bash", "-euxo", "pipefail", "-c"]
