FROM jasonrivers/nagios:latest

LABEL maintainer="auri@ufscar.br, g4briel.4lves@gmail.com"

ENV NAGIOS_TIMEZONE="America/Sao_Paulo"

RUN apt update;apt install -y vim nano

ADD lojacfg/loja_virtual.cfg /opt/nagios/etc/objects/lojacfg/
ADD contacts.cfg /opt/nagios/etc/objects/

ADD nagios.cfg /opt/nagios/etc/

# To solve CRITICAL error on ping command
# https://checkmk.com/linux-knowledge/nagios-check-ping-could-not-interpret-output-ping-command
ENV LANG=""

EXPOSE 80
