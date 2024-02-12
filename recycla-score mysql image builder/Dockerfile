FROM mysql

ADD dump_recycle.sql /docker-entrypoint-initdb.d

RUN chown -R mysql:mysql /docker-entrypoint-initdb.d/

EXPOSE 3306
