FROM php:8.1-fpm

# Instalação das dependências necessárias
RUN apt-get update && apt-get install -y \
        unzip \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libaio1 \
        wget \
    && docker-php-ext-install -j$(nproc) iconv gettext \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

# Baixa e instala o Instant Client da Oracle
RUN cd /tmp && wget https://download.oracle.com/otn_software/linux/instantclient/219000/instantclient-basiclite-linux.x64-21.9.0.0.0dbru.zip
RUN cd /tmp && wget https://download.oracle.com/otn_software/linux/instantclient/219000/instantclient-sdk-linux.x64-21.9.0.0.0dbru.zip
RUN cd /tmp && wget https://download.oracle.com/otn_software/linux/instantclient/219000/instantclient-sqlplus-linux.x64-21.9.0.0.0dbru.zip

RUN unzip /tmp/instantclient-basiclite-linux.x64-21.9.0.0.0dbru.zip -d /usr/local/
RUN unzip /tmp/instantclient-sdk-linux.x64-21.9.0.0.0dbru.zip -d /usr/local/
RUN unzip /tmp/instantclient-sqlplus-linux.x64-21.9.0.0.0dbru.zip -d /usr/local/

RUN ln -s /usr/local/instantclient_21_9 /usr/local/instantclient
RUN ln -s /usr/local/instantclient/lib* /usr/lib
RUN ln -s /usr/local/instantclient/sqlplus /usr/bin/sqlplus

RUN echo 'export LD_LIBRARY_PATH="/usr/local/instantclient"' >> /root/.bashrc
RUN echo 'umask 002' >> /root/.bashrc

# Instalação do OCI8
RUN echo 'instantclient,/usr/local/instantclient' | pecl install oci8-3.0.1
RUN docker-php-ext-enable oci8

# Configurações PHP adicionais
RUN printf "log_errors = On \nerror_log = /dev/stderr\n" > /usr/local/etc/php/conf.d/php-logs.ini

# Copia o arquivo de configuração php.ini
COPY php.ini /usr/local/etc/php/

WORKDIR /var/www/html

# Cria um arquivo phpinfo para verificar a instalação
RUN echo "<?php echo phpinfo(); ?>" > /var/www/html/phpinfo.php

CMD ["php-fpm"]
