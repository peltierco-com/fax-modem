FROM ubuntu:bionic AS builder
ENV DEBIAN_FRONTEND=noninteractive
ENV PTLIB_VERSION="2.18.6"
ENV GIT_PTLIB_VERSION="2_18_6"
ENV OPAL_VERSION="3.18.6"
ENV GIT_OPAL_VERSION="3_18_6"
ENV T38MODEM_VERSION="4.6.2"

RUN apt-get update \
	&& apt-get upgrade -y \
	&& apt install -y g++ git make autoconf libpcap-dev libexpat1-dev libssl-dev libsasl2-dev libldap2-dev unixodbc-dev liblua5.3-dev libv8-dev libncurses-dev libsdl2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev pkg-config libsrtp2-dev libgsm1-dev libspeex-dev libopus-dev libx264-dev libvpx-dev libtheora-dev libspandsp-dev dahdi \
	&& git clone --depth 1 -b "v${GIT_PTLIB_VERSION}" git://git.code.sf.net/p/opalvoip/ptlib \
	&& git clone --depth 1 -b "v${GIT_OPAL_VERSION}" git://git.code.sf.net/p/opalvoip/opal \
	&& git clone --depth 1 -b "${T38MODEM_VERSION}" https://github.com/hehol/t38modem.git

RUN mkdir /builder
ENV PKG_CONFIG_PATH=/builder/lib/pkgconfig
ENV LD_LIBRARY_PATH=/builder/lib
ENV PTLIBPLUGINDIR=/builder/lib/ptlib-${PTLIB_VERSION}:/builder/lib/opal-${OPAL_VERSION}

WORKDIR /ptlib
RUN ./configure --prefix=/builder \
	&& make \
	&& make install

WORKDIR /opal
RUN ./configure --prefix=/builder \
	&& make \
	&& make install

WORKDIR /t38modem
RUN make

FROM ubuntu:18.04 AS runner
ENV PTLIB_VERSION="2.18.6"
ENV OPAL_VERSION="3.18.6"
ENV T38MODEM_VERSION="4.6.2"
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
	&& apt-get upgrade -y \
	&& apt-get install -y ca-certificates libssl-dev libsrtp2-dev libspandsp-dev \
	&& apt-get install -y libpcap-dev libexpat1-dev libssl-dev libsasl2-dev libldap2-dev \
		unixodbc-dev liblua5.3-dev libv8-dev libncurses-dev libsdl2-dev \
		libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev pkg-config \
		supervisor nano wget ser2net \
	&& apt-get clean -y \
	&& apt-get autoremove -y \
	&& apt-get autoclean -y \
	&& rm -rf /tmp/* \
	&& rm -rf /var/lib/apt/lists/*

COPY --from=builder /builder/lib/. /usr/local/lib/
COPY --from=builder /t38modem/t38modem /usr/local/bin/t38modem

# Supervisor cofig
COPY ./supervisord.conf /etc/supervisor/supervisord.conf

# ser2net cofig
COPY ./ser2net.conf /etc/ser2net.conf

ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
ENV LD_LIBRARY_PATH=/usr/local/lib
ENV PTLIBPLUGINDIR="/usr/local/lib/ptlib-${PTLIB_VERSION}:/usr/local/lib/opal-${OPAL_VERSION}"

ENV SIPADDR=sip.example.com
ENV SIPUSER=user
ENV SIPPASS=password

# Supervisor start
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]