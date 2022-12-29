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

RUN apt-get update && \
    apt-get -y install curl wget
RUN apt-get update && apt-get install -y software-properties-common

########################################################
# Favoring non interactive installation
########################################################
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get --yes update
RUN apt-get --yes install apt-utils 
RUN apt-get --yes install git 
RUN apt-get --yes install sudo 
RUN apt-get --yes install g++ 
RUN apt-get --yes install make 
RUN apt-get --yes install gnupg 
RUN apt-get --yes install gdb 
RUN apt-get --yes install cmake
RUN apt-get --yes install x11-apps 
RUN apt-get --yes install mesa-utils 
RUN apt-get --yes install nano 
RUN apt-get --yes install emacs
RUN apt-get --yes install lsb-release

RUN apt-get update
# RUN apt-get --yes install terminator 
# (failed to download modemanager)
RUN apt-get --yes install gnuplot 
# (failed to download)

RUN echo "-----------------------------------------"
RUN echo " INSTALL DEPENDENCIES"
RUN echo "-----------------------------------------"

RUN apt-get --yes install libeigen3-dev
RUN apt-get --yes install coinor-libipopt-dev
RUN apt-get --yes install libompl-dev ompl-demos
RUN apt-get --yes install python3
RUN apt-get --yes install python3-pip

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

# 6) install pybullet
RUN pip3 install pybullet

# [/SSH IGNORE]