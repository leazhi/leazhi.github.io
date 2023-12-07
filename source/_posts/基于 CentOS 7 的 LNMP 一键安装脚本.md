---
title: 基于 CentOS 7 的 LNMP 一键安装脚本
author: leazhi
tags:
  - 
categories:
  - 
date: 2023-09-25 09:17:36
cover: 
discription: 
keywords: 
password: 
message: 
---


## 系统环境：
- OS：CentOS-7-x86_64-Minimal-1908
- Kernel：3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux


一键安装脚本：
```bash
#!/usr/bin/env bash

set -eu


# 安装初始依赖包
yum install -y wget gcc gcc-c++ make autoconf automake unzip lrzsz vim epel-release systemd-devel systemtap-sdt-devel libxml2-devel openssl-devel bzip2-devel curl-devel libjpeg-devel libpng-devel libXpm-devel  freetype-devel   t1lib-devel   gmp-devel  libicu-devel libmcrypt-devel libedit-devel libxslt-devel gd-devel GeoIP-devel  gperftools-devel libatomic_ops-devel

yum update -y


## MySQL 5.6.40

datadir='/data/mysql'

if [  -z "$(egrep mysql /etc/passwd)" ] ;then
    useradd -s /sbin/nologin mysql
fi

if [ ! -d "${datadir}" ] ;then
    mkdir -p ${datadir}
fi

if [ ! -f '/usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz' -a ! -d '/usr/local/mysql' ]; then
    wget -O /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz https://cdn.mysql.com/archives/mysql-5.6/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz

    tar -zxf /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz -C /usr/local/src/

    mv /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64 /usr/local/mysql
elif [ -f '/usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz' -a ! -d '/usr/local/mysql' ]; then

    tar -zxf /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz -C /usr/local/src/

    mv /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64 /usr/local/mysql
else
    echo 'MySQL 5.6.50 is already installed'
fi

cd /usr/local/mysql/

./scripts/mysql_install_db --datadir=${datadir} --user=mysql --skip-name-resolve

echo 'PATH=$PATH:/usr/local/mysql/bin' > /etc/profile.d/mysql.sh

source /etc/profile.d/mysql.sh



cat <<EOF >/lib/systemd/system/mysql.service
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target

[Service]
ExecStart=/usr/local/mysql/bin/mysqld_safe --datadir=/data/mysql
User=mysql
Group=mysql
TimeoutSec=300
PrivateTmp=true

[Install]
WantedBy=multi-user.target
EOF

## PHP 5.6.40

useradd -s /sbin/nologin php-fpm

wget -O /usr/local/src/php-5.6.40.tar.gz https://www.php.net/distributions/php-5.6.40.tar.gz

tar -zxf /usr/local/src/php-5.6.40.tar.gz -C /usr/local/src/

cd /usr/local/src/php-5.6.40

./configure --prefix=/usr/local/php --enable-mod-charset --enable-fpm --with-fpm-user=php-frpm --with-fpm-group=php-fpm --with-fpm-systemd --enable-phpdbg --with-config-file-path=/usr/local/php/etc --disable-ipv6 --enable-dtrace --with-libxml-dir --with-openssl --with-pcre-regex --with-zlib --enable-bcmath --with-bz2 --with-curl --enable-dba --enable-exif --with-pcre-dir --enable-ftp --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir --with-xpm-dir --with-t1lib --enable-gd-native-ttf --enable-gd-jis-conv  --with-gettext --with-gmp --with-mhash --enable-intl  --enable-mbstring --with-libmbfl --with-onig --with-mcrypt --with-mysql=mysqlnd --with-mysqli=mysqlnd --enable-embedded-mysqli --enable-opcache --enable-pcntl --with-pdo-mysql=mysqlnd --with-libedit --enable-shmop --enable-soap --enable-sockets --enable-wddx  --enable-zip --with-pcre-dir --enable-mysqlnd --enable-maintainer-zts --enable-zend-signals

make && make install 

cp php.ini-production /usr/local/php/etc/php.ini
cp sapi/fpm/php-fpm.service /lib/systemd/system/
cp sapi/fpm/php-fpm.conf /usr/local/php/etc/php-fpm.conf
sed -i 's/php-frpm/php-fpm/' /usr/local/php/etc/php-fpm.conf


##NGINX 1.18.0
useradd -s /sbin/nologin nginx
# yum install -y libxslt-devel gd-devel GeoIP-devel  gperftools-devel libatomic_ops-devel

wget -O /usr/local/src/openssl-1.1.1w.tar.gz  https://www.openssl.org/source/openssl-1.1.1w.tar.gz
tar -zxf /usr/local/src/openssl-1.1.1w.tar.gz  -C /usr/local/src/

wget -O /usr/local/src/zlib.tar.gz https://www.zlib.net/current/zlib.tar.gz
tar -zxf /usr/local/src/zlib.tar.gz -C  /usr/local/src/

wget -O /usr/local/src/nginx-1.18.0.tar.gz https://nginx.org/download/nginx-1.18.0.tar.gz
tar -zxf /usr/local/src/nginx-1.18.0.tar.gz -C /usr/local/src/

cd /usr/local/src/nginx-1.18.0/

./configure --prefix=/usr/local/nginx \
--user=nginx \
--group=nginx \
--with-threads \
--with-http_ssl_module  \
--with-http_v2_module   \
--with-http_realip_module  \
--with-http_addition_module  \
--with-http_xslt_module=dynamic \
--with-http_image_filter_module=dynamic \
--with-http_geoip_module=dynamic \
--with-http_sub_module  \
--with-http_dav_module \
--with-http_flv_module  \
--with-http_mp4_module  \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_auth_request_module \
--with-http_random_index_module \
--with-http_secure_link_module  \
--with-http_degradation_module \
--with-http_slice_module  \
--with-http_stub_status_module \
--with-stream \
--with-stream_ssl_module \
--with-stream_realip_module \
--with-stream_geoip_module  \
--with-stream_ssl_preread_module  \
--with-google_perftools_module  \
--with-compat  \
--with-zlib=/usr/local/src/zlib-1.3 \
--with-pcre \
--with-libatomic \
--with-openssl=/usr/local/src/openssl-1.1.1w

make  && make install 
```


