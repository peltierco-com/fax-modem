# Ubuntu 18.04
FROM ubuntu:bionic
ENV DEBIAN_FRONTEND=noninteractive

# Update
RUN apt-get update && apt-get upgrade -y

# Install packages 
RUN apt-get install -y \
    supervisor nano wget ser2net

# t38modem
COPY ./t38 /tmp
RUN dpkg -i /tmp/t38modem_2.0.0-4build3_amd64.deb || apt-get install -f -y

# Supervisor cofig
COPY ./supervisord.conf /etc/supervisor/supervisord.conf

# ENV SIPADDR=sip.example.com
# ENV SIPUSER=user
# ENV SIPPASS=password

# Supervisor start
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]