## Overview
Generally trying to use the official Dockerfiles. As can be seen, the approach is typically very straightforward. Using armhf docker images as base.

### Certbot
```
git clone https://github.com/certbot/certbot
cd certbot
sed -i 's/FROM python:2-alpine/FROM registry.gitlab.com\/rihardst\/cloud_project_infrastructure:python_arm_latest2/g' Dockerfile
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:certbot_arm
```
Using automatically built Python2 image as base image. Everything else the same.

### Java
```
git clone https://github.com/docker-library/openjdk
cd openjdk/8-jdk/alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
TAG=$(sed -n -e 's/ENV JAVA_VERSION //p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:openjdk_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:openjdk_arm_${TAG}
```
Meant to be used as Jenkins base image.

## Automated builds:
### Python3
```
git clone https://github.com/docker-library/python
cd $(ls -d python/*/ | sort | tail -n 1)alpine #Basically cd's to latest available version in repo
sed -i 's/FROM /FROM armhf\//g' Dockerfile
sed -i 's/\&\& gpg --keyserver/\&\& gpg-agent --daemon \&\& gpg --keyserver/g' Dockerfile
TAG=$(sed -n -e 's/ENV PYTHON_VERSION //p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG}
```
Added `gpg-agent --daemon` to fix `gpg: can't connect to the agent: IPC connect call failed` error.


### Python2
```
git clone https://github.com/docker-library/python
cd $(ls -d python/2*/ | sort | tail -n 1)alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
sed -i 's/\&\& gpg --keyserver/\&\& gpg-agent --daemon \&\& gpg --keyserver/g' Dockerfile
TAG=$(sed -n -e 's/ENV PYTHON_VERSION //p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:python_arm_${TAG}
```

### Netdata
```
git clone https://github.com/firehol/netdata
cd netdata
sed -i 's/FROM /FROM armhf\//g' Dockerfile
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:docker_netdata_arm .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:docker_netdata_arm
```

### PostgreSQL
```
git clone https://github.com/docker-library/postgres/
cd postgres/9.6/alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
TAG=$(sed -n -e 's/ENV PG_VERSION //p' Dockerfile)
docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com/rihardst/cloud_project_infrastructure
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:postgres_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:postgres_arm_${TAG}
```
Check out the login with gitlab-ci-token, which allows to push to the Gitlab container registry

### Nginx
```
git clone https://github.com/nginxinc/docker-nginx
cd docker-nginx/mainline/alpine
sed -i 's/FROM /FROM armhf\//g' Dockerfile
TAG=$(sed -n -e 's/ENV NGINX_VERSION //p' Dockerfile)
docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com/rihardst/cloud_project_infrastructure
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:nginx_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:nginx_arm_${TAG}
```

### Vault
Differences here from the original:
- Remove the provided docker-base commands, that download dumb-init and gosu
- Thus get dumb-init as written in https://github.com/Yelp/dumb-init/issues/138
- Download gosu armhf version from latest release on Github
- For sake of simplicity, do not download those in Container, but outside.
  - Because getting dumb-init depends on Debian `ar` executable.

```
git clone https://github.com/hashicorp/docker-vault
cd $(ls -d docker-vault/*/ | sort | tail -n 1) # Find the latest version
mkdir bin
wget http://ftp.us.debian.org/debian/pool/main/d/dumb-init/dumb-init_1.2.0-1_armhf.deb
ar -x *.deb
tar -xvf data.tar.xz
cp usr/bin/dumb-init bin/
wget $(curl -s https://api.github.com/repos/tianon/gosu/releases/latest | grep -e '\"browser_download_url.*armhf\"' | sed -n 's/"browser_download_url.* "//p' | sed 's/"//g') -O bin/gosu
chmod -R 755 bin/
sed -i 's#FROM .*#FROM registry.gitlab.com/rihardst/cloud_project_infrastructure:alpine_armhf_3.6.1#g' Dockerfile
sed -i 's/amd64/arm/g' Dockerfile
sed -i 's/gpg --recv-keys/gpg-agent --daemon \&\& gpg --keyserver ha.pool.sks-keyservers.net --recv-keys/g' Dockerfile
sed -i '/docker-base/ d' Dockerfile
sed -i '/cp bin\/gosu/ d' Dockerfile
sed -i '/adduser/a ADD bin/ /bin/' Dockerfile
TAG=$(sed -n -e 's/ENV VAULT_VERSION=//p' Dockerfile)
docker build -t registry.gitlab.com/rihardst/cloud_project_infrastructure:vault_arm_${TAG} .
docker push registry.gitlab.com/rihardst/cloud_project_infrastructure:vault_arm_${TAG}
```