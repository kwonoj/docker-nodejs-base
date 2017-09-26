# Base docker image
FROM ubuntu:xenial
MAINTAINER OJ Kwon <kwon.ohjoong@gmail.com>

# Set default node, npm version to install if not specified
ARG NODE_VERSION=7.9.0
ARG NPM_VERSION=4

# Bump up git to latest
RUN apt-get update && \
  apt-get install -y software-properties-common && \
  add-apt-repository ppa:git-core/ppa

# Install base dependencies
RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    sudo \
    build-essential \
    ca-certificates \
    curl \
    git \
    fakeroot \
    clang \
    cmake \
    libgnome-keyring-dev \
    libsecret-1-dev \
    libnss3 \
    libgtk2.0-dev \
    libnotify-dev \
    libdbus-1-dev \
    libxrandr-dev \
    libxext-dev \
    libxss-dev \
    libgconf2-dev \
    libasound2-dev \
    libcap-dev \
    libcups2-dev \
    libxtst-dev \
    libcurl4-openssl-dev \
    libxkbfile-dev \
    curl \
    wget \
    xvfb \
    snapcraft \
    --no-install-recommends

RUN git --version

# installing node.js, from official node.js dockerfile setup (https://github.com/nodejs/docker-node/blob/5e80a80771f31b7f5d1327ea94830af57c1a17a2/7.9/Dockerfile#L6-L32)
# except inline npm 4 install (https://github.com/npm/npm/issues/15558#issuecomment-283331163), we're using a version of NPM that can consistently shrinkwrap

# gpg keys listed at https://github.com/nodejs/node#release-team
RUN set -ex \
  && for key in \
    9554F04D7259F04124DE6B476D5A82AC7E37093B \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
    56730D5401028683275BD23C23EFEFE93C4CFFFE \
  ; do \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key" || \
    gpg --keyserver pgp.mit.edu --recv-keys "$key" || \
    gpg --keyserver keyserver.pgp.com --recv-keys "$key" ; \
  done

# install node, then update npm to 4.0.5
RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && npm install -g npm@$NPM_VERSION

# bump up gcc to 4.9
RUN apt-get install -y software-properties-common --no-install-recommends
RUN add-apt-repository ppa:ubuntu-toolchain-r/test
RUN apt-get update
RUN apt-get install -y g++-4.9
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 60 --slave /usr/bin/g++ g++ /usr/bin/g++-4.9

# Setting passwordless sudo
RUN echo "builder ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# add user `builder` for jobs doesn't run under root
RUN useradd -mr builder
ENV HOME=/home/builder
ENV TMPDIR=/home/builder/temp

RUN node --version && npm --version
CMD echo 'done'
