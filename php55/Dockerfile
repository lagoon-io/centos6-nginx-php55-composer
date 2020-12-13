# 6系最後のバージョン
FROM centos:6.9

ARG TZ="Asia/Tokyo"
ARG DEV_PACKAGES="wget grep less tree vim git"
ARG PHP_PACKAGES="php php-mbstring php-devel php-gd php-mysql \
  php-pgsql php-pear php-pecl-apc php-pecl-apc-devel php-pecl-memcache php-xml \
  php-imap php-intl php-pdo php-mcrypt php-mysqli"

# composer設定
ENV PATH="$PATH:/opt/composer/vendor/bin"
ENV COMPOSER_HOME="/usr/bin/composer"
# composer update、install時のメモリ上限を無効化
ENV COMPOSER_MEMORY_LIMIT=-1

# マルチステージビルド
# composer 公式イメージからcomposerを移植
COPY --from=composer:1.10.13 /usr/bin/composer /usr/bin/composer
RUN chmod 777 /usr/bin/composer

# Nginxのリポジトリを追加
COPY ./php55/repos/nginx.repo /etc/yum.repos.d/nginx.repo

RUN echo "//--- phpインストール ---//" && \
  #   # remiリポジトリ追加
  sed -i -e "s/^mirrorlist=http:\/\/mirrorlist.centos.org/#mirrorlist=http:\/\/mirrorlist.centos.org/g" /etc/yum.repos.d/CentOS-Base.repo && \
  sed -i -e "s/^#baseurl=http:\/\/mirror.centos.org/baseurl=http:\/\/vault.centos.org/g" /etc/yum.repos.d/CentOS-Base.repo && \
  sed -i -e 's/NETWORKING_IPV6=yes/NETWORKING_IPV6=no/' /etc/sysconfig/network && \
  # EOL後のURL
  rpm -Uvh https://archives.fedoraproject.org/pub/archive/epel/6/x86_64/epel-release-6-8.noarch.rpm && \
  rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm && \
  yum -y install --enablerepo=remi,remi-php55 ${PHP_PACKAGES} && \
  # Nginxインストール
  echo "//--- Nginxインストール ---//" && \
  yum install -y nginx && \
  chkconfig nginx on && \
  # 開発用パッケージのインストール
  echo "//--- 開発用パッケージインストール ---//" && \
  yum install -y ${DEV_PACKAGES} && \
  rm -rf /var/cache/yum/* && \
  yum clean all

CMD ["/sbin/init"]
