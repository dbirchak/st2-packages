# Stackstorm packages build environment

[![Circle CI Build Status](https://circleci.com/gh/StackStorm/st2-packages/tree/master.svg?style=shield)](https://circleci.com/gh/StackStorm/st2-packages)
[![Go to Docker Hub](https://img.shields.io/badge/Docker%20Hub-%E2%86%92-blue.svg)](https://hub.docker.com/r/stackstorm/)
[![Download deb/rpm](https://img.shields.io/badge/Download-deb/rpm-blue.svg)](https://packagecloud.io/StackStorm/)


## Highlights

 - **Docker based**. Leveraging docker it's possible to deliver packages for any OS distro in a fast and reliable way.
 - [Rake](https://github.com/ruby/rake) + [sshkit](https://github.com/capistrano/sshkit)-based execution enables easy configuration via **simple DSL** and brings **parallel task processing** out of the box.
 - **Test-driven workflow**. Artifacts built are not only available for any enabled OS distro but at the same time tested on a bunch of platforms, providing feedback such as can be installed, services can start up, operations can be executed etc.

## Overview

Packages build environment is a *multi-container docker* application defined and managed with [docker-compose](https://github.com/docker/compose). It consists of four types of containers:

 - **Packaging runner** (https://quay.io/stackstorm/packagingrunner) - the main entry point, package build and test processing controller container.
 - **Build environment** (https://quay.io/stackstorm/packagingenv) - container where actual artifacts build takes place. It's used to bring up the build environment specific for OS distro. This means that different containers are available such as *packagingenv:centos6*, *packagingenv:wheezy* correspondingly for CentOS 6 and Debian Wheezy.
 - **Test runner** (https://quay.io/dennybaa/droneunit) - containers where built artifacts are tested, i.e. *artifacts are installed, configuration is written and tests are performed*.
 - **Services** - these are different containers required for testing such as *rabbitmq, mongodb and postgresql*

The Packages build environment compose application brings a self-sufficient pipeline to deliver ready to use packages.

# Usage

It's very simple to invoke the whole build-test pipeline. First just make sure that [docker-compose.yml](docker-compose.yml) has your distro specification, after that issue the following commands:

```shell
# (Optional) First clean out previous build containers
docker-compose kill
docker-compose rm -f

# To build packages for debian wheezy (--rm will wipe packaging runner container. All others will remain active).
docker-compose run --rm wheezy
```

Execution takes a while, so grab a cup of tea or coffee and wait until it finishes. When build and test processes succeed, you'll find the StackStorm packages in `/tmp/st2-packages` on your host machine:

```shell
ls -l1 | grep ".deb$"
-rw-r--r-- 1 root root 30872652 Feb  9 18:32 st2_1.4dev-1_amd64.deb
-rw-r--r-- 1 root root 31582068 Feb  9 18:32 st2mistral_1.3.0-1_amd64.deb
```

## Manual testing inside the docker environment

After the build and test stages are finished all docker containers remain active, so you are welcome to do more in-depth testing if desired. To do so simply run:

```
docker ps
# Find the required testing container
# In our case it will be st2packages_wheezytest_1

# Simply exec to docker
docker exec -it st2packages_wheezytest_1 bash
```

Once done, you are inside the testing environment where all services are up and running. Don't forget to do (after exec):

```
export TERM=xterm
```
At this point you can do any manual testing which is required.


# Installation
Current community packages are hosted on https://packagecloud.io/StackStorm. For detailed instructions how install st2 and perform basic configuration follow these instructions:
- [Ubuntu/Debian](https://docs.stackstorm.com/install/deb.html)
- [RHEL7/CentOS7](https://docs.stackstorm.com/install/rhel7.html)
- [RHEL6/CentOS6](https://docs.stackstorm.com/install/rhel6.html)


# License and Authors

* Author:: StackStorm (st2-dev) (<info@stackstorm.com>)
* Author:: Denis Baryshev (<dennybaa@gmail.com>)
