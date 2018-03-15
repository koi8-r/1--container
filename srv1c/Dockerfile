# vpburchenya/srv1c
FROM debian:stretch-slim
LABEL maintainer="Burchenya Valentin <vp.burchenya@oz.net.ru>"


ENV PG_MAJOR 9.6


ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update -qq
RUN apt-get install --no-install-recommends -qy \
    procps \
    inetutils-tools \
    net-tools \
    iproute2 \
    iputils-ping \
    ca-certificates wget \
    gnupg dirmngr \
    locales \
    && true


# locale-gen en_US ru_RU
# locale-gen ru_RU.UTF-8
# dpkg-reconfigure locales


# locale-gen work
RUN localedef -i ru_RU -c -f UTF-8 -A /etc/locale.alias ru_RU.UTF-8
RUN localedef -i en_US -c -f UTF-8 -A /etc/locale.alias en_US.UTF-8
ENV LANG ru_RU.UTF-8

# deb postinst script during cluster init read locale from
# update-locale work
RUN echo "LANG=\"ru_RU.UTF-8\"" >> /etc/default/locale


RUN echo "deb http://1c.postgrespro.ru/deb stretch main" > /etc/apt/sources.list.d/postgrespro-1c.list
RUN wget -q -O - "http://1c.postgrespro.ru/keys/GPG-KEY-POSTGRESPRO-1C" | apt-key add -

RUN apt-get update -qq && \
    apt-get install --no-install-recommends -qy postgresql-contrib-pro-1c-${PG_MAJOR}

# 1c server runtime dependencies
RUN apt-get install --no-install-recommends -qy \
    libfontconfig1 \
    imagemagick \
    ttf-mscorefonts-installer \
    && true

RUN apt-get purge -y --auto-remove ca-certificates wget gnupg dirmngr
RUN rm -rf /var/lib/apt/lists/*


RUN mkdir -p "/tmp/1c_dist"
COPY ./repo/* "/tmp/1c_dist/"

RUN dpkg -i /tmp/1c_dist/1c-enterprise83-common_8.3.9-1818_amd64.deb
RUN dpkg -i /tmp/1c_dist/1c-enterprise83-server_8.3.9-1818_amd64.deb
RUN rm -r "/tmp/1c_dist"


# docker run -ti --rm -p 1560-1591:1560-1591/tcp -p 1540-1541:1540-1541/tcp --hostname goodwin.murmangaz.lan vpburchenya/srv1c bash
EXPOSE 1540-1541 1560-1591

# ENV KRB5_KTNAME
CMD [ "/opt/1C/v8.3/x86_64/ragent" ]

# -d /var/lib/srv1c -port 1540 -regport 1541 -range 1560:1691 -seclev 0 -debug

# libfreetype6 (dev)
# xls:
#   - libgsf-1 (dev)
#   - texlive-base (dev)
#   - libglib2.0 (dev)
# libkrb5
# libgssapi-krb5


# - libssl0.9.8
# - libossp-uuid16
# - libxslt1.1
# - libicu57
# - libt1 t1utils
# - unixodbc
# - libgfs-1.3-2
