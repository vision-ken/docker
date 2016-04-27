#�����������
FROM centos:6.7

#����nginx�汾
ENV NGINX_VERSION 1.9.14

#׼����װ����
RUN yum install -y wget && \ 
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo  && \ 
yum clean all  && \ 
yum makecache && \

#��װ�������
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-* && \
yum install -y epel-release && \
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6 && \
yum install -y patch pcre-devel openssl-devel zlib-devel gd-devel tar gcc git supervisor && \

#���ذ�װ���Ͳ���
mkdir -p /var/run/nginx/ && \
wget -c http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz && \
git clone https://github.com/cuber/ngx_http_google_filter_module.git && \
git clone https://github.com/yaoweibin/ngx_http_substitutions_filter_module.git && \
git clone https://github.com/aperezdc/ngx-fancyindex.git && \
git clone https://github.com/yaoweibin/nginx_upstream_check_module.git && \

#���б��밲װ��ͬʱ���ϲ���
tar xf nginx-${NGINX_VERSION}.tar.gz && \
cd nginx-${NGINX_VERSION} && \
cd src/ && \
#�򲹶�
patch -p1 < /nginx_upstream_check_module/check_1.9.2+.patch && \
cd .. && \
#ȥ��nginx�Ķ���汾��
sed -i -e 's/${NGINX_VERSION}//g' -e 's/nginx\//ERROR/g' -e 's/"NGINX"/"ERROR"/g' src/core/nginx.h  && \
./configure --prefix=/usr/local/nginx \
--with-pcre \
--with-ipv6 \
--with-http_ssl_module \
--with-http_flv_module \
--with-http_v2_module \
--with-http_realip_module \
--with-http_gzip_static_module \
--with-http_stub_status_module \
--with-http_mp4_module \
--with-http_image_filter_module \
--with-http_addition_module \
--with-http_sub_module  \
--with-http_dav_module  \
--http-client-body-temp-path=/usr/local/nginx/client/ \
--http-proxy-temp-path=/usr/local/nginx/proxy/ \
--http-fastcgi-temp-path=/usr/local/nginx/fcgi/ \
--http-uwsgi-temp-path=/usr/local/nginx/uwsgi \
--http-scgi-temp-path=/usr/local/nginx/scgi \
--add-module=../ngx_http_google_filter_module \
--add-module=../ngx_http_substitutions_filter_module \
--add-module=../ngx-fancyindex \
--add-module=../nginx_upstream_check_module && \
#��ʼ����
make -j $(awk '/processor/{i++}END{print i}' /proc/cpuinfo) && make install && \
#����һЩ����Ŀ¼
mkdir -p /usr/local/nginx/cache/ && \ 
mkdir -p /usr/local/nginx/temp/ && \ 
rm -rf ../{ngx*,nginx*} && \
yum clean packages

#����nginx������һ��ǰ̨���̣����ⱻdockerǿ���˳�
CMD ./usr/local/nginx/sbin/nginx && tail -f /usr/local/nginx/logs/error.log