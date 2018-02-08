FROM rocker/r-apt:xenial
MAINTAINER Jaehyeon Kim <Jaehyeon.Kim@corelogic.com.au>

## User named `docker` is already created and available via runtime flag `--user docker` 
# The user is added to `staff` group, granting them write privileges to /usr/local/lib/R/site.library
# The user also has & owns a home directory (for rstudio or linked volumes to work properly).

## Install ubuntu packages
RUN apt-get update
# General packages
RUN apt-get install -y sudo curl git
# Visual Studio Code
RUN apt-get install -y --no-install-recommends unzip libgtk2.0-0 libgconf-2-4 libnss3 libasound2 \
    libxtst6 libnotify4 libxkbfile1 libsecret-1-0 libx11-xcb-dev
RUN curl -o vscode-amd64.deb -L https://go.microsoft.com/fwlink/?LinkID=760868 && dpkg -i vscode-amd64.deb

# for development
# ip=`ifconfig bridge100 | grep inet | awk '$1=="inet" {print $2}'`
# docker run --rm -it -v $PWD/root:/root -w /root -e DISPLAY=$ip:0 golang:latest

WORKDIR /home/docker
CMD su - docker -c "code -w ."
