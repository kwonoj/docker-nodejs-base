# Base docker image
FROM antergos/archlinux-base-devel

# Set default node, npm version to install if not specified
ARG NODE_VERSION=8.9.1
ARG NPM_VERSION=5.6.0

# Update installed packages
RUN pacman --noconfirm -Syy && pacman --noconfirm -Su

# Install dependencies
RUN pacman --noconfirm -Sy \
  sudo \
  binutils \
  fakeroot \
  base-devel \
  python2 \
  alsa-lib \
  desktop-file-utils \
  gtk2 \
  glibc \
  clang \
  cmake \
  libnotify \
  libxtst \
  libxkbfile \
  libsecret \
  nss \
  gvfs \
  gconf \
  libgnome-keyring \
  libxss \
  curl \
  openssl \
  c-ares \
  http-parser \
  xorg-server-xvfb \
  pacman-contrib

RUN git --version

# Setting passwordless sudo
RUN echo "builder ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# makepkg disallow to run under root, create user named builder
RUN useradd -mr builder

ENV HOME=/home/builder
ENV TMPDIR=/home/builder/temp
USER builder

# Install packer for installing aur nvm package
RUN mkdir $TMPDIR \
  && cd $TMPDIR \
  && git clone https://aur.archlinux.org/nvm.git \
  && cd nvm \
  && makepkg \
  && sudo pacman --noconfirm -U nvm-*.pkg.tar.xz

# Set makepkg ext configuration to archive into .xz
ENV PKGEXT='.pkg.tar.xz'

ENV NVM_DIR=$HOME/.nvm

# Install arch pkg nodejs first
RUN mkdir -p /home/builder/node-tmp && cd /home/builder/node-tmp && \
  curl https://archive.archlinux.org/packages/n/nodejs/nodejs-$NODE_VERSION-1-x86_64.pkg.tar.xz > ./nodejs-$NODE_VERSION-1-x86_64.pkg.tar.xz && \
  curl https://archive.archlinux.org/packages/n/nodejs/nodejs-$NODE_VERSION-1-x86_64.pkg.tar.xz.sig > ./nodejs-$NODE_VERSION-1-x86_64.pkg.tar.xz.sig && \
  sudo pacman --noconfirm -U nodejs-$NODE_VERSION-1-x86_64.pkg.tar.xz

# Install node via nvm, bump up npm
RUN source /usr/share/nvm/init-nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default \
    && npm install -g npm@$NPM_VERSION

# add node and npm to path so the commands are available
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# reset user to root after makepkg completes
USER root

RUN node --version && npm --version
CMD echo 'done'