## PHP configure error

```bash
checking for pkg-config... /usr/bin/pkg-config
checking for sd_notify in -lsystemd-daemon... no
checking systemd/sd-daemon.h usability... no
checking systemd/sd-daemon.h presence... no
checking for systemd/sd-daemon.h... no
configure: error: Your system does not support systemd.


 yum install -y systemd-devel
```

```bash
checking for crypt_r... no

General settings
checking whether to include gcov symbols... yes
checking for lcov... no
checking for genhtml... no
configure: error: To enable code coverage reporting you must have LTP installed
```

```BASH
General settings
checking whether to include gcov symbols... yes
checking for lcov... no
checking for genhtml... no
configure: error: To enable code coverage reporting you must have LTP installed


去掉了 --enable-gcov  \
```

```bash
General settings
checking whether to include gcov symbols... no
checking whether to include debugging symbols... no
checking layout of installed files... PHP
checking path to configuration file... /usr/local/php/etc
checking where to scan for configuration files...
checking whether to enable PHP's own SIGCHLD handler... no
checking whether to explicitly link against libgcc... no
checking whether to enable short tags by default... yes
checking whether to enable dmalloc... yes
checking for dmalloc_error in -ldmalloc... no
configure: error: Problem with enabling dmalloc. Please check config.log for details.

去掉 --enable-dmalloc \
```

```bash
General settings
checking whether to include gcov symbols... no
checking whether to include debugging symbols... no
checking layout of installed files... PHP
checking path to configuration file... /usr/local/php/etc
checking where to scan for configuration files...
checking whether to enable PHP's own SIGCHLD handler... no
checking whether to explicitly link against libgcc... no
checking whether to enable short tags by default... yes
checking whether to enable dmalloc... no
checking whether to enable IPv6 support... no
checking whether to enable DTrace support... yes
checking sys/sdt.h usability... no
checking sys/sdt.h presence... no
checking for sys/sdt.h... no
configure: error: Cannot find sys/sdt.h which is required for DTrace support


 systemtap-sdt-devel

```

```bash
checking for xml2-config path...
configure: error: xml2-config not found. Please check your libxml2 installation.

[root@localhost php-5.6.40]# yum install -y libxml2-devel

```

```bash
checking for Kerberos support... no
checking whether to use system default cipher list instead of hardcoded value... no
checking for DSA_get_default_method in -lssl... no
checking for X509_free in -lcrypto... no
checking for RAND_egd... no
configure: error: Cannot find OpenSSL's <evp.h>

[root@localhost php-5.6.40]# yum install -y openssl-devel

```

