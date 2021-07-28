FROM centos:7

LABEL maintainer="moghi@github.com"

# Prepare yum
RUN rpmkeys --import file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 && \
    yum -y update && \
    yum clean all

# Install Java
# RUN yum -y install java-1.8.0-openjdk-devel
# RUN yum -y install java-11-openjdk-devel
# RUN echo $(java -version)

# Import GPG-Key
# https://www.redhat.com/sysadmin/rpm-gpg-verify-packages
# RUN rpm --import RPM-GPG-KEY-EPEL-8

# Install Node
# variables to download the correct version of node
ENV NODEREPO pub_16.x
# centos is an enterprise-linux-type
ENV DIST_TYPE el
ENV DIST_VERSION 7
ENV DIST_ARCH x86_64

RUN groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

# Download node to tmp
# Result I want: https://rpm.nodesource.com/pub_16.x/el/7/x86_64/nodesource-release-el7-1.noarch.rpm
WORKDIR /tmp/
RUN curl -fsSLO "https://rpm.nodesource.com/${NODEREPO}/${DIST_TYPE}/${DIST_VERSION}/${DIST_ARCH}/nodesource-release-${DIST_TYPE}${DIST_VERSION}-1.noarch.rpm"
