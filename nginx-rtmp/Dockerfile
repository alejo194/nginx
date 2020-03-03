FROM debian:jessie
# Versions of Nginx and nginx-rtmp-module to use
ENV NGINX_VERSION nginx-1.17.8
ENV NGINX_RTMP_MODULE_VERSION 1.2.1
ENV FFMPET_VERSION 4.2.1

# Install dependencies
RUN apt-get update && \
    apt-get install -y ca-certificates libpcre3 libpcre3-dev zlib1g-dev openssl libssl-dev wget gcc make bzip2 && \
    rm -rf /var/lib/apt/lists/*

# Download and decompress Nginx
RUN mkdir -p /tmp/build/nginx && \
    cd /tmp/build/nginx && \
    wget -O ${NGINX_VERSION}.tar.gz https://nginx.org/download/${NGINX_VERSION}.tar.gz && \
    tar -zxf ${NGINX_VERSION}.tar.gz

# Download and decompress RTMP module
RUN mkdir -p /tmp/build/nginx-rtmp-module && \
    cd /tmp/build/nginx-rtmp-module && \
    wget -O nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz https://github.com/arut/nginx-rtmp-module/archive/v${NGINX_RTMP_MODULE_VERSION}.tar.gz && \
    tar -zxf nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz && \
    cd nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}

# Build and install Nginx
# The default puts everything under /usr/local/nginx, so it's needed to change
# it explicitly. Not just for order but to have it in the PATH
RUN cd /tmp/build/nginx/${NGINX_VERSION} && \
    ./configure \
        --prefix=/etc/nginx \
        --sbin-path=/usr/local/sbin/nginx \
        --conf-path=/etc/nginx/nginx.conf \
        --error-log-path=/var/log/nginx/error.log \
        --pid-path=/var/run/nginx/nginx.pid \
        --lock-path=/var/lock/nginx/nginx.lock \
        --http-log-path=/var/log/nginx/access.log \
        --http-client-body-temp-path=/tmp/nginx-client-body \
        --with-file-aio \
        --with-http_ssl_module \
        --with-http_flv_module \
        --with-threads \
        --with-ipv6 \
        --add-module=/tmp/build/nginx-rtmp-module/nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION} && \
    make -j $(getconf _NPROCESSORS_ONLN) && \
    make install && \
    mkdir /var/lock/nginx && \
    rm -rf /tmp/build
# Download and decompress FFmpeg
# depend module yasm
RUN mkdir -p /tmp/build/yams && \
    cd /tmp/build/yams && \
    wget http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz && \
    tar xzvf yasm-1.3.0.tar.gz && \
    cd yasm-1.3.0 && \
    ./configure --prefix="/usr/local/ffmpeg_build" --bindir="/usr/local/bin" && \
    make && \
    make install && \
    mkdir -p /tmp/build/ffmpeg  && \
    cd /tmp/build/ffmpeg && \
    wget http://ffmpeg.org/releases/ffmpeg-${FFMPET_VERSION}.tar.bz2 && \
    tar xjvf ffmpeg-${FFMPET_VERSION}.tar.bz2 && \
    cd ffmpeg-${FFMPET_VERSION} && \
    ./configure --prefix=/var/ffmpeg --enable-shared && \
    make && \
    make install && \
    rm -rf /tmp/build/yams/yasm-1.3.0.tar.gz && \
    rm -rf /tmp/build/ffmpeg/ffmpeg-${FFMPET_VERSION}.tar.bz2 && \
    rm -rf /tmp/build/ffmpeg && \
    rm -rf /tmp/build/yams
RUN echo /var/ffmpeg/lib >> /etc/ld.so.conf && \
    ldconfig && \
    cp -rp /var/ffmpeg/bin/ffmpeg /usr/local/bin/ && \
    cp -rp /var/ffmpeg/bin/ffprobe /usr/local/bin/
EXPOSE 1935
EXPOSE 80

CMD ["nginx"]
