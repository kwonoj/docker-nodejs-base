# Base docker image
FROM fedora:21

# Set default node, npm version to install if not specified
ARG NODE_VERSION=8.9.1
ARG NPM_VERSION=5.6.0

# Install base dependencies
RUN rpm --rebuilddb; yum install -y \
    sudo \
    make \
    gcc \
    gcc-c++ \
    glibc-devel \
    rpmdevtools \
    wget \
    clang \
    cmake \
    libgnome-keyring-devel \
    libnss3 \
    libsecret-devel \
    gtk2-devel \
    libnotify-devel \
    dbus-devel \
    libXrandr-devel \
    libXext-devel \
    GConf2-devel \
    alsa-lib-devel \
    libcap-devel \
    libcurl-devel \
    libxkbfile-devel \
    curl \
    cups-devel \
    libXScrnSaver-devel \
    xorg-x11-server-Xvfb \
    libXtst-devel \
    gnupg \
    curl-devel \
    expat-devel \
    gettext-devel \
    openssl-devel \
    zlib-devel \
    perl-ExtUtils-MakeMaker

# Bump up git to latest

RUN cd /usr/src && \
  wget https://www.kernel.org/pub/software/scm/git/git-2.14.1.tar.gz && \
  tar xzf git-2.14.1.tar.gz && \
  cd git-2.14.1 && \
  make prefix=/usr/local/git all && \
  make prefix=/usr/local/git install

ENV PATH /usr/local/git/bin:$PATH

RUN git --version

# installing node.js, from official node.js dockerfile setup (https://github.com/nodejs/docker-node/blob/5e80a80771f31b7f5d1327ea94830af57c1a17a2/7.9/Dockerfile#L6-L32)

# gpg keys listed at https://github.com/nodejs/node#release-team
RUN set -ex \
  && for key in \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    77984A986EBC2AA786BC0F66B01FBB92821C587A \
    8FCCA13FEF1D0C2E91008E09770F7A9A5AE15600 \
    4ED778F539E3634C779C87C6D7062848A1AB005C \
    A48C2BEE680E841632CD4E44F07496B3EB3C1762 \
    B9E2F5981AA6E0CD28160D9FF13993A75599653C \
  ; do \
    gpg --batch --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
    gpg --batch --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
    gpg --batch --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
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

# Setting passwordless sudo
RUN echo "builder ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# add user `builder` for jobs doesn't run under root
RUN useradd -mr builder
ENV HOME=/home/builder
ENV TMPDIR=/home/builder/temp

RUN node --version && npm --version
CMD echo 'done'
