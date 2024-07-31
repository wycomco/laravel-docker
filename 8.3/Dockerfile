FROM php:8.3-alpine3.17

# Add docker-php-extension-installer script
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# Install dependencies
RUN apk add --no-cache \
    bash \
    ca-certificates \
    curl \
    dirmngr \
    freetype-dev \
    g++ \
    gcc \
    gpg \
    gpg-agent \
    gcompat \
    git \
    icu-dev \
    icu-libs \
    libc-dev \
    libzip-dev \
    libldap \
    make \
    mysql-client \
    nodejs \
    npm \
    oniguruma-dev \
    yarn \
    openssh-client \
    openldap-dev \
    rsync \
    unzip \
    wget \
    zlib-dev

# Install Bun and PHP extensions
RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions \
    @composer \
    redis-stable \
    bcmath \
    calendar \
    exif \
    gd \
    intl \
    pdo_mysql \
    pcntl \
    soap \
    ldap \
    zip

# Install Bun
RUN wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub && \
    wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.35-r1/glibc-2.35-r1.apk && \
    apk add --force-overwrite glibc-2.35-r1.apk && \
    rm glibc-2.35-r1.apk && \
    npm install -g bun

# Add local and global vendor bin to PATH.
ENV PATH ./vendor/bin:/composer/vendor/bin:/root/.composer/vendor/bin:/usr/local/bin:$PATH

# Setup working directory
WORKDIR /var/www
