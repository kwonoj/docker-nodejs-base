# docker-nodejs-base

This repo contains Dockerfiles of base linux image preconfigured node.js

# Publish image into docker hub (https://hub.docker.com/u/ojkwon/)

[Travis configuraiton](https://github.com/kwonoj/docker-nodejs-base/blob/6c538ccff20df307d20099333f9b18ea2d031acc/.travis.yml#L36-L39) in this repo does auto publish images when it's ready. Once PR goes green, create tag and push into upstream remote.

Tag versioning rules are `${shortCommitSha}-node${version}-npm${verision}`. If image has major dist bump, ensure to update docker image name via `DOCKER_REPO` env as well (https://github.com/kwonoj/docker-nodejs-base/blob/6c538ccff20df307d20099333f9b18ea2d031acc/.travis.yml#L18-L27)

Lastly, ensure creates release (https://github.com/kwonoj/docker-nodejs-base/releases) with corresponding commit / short description to track history, especially non-versioning changes (dist, new package install, etcs) should be logged.