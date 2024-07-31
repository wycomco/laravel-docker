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
    zlib-dev

# Install Bun and PHP extensions
RUN curl "https://github.com/oven-sh/bun/releases/latest/download/bun-linux-x64-baseline.zip" \
    -fsSLO \
    --compressed \
    --retry 5 \
    && gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "F3DCC08A8572C0749B3E18888EAB4D40A7B22B59" \
    || gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "F3DCC08A8572C0749B3E18888EAB4D40A7B22B59" \
    && curl "https://github.com/oven-sh/bun/releases/latest/download/SHASUMS256.txt.asc" \
    -fsSLO \
    --compressed \
    --retry 5 && \
    gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
    || (echo "error: failed to verify: latest" && exit 1) && \
    grep " bun-linux-x64-baseline.zip\$" SHASUMS256.txt | sha256sum -c - \
    || (echo "error: failed to verify: latest" && exit 1) && \
    unzip "bun-linux-x64-baseline.zip" && \
    mv "bun-linux-x64-baseline/bun" /usr/local/bin/bun && \
    rm -f "bun-linux-x64-baseline.zip" SHASUMS256.txt.asc SHASUMS256.txt && \
    chmod +x /usr/local/bin/bun && \
    chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions \
    @composer \
    redis-stable \
    xdebug-beta \
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


# Disable the runtime transpiler cache by default inside Docker containers.
# On ephemeral containers, the cache is not useful
ARG BUN_RUNTIME_TRANSPILER_CACHE_PATH=0
ENV BUN_RUNTIME_TRANSPILER_CACHE_PATH=${BUN_RUNTIME_TRANSPILER_CACHE_PATH}

# Add local and global vendor bin to PATH.
ENV PATH ./vendor/bin:/composer/vendor/bin:/root/.composer/vendor/bin:/usr/local/bin:$PATH

# Setup working directory
WORKDIR /var/www
