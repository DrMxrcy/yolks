FROM  --platform=$TARGETOS/$TARGETARCH ubuntu:22.04

LABEL author="Daniel Barton" maintainer="danny6167@gmail.com"

ENV   DEBIAN_FRONTEND noninteractive

## add container user
RUN   useradd -m -d /home/container -s /bin/bash container

## Update base packages
RUN			apt update \
	&& apt upgrade -y

## Install dependencies
RUN          apt install -y libc++-dev liblzo2-2 cabextract net-tools libatomic1 libsdl1.2debian libsdl2-2.0-0 \
	libfontconfig libicu67 icu-devtools libunwind8 libssl-dev sqlite3 libsqlite3-dev libmariadbclient-dev-compat libduktape205 ffmpeg apt-transport-https software-properties-common ca-certificates \
	libz-dev rapidjson-dev tzdata libevent-dev libzip4 libsdl2-mixer-2.0-0 libsdl2-image-2.0-0 libgdiplus

RUN         dpkg --add-architecture armhf \
	&& apt update -y \
	&& apt upgrade -y \
	&& apt install -y build-essential cmake gcc-arm-linux-gnueabihf git libc6:armhf libncurses5:armhf libstdc++6:armhf libpulse0 locales git wget curl tar zip unzip binutils xz-utils gnupg2 iproute2 gettext libnss-wrapper

## configure locale
RUN   update-locale lang=en_US.UTF-8 \
	&&   dpkg-reconfigure --frontend noninteractive locales

## Install box64
RUN         wget https://ryanfortner.github.io/box64-debs/box64.list -O /etc/apt/sources.list.d/box64.list \
	&& wget -qO- https://ryanfortner.github.io/box64-debs/KEY.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/box64-debs-archive-keyring.gpg \
	&& apt update && apt install box64-rpi4arm64 -y

## Install box86
RUN 		wget https://ryanfortner.github.io/box86-debs/box86.list -O /etc/apt/sources.list.d/box86.list \
	&& wget -qO- https://ryanfortner.github.io/box86-debs/KEY.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/box86-debs-archive-keyring.gpg \
	&& apt update && apt install box86-rpi4arm64 -y

ENV         USER=container HOME=/home/container
WORKDIR     /home/container



## Prepare NSS Wrapper for the entrypoint as a workaround for Valheim requiring a valid UID
ENV         NSS_WRAPPER_PASSWD=/tmp/passwd NSS_WRAPPER_GROUP=/tmp/group
RUN         touch ${NSS_WRAPPER_PASSWD} ${NSS_WRAPPER_GROUP} \
	&& chgrp 0 ${NSS_WRAPPER_PASSWD} ${NSS_WRAPPER_GROUP} \
	&& chmod g+rw ${NSS_WRAPPER_PASSWD} ${NSS_WRAPPER_GROUP}
ADD         passwd.template /passwd.template


COPY  ./entrypoint.sh /entrypoint.sh
CMD   [ "/bin/bash", "/entrypoint.sh" ]
