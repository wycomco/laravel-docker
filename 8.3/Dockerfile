FROM php:8.3-alpine3.17

ARG BUN_VERSION=latest

# https://github.com/sgerrand/alpine-pkg-glibc/releases
# https://github.com/sgerrand/alpine-pkg-glibc/issues/176
ARG GLIBC_VERSION=2.34-r0

# https://github.com/oven-sh/bun/issues/5545#issuecomment-1722461083
ARG GLIBC_VERSION_AARCH64=2.26-r1

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

RUN arch="$(apk --print-arch)" \
  && echo ${arch} \
  && case "${arch##*-}" in \
  x86_64) build="x64-baseline";; \
  aarch64) build="aarch64";; \
  *) echo "error: unsupported architecture: $arch"; exit 1 ;; \
  esac \
  && version="$BUN_VERSION" \
  && case "$version" in \
  latest | canary | bun-v*) tag="$version"; ;; \
  v*)                       tag="bun-$version"; ;; \
  *)                        tag="bun-v$version"; ;; \
  esac \
  && case "$tag" in \
  latest) release="latest/download"; ;; \
  *)      release="download/$tag"; ;; \
  esac \
  && curl "https://github.com/oven-sh/bun/releases/$release/bun-linux-$build.zip" \
  -fsSLO \
  --compressed \
  --retry 5 \
  || (echo "error: failed to download: $tag" && exit 1) \
  && for key in \
  "F3DCC08A8572C0749B3E18888EAB4D40A7B22B59" \
  ; do \
  gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "$key" \
  || gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "$key" ; \
  done \
  && curl "https://github.com/oven-sh/bun/releases/$release/SHASUMS256.txt.asc" \
  -fsSLO \
  --compressed \
  --retry 5 \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  || (echo "error: failed to verify: $tag" && exit 1) \
  && grep " bun-linux-$build.zip\$" SHASUMS256.txt | sha256sum -c - \
  || (echo "error: failed to verify: $tag" && exit 1) \
  && unzip "bun-linux-$build.zip" \
  && mv "bun-linux-$build/bun" /usr/local/bin/bun \
  && rm -f "bun-linux-$build.zip" SHASUMS256.txt.asc SHASUMS256.txt \
  && chmod +x /usr/local/bin/bun \
  && cd /tmp \
  && case "${arch##*-}" in \
  x86_64) curl "https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-${GLIBC_VERSION}.apk" \
  -fsSLO \
  --compressed \
  --retry 5 \
  || (echo "error: failed to download: glibc v${GLIBC_VERSION}" && exit 1) \
  && mv "glibc-${GLIBC_VERSION}.apk" glibc.apk \
  && curl "https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-bin-${GLIBC_VERSION}.apk" \
  -fsSLO \
  --compressed \
  --retry 5 \
  || (echo "error: failed to download: glibc-bin v${GLIBC_VERSION}" && exit 1) \
  && mv "glibc-bin-${GLIBC_VERSION}.apk" glibc-bin.apk ;; \
  aarch64) curl "https://raw.githubusercontent.com/squishyu/alpine-pkg-glibc-aarch64-bin/master/glibc-${GLIBC_VERSION_AARCH64}.apk" \
  -fsSLO \
  --compressed \
  --retry 5 \
  || (echo "error: failed to download: glibc v${GLIBC_VERSION_AARCH64}" && exit 1) \
  && mv "glibc-${GLIBC_VERSION_AARCH64}.apk" glibc.apk \
  && curl "https://raw.githubusercontent.com/squishyu/alpine-pkg-glibc-aarch64-bin/master/glibc-bin-${GLIBC_VERSION_AARCH64}.apk" \
  -fsSLO \
  --compressed \
  --retry 5 \
  || (echo "error: failed to download: glibc-bin v${GLIBC_VERSION_AARCH64}" && exit 1) \
  && mv "glibc-bin-${GLIBC_VERSION_AARCH64}.apk" glibc-bin.apk ;; \
  *) echo "error: unsupported architecture '$arch'"; exit 1 ;; \
  esac

# Install Bun and PHP extensions
RUN chmod +x /usr/local/bin/install-php-extensions && \
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
