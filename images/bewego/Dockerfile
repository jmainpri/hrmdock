# [SSH IGNORE]

FROM nvidia/opengl:1.0-glvnd-runtime-ubuntu20.04

MAINTAINER Jim Mainprice <mainprice@gmail.com>

################################################################################
# This Dockerfile is also used to generate the official image install ssh 
# script. (see desktop_update folder in amd-clmc/official_images)
# The [SSH IGNORE*]  [/SSH IGNORE*] ;
# [SSH ONLY*] [/SSH ONLY*] tags are here to indicate
# what will go in the official ssh install script and what will not
################################################################################

# [/SSH IGNORE]

# RUN echo "${HTTP_PROXY}"
# RUN export HTTP_PROXY=""
# RUN export HTTPS_PROXY=""
# RUN echo "${HTTP_PROXY}"

################################################################################
# We add all other repositories.
################################################################################

RUN apt-get update && \
    apt-get -y install curl wget
RUN apt-get update && apt-get install -y software-properties-common

########################################################
# Favoring non interactive installation
########################################################
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get --yes update
RUN apt-get --yes install \
    apt-utils git sudo g++ make gnupg gdb cmake \
    x11-apps mesa-utils \
    nano emacs terminator lsb-release gnuplot

RUN echo "-----------------------------------------"
RUN echo " INSTALL DEPENDENCIES"
RUN echo "-----------------------------------------"

RUN apt-get --yes install \
    libeigen3-dev \
    coinor-libipopt-dev \
    python3-pip

# [SSH IGNORE]

# RUN export MOVE3D_INSTALL_DIR=/workspace/move3d/install

##########################################################
# This is then mounted by the user to a shared folder

RUN mkdir -p /workspace

##########################################################
# INSTALL BEWEGO

RUN mkdir -p /ustuttgart && cd /ustuttgart
    
RUN git clone https://github.com/humans-to-robots-motion/pyrieef.git  && \
    cd pyrieef && \
    pip install -r requirements.txt && \
    cd -

RUN git clone --recursive git@github.com:jmainpri/bewego-car.git && \
    cd bewego && \
    bash scripts/install_dependencies.sh && \
    mkdir -p build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo \
             -DWITH_IPOPT=true \
             -DWITH_OSQP=true

WORKDIR /workspace
# [/SSH IGNORE]