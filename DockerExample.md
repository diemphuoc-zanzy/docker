## Hướng dẫn các lệnh trong dockerfile
Dockerfile là file config dùng để build các docker image. Hôm nay chúng ta cùng tìm hiểu các chỉ thị cơ bản trong dockerfile(dokerfile instruction). Các để hiểu sâu về dockerfile các bạn cần tìm hiểu các kiến thức liên quan đến <b>linux command</b> và <b>shell script</b> nhé.

## Dockerfile instruction
### 1. FROM
Một dockerfile đúng phải bắt đầu bằng chỉ thị from(from instruction)

``` dockerfile
    FROM [image]:[tag]
    FROM ubuntu:18.04
```

Chỉ thị FROM khai báo image cơ sở, gồm 2 phần tên và tag của image trên docker hub. Khi build image docker sẽ pull image cơ sở trên docker hub nếu máy của bạn chưa có image trên để khởi tạo image mới của bạn.

Lưu ý không nên khai báo tag là latest vì mỗi khi build lại image của bạn sẽ bị update lên version mới nhất làm mất tính ổn định của ứng dụng, vì vậy nhớ khai báo tag của image cụ thể nhé!

### 2. LABEL
Dùng để khai báo thông tin metadata cho image. Thường khai báo các thông tin người tạo dockerfile

### 3. ENV
Thiết lập biến môi trường

### 4. RUN
Chỉ chạy khi build image, được sử dụng để cài các pagekage vào container, mỗi một RUN instruction sẽ tạo một image layer, tương tự với các chỉ thị CMD, ENTRYPOINT, ADD...
Lưu ý image càng ít layer thì dung lượng càng nhỏ và buid nhanh hơn .Vì vậy khi build image cần gom nhóm các lệnh cần chạy với chỉ thị run, thêm \ để chạy nhiều lệnh trong 1 chỉ thị run.

### 5.COPY, ADD
Copy file vào từ host machine to container. Điểm khác giữa copy và add là add có thể download file từ url

### 6. CMD, ENTRYPOINT
Đều dùng để chỉ định các lệnh sẽ được chạy khi container bắt đầu chạy.

``` dockerfile
  # Có 2 cách khai báo ENTRYPOINT
  #exec form
  ENTRYPOINT commnad_script
  #shell form
  ENTRYPOINT command param1 param2
  
  # Có 3 cách khai báo CMD
  CMD ["executable","param1","param2"] (exec form, this is the preferred form)
  CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
  CMD command param1 param2 (shell form)
```
Nếu dùng cả ENTRYPOINT và CMD thì khai báo trong EMTRYPOINT sẽ là command được chạy trong container và khai báo trong CMD sẽ là tham số cho command trong ENTRYPOINT

### 7. WORKDIR
Thiết lập thư mục làm việc

### 8. ARG
Khai báo biến trong dockerfile

### 9. VOLUME
Dùng để lưu dữ liệu,  khi doker bị xoá thì toàn bộ dữ liệu sẽ bị xoá vì vậy dữ liệu cần được lưu lại ở host machine.

### 10. EXPOSE
Dùng để mở cổng cho container, post mà container lắng nghe request, or giao tiếp với container khác cùng mạng.

### 11. User
Chỉ định user chạy container. Mặc định docker sẽ chạy container với user root.

<b>Chú ý khi viết dockerfile các chỉ thị ít thay đổi sẽ được viết trước giúp quá trình build sẽ nhanh hơn.</b>

## Docker file demo

``` dockerfile
    # Buid docker image php từ image php với tag php:7.4.11-fpm-alpine
    
    FROM php:7.4.11-fpm-alpine AS prod
    
    # Optional, force UTC as server time
    RUN echo "UTC" > /etc/timezone
    
    # Install essential build tools
    RUN apk update && apk add --no-cache git \
            autoconf \
            g++ \
            gcc \
            make \
            zlib-dev\
            bash \
            grep \
            mysql-client \
            dcron \
            tar \
            supervisor
    # Setup bzip2 extension
    RUN apk add --no-cache \
            bzip2-dev \
        && docker-php-ext-install -j$(nproc) bz2 \
        && docker-php-ext-enable bz2
    # Setup GD extension
    RUN apk add --no-cache \
            freetype \
            libjpeg-turbo \
            libpng \
            freetype-dev \
            libjpeg-turbo-dev \
            libpng-dev \
        && docker-php-ext-configure gd \
            --with-freetype=/usr/include/ \
            --with-jpeg=/usr/include/ \
        && docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-enable gd
    # Install intl extension
    RUN apk add --no-cache icu-dev \
        && docker-php-ext-install -j$(nproc) intl \
        && docker-php-ext-enable intl
    # Install mbstring extension
    RUN apk add --no-cache oniguruma-dev \
        && docker-php-ext-install -j$(nproc) mbstring \
        && docker-php-ext-enable mbstring
    # Force UTC timezone
    RUN apk add --no-cache tzdata \
        && cp /usr/share/zoneinfo/UTC /etc/localtime \
        && echo 'UTC' > /etc/localtime
    # Install Iconv
    RUN apk add --no-cache --repository http://dl-3.alpinelinux.org/alpine/edge/testing gnu-libiconv --allow-untrusted \
        && docker-php-ext-install -j$(nproc) iconv
    ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so
    
    # Install bcmath
    RUN docker-php-ext-install -j$(nproc) bcmath
    # Install sqlite
    RUN apk add --no-cache sqlite-dev \
        && docker-php-ext-install -j$(nproc) \
            pdo_sqlite \
            pdo_mysql
    # Install readline extension
    RUN apk add --no-cache libedit-dev \
        && docker-php-ext-install -j$(nproc) readline
    # Install zip extension
    RUN apk add --no-cache libzip-dev \
        && docker-php-ext-install -j$(nproc) zip
    # Install curl extension
    RUN apk add --no-cache libcurl curl-dev \
        && docker-php-ext-install -j$(nproc) curl
    # Install gettext extension
    RUN apk add --no-cache gettext-dev \
        && docker-php-ext-install -j$(nproc) gettext
    # Install other extension
    RUN docker-php-ext-install -j$(nproc) \
            json \
            opcache \
            mysqli \
            calendar \
            fileinfo \
            exif \
        && docker-php-ext-enable opcache
    # Install imagick
    RUN export CFLAGS="$PHP_CFLAGS" CPPFLAGS="$PHP_CPPFLAGS" LDFLAGS="$PHP_LDFLAGS" \
        && apk add --no-cache imagemagick \
            imagemagick-dev \
        && pecl install imagick \
        && docker-php-ext-enable imagick \
        && rm -rf /tmp/*
    
    VOLUME /var/www/html
    
    # Install composer
    RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=1.10.16
    
    COPY php.ini /usr/local/etc/php/php.ini
    
    WORKDIR /var/www/html
```




























