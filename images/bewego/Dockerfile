# [SSH IGNORE]

FROM nvidia/opengl:1.0-glvnd-runtime-ubuntu20.04

MAINTAINER Jim Mainprice <mainprice@gmail.com>

# This Dockerfile requires BuildKit
# https://docs.docker.com/build/buildkit/
# 
#    DOCKER_BUILDKIT=1

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

RUN apt-get --yes update && \
    apt-get --yes install curl wget
RUN apt-get update && apt-get install --yes software-properties-common

########################################################
# Favoring non interactive installation
########################################################
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get --yes update &&
    apt-utils \
    git \
    sudo \
    g++ \
    make \
    gnupg \
    gdb \
    cmake \
    x11-apps \
    mesa-utils \
    nano \
    emacs \
    lsb-release

RUN apt-get update
# RUN apt-get --yes install terminator 
# (failed to download modemanager)
RUN apt-get --yes install gnuplot 
# (failed to download)

RUN echo "-----------------------------------------"
RUN echo " INSTALL DEPENDENCIES"
RUN echo "-----------------------------------------"

RUN apt-get --yes update && \
    apt-get --yes install libeigen3-dev \
    coinor-libipopt-dev \
    libompl-dev ompl-demos \
    python3 \
    python3-pip \
    freeglut3-dev

# [SSH IGNORE]

##########################################################
# This is then mounted by the user to a shared folder

RUN mkdir -p /workspace
RUN mkdir -p /ssh

##########################################################
# INSTALL BEWEGO

RUN mkdir -p /ustuttgart
    
# 1) install pyrieef
RUN cd /ustuttgart && \
    git clone https://github.com/humans-to-robots-motion/pyrieef.git && \
    cd pyrieef && \
    pip3 install -r requirements.txt

# 2) add github to known hosts
RUN mkdir -p -m 0600 ~/.ssh && ssh-keyscan github.com >> ~/.ssh/known_hosts

# 3) clone private bewego
RUN --mount=type=ssh cd /ustuttgart && \
    git clone --recursive git@github.com:jmainpri/bewego-car.git bewego

# 4) clone from github, build and install dependencies 
RUN bash /ustuttgart/bewego/scripts/install_dependencies.sh

# 5) setup environement for build
ENV BEWEGO_DEP_INSTALL=/ustuttgart/bewego/dependencies/install
ENV LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${BEWEGO_DEP_INSTALL}/lib
ENV LIBRARY_PATH=${LIBRARY_PATH}:${BEWEGO_DEP_INSTALL}/lib

# 5) configure bewego
RUN cd /ustuttgart/bewego && \
    mkdir -p build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo \
             -DWITH_GTEST_DOWNLOAD=true \
             -DWITH_IPOPT=true \
             -DWITH_OSQP=true \
             -DWITH_OMPL=true

# 5) build bewego
RUN cd /ustuttgart/bewego && cd build && \
    echo "LIBRARY_PATH : $LIBRARY_PATH" && \
    make -j$(nproc)

# 6) install python viz
RUN pip3 install pybullet
RUN pip3 install pyglet==1.5.27

# 7) install assets
RUN cd /ustuttgart && \
    git clone https://github.com/humans-to-robots-motion/pybullet_robots.git

# [/SSH IGNORE]