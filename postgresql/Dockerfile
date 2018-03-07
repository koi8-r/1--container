# vpburchenya/1c-pgsql
FROM debian:buster-slim
LABEL maintainer="Burchenya Valentin <vp.burchenya@oz.net.ru>"


ENV PG_MAJOR 10

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


RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ buster-pgdg main" >> /etc/apt/sources.list.d/postgresql.list


RUN localedef -i ru_RU -c -f UTF-8 -A /etc/locale.alias ru_RU.UTF-8
ENV LANG ru_RU.UTF8


# apt-key adv --keyserver ipv4.pool.sks-keyservers.net --recv-keys "$KEY"
RUN export KEY='B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8' GNUPGHOME="$(mktemp -d)" && \
    ( gpg --keyserver ipv4.pool.sks-keyservers.net --recv-keys "$KEY" && \
      gpg --export "$KEY" > /etc/apt/trusted.gpg.d/apt.postgresql.org.gpg ) ; \
    rm -rf "$GNUPGHOME"


RUN apt-get update -qq && apt-get install --no-install-recommends -qy postgresql-${PG_MAJOR}

RUN apt-get purge -y --auto-remove ca-certificates wget gnupg dirmngr
RUN rm -rf /var/lib/apt/lists/*


# todo: try ram disk
RUN mkdir -p /var/run/postgresql/${PG_MAJOR}-main.pg_stat_tmp/
RUN chown postgres:postgres /var/run/postgresql/${PG_MAJOR}-main.pg_stat_tmp/
RUN chmod 2750 /var/run/postgresql/${PG_MAJOR}-main.pg_stat_tmp/


RUN echo "host all all 0.0.0.0/0 md5" >> /etc/postgresql/${PG_MAJOR}/main/pg_hba.conf
RUN ln -s /etc/postgresql/${PG_MAJOR}/main/postgresql.conf /var/lib/postgresql/${PG_MAJOR}/main/

# https://github.com/docker-library/postgres/blob/0aaaf2094034647a552f0b1ec63b1b0ec0f6c2cc/10/docker-entrypoint.sh
# ALTER USER postgres PASSWORD 'postgres';
# psql --command "CREATE USER pguser WITH SUPERUSER PASSWORD ${PG_PASSWD:-postgres};"
RUN (echo '#!/bin/bash' ; \
     echo 'exec "$@"' \
    ) >> /ep.sh
RUN chmod +x /ep.sh

ENTRYPOINT ["/ep.sh"]

VOLUME ["/var/lib/postgresql/"]
EXPOSE 5432


USER postgres
ENV PATH "$PATH:/usr/lib/postgresql/${PG_MAJOR}/bin/"
ENV PGDATA "/var/lib/postgresql/${PG_MAJOR}/main"
ENV PGOPTIONS="-c config_file=/etc/postgresql/${PG_MAJOR}/main/postgresql.conf -c listen_addresses=*"
CMD ["bash", "-c", "postgres ${PGOPTIONS}"]
