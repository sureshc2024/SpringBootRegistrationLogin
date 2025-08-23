FROM mysql:9.4.0-oraclelinux9

RUN apt-get update && \
    apt-get install -y --only-upgrade openssl && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

COPY table.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
