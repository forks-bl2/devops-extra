FROM jasonrivers/nagios:latest

LABEL maintainer="auri@ufscar.br, g4briel.4lves@gmail.com"

ENV NAGIOS_TIMEZONE="america/sao_paulo"

RUN apt update
RUN apt install -y vim nano postfix mailutils libsasl2-2 ca-certificates libsasl2-modules

ADD lojacfg/loja_virtual.cfg /opt/nagios/etc/objects/lojacfg/
ADD contacts.cfg /opt/nagios/etc/objects/
#ADD main.cf /etc/postfix/
#ADD sasl_passwd /etc/postfix/sasl/
#ADD sasl_passwd.db /etc/postfix/sasl/

ADD nagios.cfg /opt/nagios/etc/

#RUN chown -R root:postfix /etc/postfix/sasl
#RUN chmod 750 /etc/postfix/sasl
#RUN chmod 640 /etc/postfix/sasl/sasl_passwd*

EXPOSE 80