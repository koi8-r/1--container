# vpburchenya/postgresql-1c
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
    apt-get install --no-install-recommends -qy postgresql-pro-1c-${PG_MAJOR}

RUN apt-get purge -y --auto-remove ca-certificates wget gnupg dirmngr
RUN rm -rf /var/lib/apt/lists/*


# todo: try ram disk
RUN mkdir -p /var/run/postgresql/${PG_MAJOR}-main.pg_stat_tmp/
RUN chown postgres:postgres /var/run/postgresql/${PG_MAJOR}-main.pg_stat_tmp/
RUN chmod 2750 /var/run/postgresql/${PG_MAJOR}-main.pg_stat_tmp/

RUN echo "host all all 0.0.0.0/0 md5" >> /etc/postgresql/${PG_MAJOR}/main/pg_hba.conf


# todo: resolve PGPASSWD inline and unset PGPASSWD env, change owner and perm of ep.sh to postgres and 400
RUN (echo '#!/bin/bash' ; \
     echo 'set -o pipefail' ; \
     echo -n "echo -e \"ALTER USER postgres PASSWORD '\${PGPASSWD:-postgres}' ;\\\n\"" ; \
     echo " | postgres --single -E -D \"\${PGDATA}\" -c config_file=\"\${PGCFG}\"" ; \
     echo ; \
     echo 'exec "$@"' \
    ) >> /ep.sh
RUN chmod +x /ep.sh


ENTRYPOINT ["/ep.sh"]
VOLUME ["/var/lib/postgresql/"]
EXPOSE 5432


USER postgres
ENV PATH "$PATH:/usr/lib/postgresql/${PG_MAJOR}/bin/"
ENV PGDATA "/var/lib/postgresql/${PG_MAJOR}/main"
ENV PGCFG="/etc/postgresql/${PG_MAJOR}/main/postgresql.conf"

# -c backslash_quote=safe_encoding
# -c escape_string_warning=off
# -c standart_conforming_strings=off
CMD [ "bash", "-c", "postgres -D ${PGDATA} -c config_file=${PGCFG} -c listen_addresses=*" ]
