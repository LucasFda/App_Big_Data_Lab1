FROM postgres:16-alpine

ENV POSTGRES_DB=db \
    POSTGRES_USER=user \
    POSTGRES_PASSWORD=pwd

COPY *.sql /docker-entrypoint-initdb.d