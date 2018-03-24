# 編譯原始碼

以下流程皆是在CentOS 7下進行，其他系統雖有不同，但都大同小異。

## 編譯前的準備

安裝編譯所需套件

```
$ yum install -y git wget
```

```
$ yum -y groupinstall "Development Tools"
```

## 下載原始碼

### nginx原始碼

目前穩定版本是 1.12.2

```
$ wget https://nginx.org/download/nginx-1.12.2.tar.gz
```

解壓縮

```
$ tar zxvf nginx-1.12.2.tar.gz
```

### Nchan原始碼

```
$ git clone https://github.com/slact/nchan.git
```

## 產生Makefile

在執行configure之前可以先用help看有哪些參數可以設定

```
$ ./configure --help
```

若不知道要設哪些，可以用-V參數查看目前（或其他已編譯好的）Nginx的編譯參數

```
$ nginx -V
```

```
nginx version: nginx/1.12.2
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/lib/nginx/tmp/client_body --http-proxy-temp-path=/var/lib/nginx/tmp/proxy --http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi --http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi --http-scgi-temp-path=/var/lib/nginx/tmp/scgi --pid-path=/run/nginx.pid --lock-path=/run/lock/subsys/nginx --user=nginx --group=nginx --with-file-aio --with-ipv6 --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module=dynamic --with-http_image_filter_module=dynamic --with-http_geoip_module=dynamic --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_slice_module --with-http_stub_status_module --with-http_perl_module=dynamic --with-mail=dynamic --with-mail_ssl_module --with-pcre --with-pcre-jit --with-stream=dynamic --with-stream_ssl_module --with-google_perftools_module --with-debug --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -m64 -mtune=generic' --with-ld-opt='-Wl,-z,relro -specs=/usr/lib/rpm/redhat/redhat-hardened-ld -Wl,-E'
```

以下將會使用CentOS 7用yum安裝的nginx編譯參數，並再加上nchan module

```
$ ./configure --prefix=/usr/share/nginx \
              --sbin-path=/usr/sbin/nginx \
              --modules-path=/usr/lib64/nginx/modules \
              --conf-path=/etc/nginx/nginx.conf \
              --error-log-path=/var/log/nginx/error.log \
              --http-log-path=/var/log/nginx/access.log \
              --http-client-body-temp-path=/var/lib/nginx/tmp/client_body \
              --http-proxy-temp-path=/var/lib/nginx/tmp/proxy \
              --http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi \
              --http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi \
              --http-scgi-temp-path=/var/lib/nginx/tmp/scgi \
              --pid-path=/run/nginx.pid \
              --lock-path=/run/lock/subsys/nginx \
              --user=nginx \
              --group=nginx \
              --with-file-aio \
              --with-ipv6 \
              --with-http_ssl_module \
              --with-http_v2_module \
              --with-http_realip_module \
              --with-http_addition_module \
              --with-http_xslt_module=dynamic \
              --with-http_image_filter_module=dynamic \
              --with-http_geoip_module=dynamic \
              --with-http_sub_module \
              --with-http_dav_module \
              --with-http_flv_module \
              --with-http_mp4_module \
              --with-http_gunzip_module \
              --with-http_gzip_static_module \
              --with-http_random_index_module \
              --with-http_secure_link_module \
              --with-http_degradation_module \
              --with-http_slice_module \
              --with-http_stub_status_module \
              --with-http_perl_module=dynamic \
              --with-mail=dynamic \
              --with-mail_ssl_module \
              --with-pcre \
              --with-pcre-jit \
              --with-stream=dynamic \
              --with-stream_ssl_module \
              --with-google_perftools_module --with-debug --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -m64 -mtune=generic' --with-ld-opt='-Wl,-z,relro -specs=/usr/lib/rpm/redhat/redhat-hardened-ld -Wl,-E'
```

## 編譯與安裝



