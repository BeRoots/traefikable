# syntax=docker/dockerfile:1

##
# Debian 10 (buster) amd64 Created FROM debian:buster-20210511-slim
##
FROM debian@sha256:33965bf1eaadb19ce2f9396595c4a669e3e04c1ab8cc073b8929f529c58404bb
LABEL maintainer="Dev <dev@example.localhost>" description="Devtools Dependencies Container on Debian 10 (buster)" version="1.0"


##
# DEFAULT SHELL
##
SHELL ["/bin/sh", "-c"],


##
# OVERRIDABLE ARGS VARIABLES (A good security practice is to not log the build command in your bash history)
##
ARG ROOT_PASSWD="ChangeMe!"
ARG USER_LOGIN="dev"
ARG USER_GROUP="dev"
ARG USER_UID="1000"
ARG USER_GID="1000"
ARG USER_PASSWD="ChangeMe!"
ARG USER_NAME="developer"
ARG USER_ROOM=""
ARG USER_WPHONE=""
ARG USER_HPHONE=""
ARG USER_OTHER="No more informations about ${USER_NAME}"
ARG SYS_TZ="Europe/Paris"


##
# NOT OVERRIDABLE ARGS VARIABLES
##
ARG DEBIAN_FRONTEND="noninteractive"


##
# ENVIRONMENT VARIABLES
##
# Default to supporting utf-8
ENV HISTSIZE=1000
ENV HISTFILESIZE=1000
ENV TZ=${SYS_TZ}
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

ENV OPENSSL_EMAIL_ADDRESS="/emailAddress=dev@example.localhost"
ENV OPENSSL_SUBJ="/C=FR/ST=France/L=Rhône-Alpes Auvergne/O=Sebastien Deschamps/OU=development"
ENV OPENSSL_CA="${OPENSSL_SUBJ}/CN=fake-CA${OPENSSL_EMAIL_ADDRESS}"
ENV OPENSSL_SERVER="${OPENSSL_SUBJ}/CN=fake-server${OPENSSL_EMAIL_ADDRESS}"
ENV OPENSSL_CLIENT="${OPENSSL_SUBJ}/CN=fake-client${OPENSSL_EMAIL_ADDRESS}"


##
# 1) Root COPY Stage: COPY Command file
##
COPY gencerts.sh /opt


##
# 2) Root Stage:
#
# INFOS: This is the Container Administrator Stage. This stage is implemented by the maintainer only and provide a full devtool stack
#        for PHP oriented project.
#
# /!\ IMPORTANT: DON'T CHANGE ANYTHING ON THIS STAGE. CONTACT THE MAINTAIRNER FOR ANY CHANGES REQUEST.
#
#    - Set Root BASH History Log
#    - Change root password (overridable by the user through the docker-compose build used on install-env.* scripts)
#    - Create user/group dev that match 1000:1000 by default (overridable by the user through the docker-compose build used on install-env.* scripts)
#    - Change dev user password (overridable by the user through the docker-compose build used on install-env.* scripts)
#    - Set system clock from UTC hardware clock (overridable by the user through the docker-compose build used on install-env.* scripts)
#    - Install openssl
#    - Set gencerts.sh permissions
#    - Create the workdir folder
#    - Clean APT
##
RUN set -eux && \
echo "\e[36m====================\n\n[DEVTOOLS CONTAINER BUILD STAGE] Add/Set users\n\n====================\e[0m" && \
echo "root:${ROOT_PASSWD}" | chpasswd root && \
addgroup --gid ${USER_GID} ${USER_LOGIN} && \
adduser ${USER_LOGIN} --uid ${USER_UID} --gid ${USER_GID} --gecos "${USER_NAME},${USER_ROOM},${USER_WPHONE},${USER_HPHONE},${USER_OTHER}" --disabled-login && \
echo "${USER_LOGIN}:${USER_PASSWD}" | chpasswd ${USER_LOGIN} && \
echo "\e[36m====================\n\n[DEVTOOLS CONTAINER BUILD STAGE] Set TIMEZONE: ${SYS_TZ}\n\n====================\e[0m" && \
[ $(which timedatectl) ] && timedatectl set-timezone ${SYS_TZ} || rm -rf /etc/localtime && ln -s /usr/share/zoneinfo/${SYS_TZ} /etc/localtime && \
apt update && \
echo "\e[36m====================\n\n[DEVTOOLS CONTAINER BUILD STAGE] APT install: openssl\n\n====================\e[0m" && \
apt install -y openssl && \
echo '\e[36m====================\n\n[DEVTOOLS CONTAINER BUILD STAGE] CMD SCRIPT: set script permissions \n\n====================\e[0m' && \
chown ${USER_LOGIN}:${USER_GROUP} /opt/gencerts.sh && \
chmod +x /opt/gencerts.sh && \
echo '\e[36m====================\n\n[DEVTOOLS CONTAINER BUILD STAGE] WORKDIR: create app folder\n\n====================\e[0m' && \
mkdir /opt/certs && \
chown -R ${USER_LOGIN}:${USER_GROUP} /opt/certs && \
echo '\e[36m====================\n\n[DEVTOOLS CONTAINER BUILD STAGE] APT: clean\n\n====================\e[0m' && \
apt autoremove -y && \
apt clean && \
rm -rf /var/lib/apt/lists/*

##
# 3) Container Configuration
##
VOLUME /opt/certs

WORKDIR /opt/certs

USER ${USER_LOGIN}

CMD /opt/gencerts.sh
