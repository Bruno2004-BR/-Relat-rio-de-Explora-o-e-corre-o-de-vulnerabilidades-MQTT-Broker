FROM debian:11
LABEL maintainer="Professor Tiago Demay"
LABEL description="Lab Pentest - Apache 2.4.49 vulnerável + PHP 7.3.31"
ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /root

# Deps
RUN apt-get update && apt-get install -y \
  build-essential libpcre3-dev libssl-dev libexpat1-dev zlib1g-dev \
  ca-certificates wget curl unzip tar vim procps dos2unix \
  netcat-traditional iputils-ping \
  libxml2-dev libsqlite3-dev libonig-dev libcurl4-openssl-dev \
  libjpeg-dev libpng-dev libzip-dev libreadline-dev pkg-config \
  && rm -rf /var/lib/apt/lists/*

# Arquivos do projeto
COPY httpd.conf /tmp/httpd.conf
COPY install_php.sh /tmp/install_php.sh
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh /tmp/install_php.sh && dos2unix /entrypoint.sh /tmp/install_php.sh

# --- Apache 2.4.49 com DSO ---
RUN set -eux; \
  mkdir -p /usr/local/src && cd /usr/local/src && \
  wget https://archive.apache.org/dist/httpd/httpd-2.4.49.tar.gz && tar -xzf httpd-2.4.49.tar.gz && \
  cd httpd-2.4.49 && \
  wget https://archive.apache.org/dist/apr/apr-1.7.0.tar.gz && \
  wget https://archive.apache.org/dist/apr/apr-util-1.6.1.tar.gz && \
  tar -xzf apr-1.7.0.tar.gz && tar -xzf apr-util-1.6.1.tar.gz && \
  mv apr-1.7.0 srclib/apr && mv apr-util-1.6.1 srclib/apr-util && \
  ./configure --prefix=/usr/local/apache2 --with-included-apr \
    --enable-so --enable-mods-shared=most \
    --enable-mpms-shared=all --with-mpm=prefork \
    --enable-cgi --enable-rewrite --enable-ssl \
  && make -j"$(nproc)" && make install

# Configuração vulnerável (inclui Alias /icons e CGI liberado)
RUN cp /tmp/httpd.conf /usr/local/apache2/conf/httpd.conf

# Estrutura e permissões
RUN mkdir -p /usr/local/apache2/htdocs/poc /usr/local/apache2/cgi-bin && \
    chown -R daemon:daemon /usr/local/apache2

# PHP como módulo (libphp7.so)
RUN /tmp/install_php.sh

# --- PoC CGI: RCE ---
RUN printf '%s\n' \
  '#!/bin/bash' \
  'echo "Content-Type: text/plain"' \
  'echo' \
  'echo "VULNERABLE - RCE via CGI"' \
  'id' \
  > /usr/local/apache2/cgi-bin/poc.sh \
  && dos2unix /usr/local/apache2/cgi-bin/poc.sh \
  && chown daemon:daemon /usr/local/apache2/cgi-bin/poc.sh \
  && chmod 755 /usr/local/apache2/cgi-bin/poc.sh

# --- PoC CGI: info ---
RUN printf '%s\n' \
  '#!/bin/bash' \
  'echo "Content-Type: text/plain"' \
  'echo' \
  'uname -a' \
  > /usr/local/apache2/cgi-bin/info.sh \
  && dos2unix /usr/local/apache2/cgi-bin/info.sh \
  && chown daemon:daemon /usr/local/apache2/cgi-bin/info.sh \
  && chmod 755 /usr/local/apache2/cgi-bin/info.sh

# PHP PoCs
RUN echo "<?php echo 'PoC PHP Funcionando!'; ?>" > /usr/local/apache2/htdocs/poc/teste.php \
 && echo '<?php header("Content-Type: text/plain"); system($_GET["cmd"] ?? "id"); ?>' > /usr/local/apache2/htdocs/poc/shell.php \
 && chown -R daemon:daemon /usr/local/apache2/htdocs \
 && dos2unix /usr/local/apache2/htdocs/poc/*.php || true

# Sobrescreve a página padrão
COPY --chown=daemon:daemon index.html /usr/local/apache2/htdocs/index.html

EXPOSE 80
ENTRYPOINT ["/entrypoint.sh"]
