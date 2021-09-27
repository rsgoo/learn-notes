1：进入目录

> cd /usr/local/src

2: 安装编译工具

> apt-get update
>
> apt-get -y install wget
>
> apt-get -y  install build-essential
>
> apt-get -y install zlib1g-dev
>
> apt-get -y install openssl

3: 下载并安装 pcre

> wget https://jaist.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz
>
> cd pcre-8.45
>
> ./confugure
>
> make && make install
>
> pcre-config --version
>
> cd /usr/local/src
>
> wget https://nginx.org/download/nginx-1.21.3.tar.gz
>
> cd nginx-1.21.3
>
> ./configure --prefix=/usr/local/src/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.45
>
> make && make install