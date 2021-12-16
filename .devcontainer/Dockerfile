ARG VARIANT="8.1-apache-bullseye"

FROM mcr.microsoft.com/vscode/devcontainers/php:0-${VARIANT}

COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin

# [Choice] Node.js version: none, lts/*, 16, 14, 12, 10
ARG NODE_VERSION="none"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php && php -r "unlink('composer-setup.php');" \
    mv composer.phar /bin/composer

ARG CASSANDRA_LIB_VERSION="2.16.2"
RUN apt update -y  && apt upgrade -y \
    && apt install python3 pip cmake unzip mlocate build-essential git libuv1-dev libssl-dev libgmp-dev openssl zlib1g-dev libpcre3-dev -y \
    && git clone --recursive https://github.com/datastax/cpp-driver /cpp-driver \
    && cd /cpp-driver \
    && git fetch --tags \
    && git checkout "tags/${CASSANDRA_LIB_VERSION}" -b "v${CASSANDRA_LIB_VERSION}" \
    && mkdir -p /cpp-driver/build \
    && cd build \
    && cmake \
        -DCMAKE_CXX_FLAGS="-fPIC" \
        -DCASS_BUILD_STATIC=OFF \
        -DCASS_BUILD_SHARED=ON \
        -DCMAKE_BUILD_TYPE=RELEASE \
        -DCMAKE_INSTALL_LIBDIR:PATH=lib \
        -DCASS_USE_ZLIB=ON .. \
    && make -j8 && make install \
    && install-php-extensions intl zip pcntl gmp ast xdebug \
    && pip install ccm

RUN apt update -y && apt install clang clang-format libclang-dev -y

# RUN