```bash
checking for BZip2 in default path... not found
configure: error: Please reinstall the BZip2 distribution

[root@localhost php-5.6.40]# yum install -y bzip2-devel
```

```bash
checking for cURL in default path... not found
configure: error: Please reinstall the libcurl distribution -
    easy.h should be in <curl-dir>/include/curl/

[root@localhost php-5.6.40]# yum install -y curl-devel

```

```bash
If configure fails try --with-vpx-dir=<DIR>
configure: error: jpeglib.h not found.

libjpeg-devel
```

```bash
If configure fails try --with-vpx-dir=<DIR>
checking for jpeg_read_header in -ljpeg... yes
configure: error: png.h not found.

libpng-devel
```

```bash
If configure fails try --with-vpx-dir=<DIR>
checking for jpeg_read_header in -ljpeg... yes
checking for png_write_image in -lpng... yes
configure: error: xpm.h not found.

libXpm-devel
```

```bash
configure: error: freetype-config not found.

 freetype-devel
```

```bash
configure: error: Your t1lib distribution is not installed correctly. Please reinstall it.

t1lib-devel
```

```bash
configure: error: Unable to locate gmp.h

 gmp-devel

```

```bash
checking for location of ICU headers and libraries... not found
configure: error: Unable to detect ICU prefix or yes/bin/icu-config failed. Please verify ICU install prefix and make sure icu-config works.

libicu-devel   不生效
```

```bash
configure: error: mcrypt.h not found. Please reinstall libmcrypt.

libmcrypt-devel
```

```bash
checking for libedit readline replacement... yes
configure: error: Please reinstall libedit - I cannot find readline.h

libedit-devel
```

## nginx 
```bash
./configure: error: the HTTP XSLT module requires the libxml2/libxslt
libraries. You can either do not enable the module or install the libraries.

libxslt-devel
```

```bash
hecking for GD library ... not found
checking for GD library in /usr/local/ ... not found
checking for GD library in /usr/pkg/ ... not found
checking for GD library in /opt/local/ ... not found

./configure: error: the HTTP image filter module requires the GD library.
You can either do not enable the module or install the libraries.

gd-devel
```

```bash
checking for GeoIP library in /usr/local/ ... not found
checking for GeoIP library in /usr/pkg/ ... not found
checking for GeoIP library in /opt/local/ ... not found

./configure: error: the GeoIP module requires the GeoIP library.
You can either do not enable the module or install the library.

[root@localhost nginx-1.18.0]# yum install -y GeoIP-devel

```


```bash
checking for Google perftools ... not found
checking for Google perftools in /usr/local/ ... not found
checking for Google perftools in /opt/local/ ... not found

./configure: error: the Google perftools module requires the Google perftools
library. You can either do not enable the module or install the library.

 gperftools-devel

```


```bash
checking for GD WebP support ... not found
checking for GeoIP library ... found
checking for GeoIP IPv6 support ... found
checking for Google perftools ... found
checking for atomic_ops library ... not found

./configure: error: libatomic_ops library was not found.

libatomic_ops-devel

```


### make 
```bash
[root@localhost nginx-1.18.0]# make
make -f objs/Makefile
make[1]: Entering directory `/usr/local/src/nginx-1.18.0'
cd /usr/local/ssl \
&& if [ -f Makefile ]; then make clean; fi \
&& ./config --prefix=/usr/local/ssl/.openssl no-shared no-threads  \
&& make \
&& make install_sw LIBDIR=lib
/bin/sh: line 2: ./config: No such file or directory
make[1]: *** [/usr/local/ssl/.openssl/include/openssl/ssl.h] Error 127
make[1]: Leaving directory `/usr/local/src/nginx-1.18.0'
make: *** [build] Error 2

要指定 openssl 源码解压目录，不能指定 opnessl 安装目录
```


```bash
&& CFLAGS="-O2 -fomit-frame-pointer -pipe " CC="cc" \
        ./configure \
&& make libz.a
make[2]: Entering directory `/usr'
make[2]: *** No rule to make target `distclean'.  Stop.
make[2]: Leaving directory `/usr'
make[1]: *** [/usr/libz.a] Error 2
make[1]: Leaving directory `/usr/local/src/nginx-1.18.0'
make: *** [build] Error 2


--with-zlib=/usr/local/src/zlib
```















