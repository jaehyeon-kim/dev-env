FROM rocker/tidyverse:3.4.3
MAINTAINER Jaehyeon Kim <dottami@gmail.com>

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install openssh-server sudo

ADD set_root_pw.sh /set_root_pw.sh
ADD run.sh /run.sh
# prevent /bin/bash^M: bad interpreter: No such file or directory
RUN sed -i -e 's/\r$//' /set_root_pw.sh \
    && sed -i -e 's/\r$//' /run.sh \
    && chmod +x /*.sh

RUN mkdir -p /var/run/sshd && sed -i "s/UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g" /etc/ssh/sshd_config \
    && sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && touch /root/.Xauthority \
    && true

## Set a default user. Available via runtime flag `--user docker`
## Add user to 'staff' group, granting them write privileges to /usr/local/lib/R/site.library
## User should also have & own a home directory, but also be able to sudo
RUN useradd docker \
    && echo "docker:docker" | chpasswd \
    && mkdir /home/docker \
    && chown docker:docker /home/docker \
    && addgroup docker staff \
    && addgroup docker sudo \
    && true

EXPOSE 22
CMD ["/run.sh"]
