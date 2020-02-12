FROM ubuntu:16.04

LABEL maintainer="Bruce <Bruce.lin@plus.ai>"
# Install uWSGI

RUN apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y \
    build-essential \
    ca-certificates \
    gcc \
    git \
    libpq-dev \
    make \
    python-pip \
    python2.7 \
    python2.7-dev \
    && pip install -U "setuptools==3.4.1" \
    && pip install uwsgi  \


# Standard set up Nginx
ENV NGINX_VERSION 1.15.8-1~xenial
ENV NJS_VERSION   1.15.8.0.2.7-1~xenial

RUN set -x \
	&& apt-get install --no-install-recommends --no-install-suggests -y apt-transport-https ca-certificates \
	&& apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62 \
    && rm -rf /var/lib/apt/lists/* \
	&& dpkgArch="$(dpkg --print-architecture)" \
	&& nginxPackages=" \
		nginx=${NGINX_VERSION} \
		nginx-module-xslt=${NGINX_VERSION} \
		nginx-module-geoip=${NGINX_VERSION} \
		nginx-module-image-filter=${NGINX_VERSION} \
		nginx-module-njs=${NJS_VERSION} \
	" \
	&& echo "deb https://nginx.org/packages/mainline/ubuntu/ xenial nginx" >> /etc/apt/sources.list.d/nginx.list \
	&& echo "deb-src https://nginx.org/packages/mainline/ubuntu/ xenial nginx" >> /etc/apt/sources.list.d/nginx.list \
	&& apt-get update \
	&& tempDir="$(mktemp -d)" \
	&& chmod 777 "$tempDir" \
    && savedAptMark="$(apt-mark showmanual)" \
	&& apt-get build-dep -y $nginxPackages \
	&& apt-get install --no-install-recommends --no-install-suggests -y \
						$nginxPackages \
						gettext-base

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log
EXPOSE 80

EXPOSE 443

RUN rm /etc/nginx/conf.d/default.conf
COPY uwsgi.ini /etc/uwsgi/

RUN apt-get update && apt-get install -y supervisor \
&& rm -rf /var/lib/apt/lists/*
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ENV UWSGI_INI /app/uwsgi.in
ENV UWSGI_CHEAPER 2
ENV UWSGI_PROCESSES 16
ENV NGINX_MAX_UPLOAD 0
ENV NGINX_WORKER_PROCESSES 1
ENV LISTEN_PORT 80
COPY start.sh /start.sh
RUN chmod +x /start.sh
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
COPY ./app /app
WORKDIR /app
CMD ["/start.sh"]
