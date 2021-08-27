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
ARG NODEREPO
ENV NODEREPO=${NODEREPO:-pub_16.x}

# centos is an enterprise-linux-type
ARG DIST_TYPE
ENV DIST_TYPE=${DIST_TYPE:-el}

ARG DIST_VERSION
ENV DIST_VERSION=${DIST_VERSION:-7}

ARG DIST_ARCH
ENV DIST_ARCH=${DIST_ARCH:-x86_64}
# The variable expansion technique with ARG and ENV allows you to pass arguments from the command line and persist them
# in the final image by leveraging the ENV instruction.

RUN groupadd --gid 1000 node \
    && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

# Download node to tmp
# Result I want: https://rpm.nodesource.com/pub_16.x/el/7/x86_64/nodesource-release-el7-1.noarch.rpm
WORKDIR /tmp/
RUN curl -fsSLO "https://rpm.nodesource.com/${NODEREPO}/${DIST_TYPE}/${DIST_VERSION}/${DIST_ARCH}/nodesource-release-${DIST_TYPE}${DIST_VERSION}-1.noarch.rpm"

## --nosignature because nodesource-release contains the signature!
RUN rpm -i --nosignature --force nodesource-release-${DIST_TYPE}${DIST_VERSION}-1.noarch.rpm \
    && yum -y install nodejs

# Install other stuff
RUN yum -y install \
    git \
    wget

# Install ssh
RUN yum -y install openssh-server \
    && sed -i 's|session  required  pam_loginuid.so|session  optional  pam_loginuid.so|g' /etc/pam.d/sshd \
    && mkdir -p /var/run/sshd

# Create a system group for jenkins-user
RUN groupadd -r jenkins-slaves -g 2000 \
    # Create a systems user for jenkins with a home directory in /opt
    && useradd -u 2000 -r -g jenkins-slaves -m -d /opt/jenkinsuser -c "Jenkins Slave User" jenkinsuser \
    && echo "root:somePassword" | chpasswd \
    && echo "jenkinsuser:somePassword" | chpasswd

RUN ssh-keygen -b 2048 -P "" -t rsa -f /etc/ssh/ssh_host_key \
    && ssh-keygen -b 2048 -P "" -t rsa -f /etc/ssh/ssh_host_rsa_key \
    && ssh-keygen -b 1024 -P "" -t dsa -f /etc/ssh/ssh_host_dsa_key \
    && ssh-keygen -P "" -t ed25519 -f /etc/ssh/ssh_host_ed25519_key \
    && ssh-keygen -P "" -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key

# config git and npm for jenkinsuser
USER jenkinsuser
COPY .npmrc /opt/jenkinsuser
RUN git config --global user.mail "some@email.com" \
    && git config --global user.name "some name"
USER root

# TODO: install maven
ARG MVN_BASE_URL
ENV MVN_BASE_URL=${MVN_BASE_URL:-https://apache.osuosl.org/maven}

ADD ${MVN_BASE_URL}/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz /opt/
ADD ${MVN_BASE_URL}/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz /opt/

## Unpack maven binaries and show the content of the directory
RUN cd /opt/ && ls *.tar.gz | xargs -i tar -xvf "{}" && ls -la


RUN ln -s /opt/apache-maven-3.5.4 /opt/apache-maven-3.5.x && \
    ln -s /opt/apache-maven-3.6.3 /opt/apache-maven-3.6.x && \
    ln -s /opt/apache-maven-3.6.x/bin/mvn /usr/bin/mvn

COPY ./mvn_settings.xml /opt/apache-maven-3.5.x/conf
COPY ./mvn_settings.xml /opt/apache-maven-3.6.x/conf

ENV MAVEN_HOME /opt/apache-maven-3.6.x
ENV MAVEN_CONFIG "${USER_HOMEDIR}/.m2"

RUN rm -rf /opt/*.tar.gz && \
    mvn --version

# TODO: Install Tomcat

# TODO: Install latest chrome driver
# TODO: Install google chrome
# TODO: Install latest gecko driver
# TODO: Add certificates

# run sshd in foreground (not detached, that why the option is D)
CMD ["/usr/sbin/sshd", "-D"]
