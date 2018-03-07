# vpburchenya/1c
FROM debian:buster-slim
LABEL maintainer="Burchenya Valentin <vp.burchenya@oz.net.ru>"


ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update -qq
RUN apt-get install --no-install-recommends -qy \
    procps \
    inetutils-tools \
    net-tools \
    iproute2 \
    && true


RUN mkdir -p "/tmp/1c_dist"
COPY ./repo/* "/tmp/1c_dist/"

RUN dpkg -i /tmp/1c_dist/1c-enterprise83-common_8.3.9-1818_amd64.deb
RUN dpkg -i /tmp/1c_dist/1c-enterprise83-server_8.3.9-1818_amd64.deb
RUN rm -r "/tmp/1c_dist"


# docker run -ti --rm -p 1560-1591:1560-1591/tcp -p 1540-1541:1540-1541/tcp vpburchenya/1c bash
EXPOSE 1540-1541 1560-1591

CMD [ "/etc/init.d/srv1cv83", "start" ]
