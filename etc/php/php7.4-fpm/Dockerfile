FROM php:7.4-fpm

# 设置时区
ENV TZ=Asia/Shanghai

#swoole版本
ENV SWOOLE_VERSION=4.5.2

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 替换阿里源
RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list \
  && sed -i 's/security.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list

# 更新安装依赖包
RUN apt update && apt install -y \
        git \
        vim \
        curl \
        wget \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libwebp-dev \
        libjpeg-dev \
        libxml2-dev \
		libssl-dev \
        libzip-dev \
        librabbitmq-dev \
        unzip \
        iputils-ping \
        supervisor

# 安装PHP核心拓展
RUN docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
	&& docker-php-ext-install gd zip pdo_mysql opcache mysqli bcmath sockets pcntl

# 安装PECL扩展
RUN pecl install -o -f amqp redis

# 安装swoole
RUN wget http://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -O swoole.tar.gz && \
        mkdir -p swoole && \
        tar -xf swoole.tar.gz -C swoole --strip-components=1 && \
        rm swoole.tar.gz && \
        ( \
        cd swoole && \
        phpize && \
        ./configure --enable-mysqlnd --enable-sockets --enable-openssl --enable-http2 && \
        make -j$(nproc) && \
        make install \
        ) && \
        rm -r swoole

RUN docker-php-ext-enable amqp redis swoole

# 安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH
# 更换Composer为腾讯源
RUN composer config -g repo.packagist composer https://mirrors.cloud.tencent.com/composer/

# 清理镜像
RUN rm -rf /var/lib/apt/lists/* && rm -rf /tmp/pear

# 写权限
RUN usermod -u 1000 www-data

# 创建supervisor必要文件并运行
RUN touch /var/run/supervisor.sock

WORKDIR /www