# Base docker image
FROM antergos/archlinux-base-devel
MAINTAINER OJ Kwon <kwon.ohjoong@gmail.com>

# Set default node, npm version to install if not specified
ARG NODE_VERSION=7.9.0
ARG NPM_VERSION=4

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
  glibc \
  libxss \
  curl \
  openssl \
  c-ares \
  http-parser \
  xorg-server-xvfb \
  nodejs

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

# Install node and npm
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
