FROM jasonrivers/nagios:latest

LABEL maintainer="auri@ufscar.br, g4briel.4lves@gmail.com"

ENV NAGIOS_TIMEZONE="America/Sao_Paulo"

RUN apt update
RUN apt install -y vim nano

ADD lojacfg/loja_virtual.cfg /opt/nagios/etc/objects/lojacfg/
ADD contacts.cfg /opt/nagios/etc/objects/

ADD nagios.cfg /opt/nagios/etc/

EXPOSE 80
