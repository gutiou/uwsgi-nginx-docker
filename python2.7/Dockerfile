FROM ubuntu:16.04

LABEL maintainer="Bruce <Bruce.lin@plus.ai>"
# Install uWSGI

RUN apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y \
    apt-utils build-essential \
    ca-certificates \
    libpq-dev \
    python2.7 \
    python2.7-dev \
    python-pip \
    apt-transport-https ca-certificates \
    nginx supervisor \
    && pip install --upgrade pip \
    && pip install -U "setuptools==41.2.0" \
    && pip install "wheel==0.33.6" \
    && pip install uwsgi  \
    && rm -rf /var/lib/apt/lists/*  \
    && ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80
EXPOSE 443
COPY uwsgi.ini /etc/uwsgi/
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ENV UWSGI_INI /app/uwsgi.ini
ENV UWSGI_CHEAPER 2
ENV UWSGI_PROCESSES 16
ENV NGINX_MAX_UPLOAD 0
ENV NGINX_WORKER_PROCESSES 1
ENV LISTEN_PORT 80
COPY start.sh /start.sh
RUN chmod +x /start.sh
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
RUN useradd -s /sbin/nologin -M nginx
RUN groupadd wwwgroup
ENTRYPOINT ["/entrypoint.sh"]
COPY ./app /app
WORKDIR /app
CMD ["/start.sh"]
