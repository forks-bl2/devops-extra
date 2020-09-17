FROM mysql:5.6

LABEL maintainer="auri@ufscar.br, g4briel.4lves@gmail.com"

ENV MYSQL_ROOT_PASSWORD secret
ENV MYSQL_DATABASE loja_schema
ENV MYSQL_USER loja
ENV MYSQL_PASSWORD=lojasecret

COPY schema.sql /docker-entrypoint-initdb.d

EXPOSE 3306