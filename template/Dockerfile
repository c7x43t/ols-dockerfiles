FROM rockylinux:9.2

# ARG OLS_VERSION
# ARG PHP_VERSION
USER root
# Set the build-time variables
ARG OLS_VERSION=1.7.16
ARG PHP_VERSION=lsphp82

# Use the build-time variables
ENV OLS_VERSION=${OLS_VERSION}
ENV PHP_VERSION=${PHP_VERSION}


RUN dnf update -y
RUN dnf install wget crontabs tzdata -y

RUN wget https://repo.litespeed.sh -O litespeed.sh
RUN bash litespeed.sh

RUN wget https://openlitespeed.org/preuse/openlitespeed-$OLS_VERSION.tgz
RUN  tar xzf openlitespeed-$OLS_VERSION.tgz && cd openlitespeed && ./install.sh
RUN echo 'cloud-docker' > /usr/local/lsws/PLAT && rm -rf /openlitespeed && rm /openlitespeed-$OLS_VERSION.tgz

RUN dnf install mysql -y

RUN dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
RUN dnf install libargon2 -y

RUN dnf install $PHP_VERSION $PHP_VERSION-common -y

RUN dnf install $PHP_VERSION-mysqlnd -y
RUN dnf install $PHP_VERSION-process -y
RUN dnf install $PHP_VERSION-bcmath -y
RUN dnf install $PHP_VERSION-pdo -y
RUN dnf install $PHP_VERSION-xml -y
RUN dnf install $PHP_VERSION-soap -y
RUN dnf install $PHP_VERSION-opcache -y

RUN dnf install $PHP_VERSION-curl -y
#RUN dnf install -y GraphicsMagick GraphicsMagick-devel GraphicsMagick-perl
# RUN dnf install -y ImageMagick ImageMagick-devel ImageMagick-perl
RUN dnf groupinstall -y 'Development Tools'

# RUN dnf -y install bzip2-devel freetype-devel libjpeg-devel libpng-devel libtiff-devel giflib-devel zlib-devel ghostscript-devel djvulibre-devel libwmf-devel jasper-devel libtool-ltdl-devel libX11-devel libXext-devel libXt-devel lcms-devel libxml2-devel librsvg2-devel OpenEXR-devel php-devel
RUN wget https://www.imagemagick.org/download/ImageMagick.tar.gz && \
tar xvzf ImageMagick.tar.gz && \
cd ImageMagick*/ && \
./configure && \
make && \ 
make install
RUN dnf install $PHP_VERSION-imagick -y

RUN dnf install $PHP_VERSION-redis -y

RUN dnf install $PHP_VERSION-memcached -y

RUN dnf install $PHP_VERSION-intl -y
RUN dnf install $PHP_VERSION-mbstring -y
RUN dnf install $PHP_VERSION-mcrypt  -y
RUN dnf install $PHP_VERSION-mhash  -y
RUN dnf install $PHP_VERSION-simplexml  -y
RUN dnf install $PHP_VERSION-gd -y



RUN ["/bin/bash", "-c", "if [[ $PHP_VERSION == lsphp7* ]]; then apt-get install $PHP_VERSION-json -y; fi"]

RUN wget -O /usr/local/lsws/admin/misc/lsup.sh \
    https://raw.githubusercontent.com/litespeedtech/openlitespeed/master/dist/admin/misc/lsup.sh && \
    chmod +x /usr/local/lsws/admin/misc/lsup.sh

RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
	chmod +x wp-cli.phar && mv wp-cli.phar /usr/bin/wp && \
	ln -s /usr/local/lsws/$PHP_VERSION/bin/php /usr/bin/php

RUN wget -O -  https://get.acme.sh | sh

EXPOSE 7080
ENV PATH="/usr/local/sbin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/bin"

ADD docker.conf /usr/local/lsws/conf/templates/docker.conf
ADD setup_docker.sh /usr/local/lsws/bin/setup_docker.sh
ADD httpd_config.xml /usr/local/lsws/conf/httpd_config.xml
ADD htpasswd /usr/local/lsws/admin/conf/htpasswd

RUN /usr/local/lsws/bin/setup_docker.sh && rm /usr/local/lsws/bin/setup_docker.sh
RUN chown 999:999 /usr/local/lsws/conf -R
RUN cp -RP /usr/local/lsws/conf/ /usr/local/lsws/.conf/
RUN cp -RP /usr/local/lsws/admin/conf /usr/local/lsws/admin/.conf/
#RUN sed -i "s|fcgi-bin/lsphp|/usr/local/lsws/$PHP_VERSION/bin/lsphp|g" /usr/local/lsws/conf/httpd_config.conf
RUN ["/bin/bash", "-c", "if [[ $PHP_VERSION == lsphp8* ]]; then ln -sf /usr/local/lsws/$PHP_VERSION/bin/lsphp /usr/local/lsws/fcgi-bin/lsphp8; fi"]
RUN ["/bin/bash", "-c", "if [[ $PHP_VERSION == lsphp8* ]]; then ln -sf /usr/local/lsws/fcgi-bin/lsphp8 /usr/local/lsws/fcgi-bin/lsphp; fi"]
RUN ["/bin/bash", "-c", "if [[ $PHP_VERSION == lsphp7* ]]; then ln -sf /usr/local/lsws/$PHP_VERSION/bin/lsphp /usr/local/lsws/fcgi-bin/lsphp7; fi"]
RUN ["/bin/bash", "-c", "if [[ $PHP_VERSION == lsphp7* ]]; then ln -sf /usr/local/lsws/fcgi-bin/lsphp7 /usr/local/lsws/fcgi-bin/lsphp; fi"]
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
WORKDIR /var/www/vhosts/
