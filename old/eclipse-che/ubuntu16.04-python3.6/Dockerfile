FROM eclipse/ubuntu_python:2.7
MAINTAINER Jaehyeon Kim <dottami@gmail.com>

USER root

RUN apt-get update \
    && apt-get install -y software-properties-common

#### install python 3.6 and related
## https://askubuntu.com/questions/865554/how-do-i-install-python-3-6-using-apt-get/865569#865569
RUN add-apt-repository ppa:jonathonf/python-3.6 && apt-get update
RUN apt-get install -y python3.6 python3.6-dev python3.6-venv
RUN wget https://bootstrap.pypa.io/get-pip.py \
    && python3.6 get-pip.py \
    && pip3.6 install --upgrade pip

## install scipy dependencies
RUN apt-get install -y libopenblas-dev gfortran

USER user
