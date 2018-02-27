# Base docker image
FROM ubuntu:xenial

# Set default node, npm version to install if not specified
ARG NODE_VERSION=8.9.1
ARG NPM_VERSION=5.6.0

# Bump up git to latest
RUN apt-get update && \
  apt-get install -y software-properties-common && \
  add-apt-repository ppa:git-core/ppa && \
  add-apt-repository ppa:snappy-dev/snapcraft-daily -y

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
    snapd \
    snapcraft \
    squashfs-tools \
    --no-install-recommends

RUN git --version

# installing node.js, from official node.js dockerfile setup (https://github.com/nodejs/docker-node/blob/5e80a80771f31b7f5d1327ea94830af57c1a17a2/7.9/Dockerfile#L6-L32)
# except inline npm 4 install (https://github.com/npm/npm/issues/15558#issuecomment-283331163), we're using a version of NPM that can consistently shrinkwrap

# gpg keys listed at https://github.com/nodejs/node#release-team
RUN set -ex \
  && for key in \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    56730D5401028683275BD23C23EFEFE93C4CFFFE \
    77984A986EBC2AA786BC0F66B01FBB92821C587A \
  ; do \
    gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
    gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
    gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
  done

# install node, then update npm to specified version
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
