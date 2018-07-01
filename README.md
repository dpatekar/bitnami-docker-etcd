[![CircleCI](https://circleci.com/gh/bitnami/bitnami-docker-etcd/tree/master.svg?style=shield)](https://circleci.com/gh/bitnami/bitnami-docker-etcd/tree/master)

# What is etcd?

> etcd is a distributed key-value store designed to securely store data across a cluster. etcd is widely used in production on account of its reliability, fault-tolerance and ease of use.

[https://coreos.com/etcd/](https://coreos.com/etcd/)

# TL;DR;

```bash
$ docker run -it --name etcd bitnami/etcd
```

## Docker Compose

```bash
$ curl -LO https://raw.githubusercontent.com/bitnami/bitnami-docker-etcd/master/docker-compose.yml
$ docker-compose up
```

# Why use Bitnami Images?

* Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
* With Bitnami images the latest bug fixes and features are available as soon as possible.
* Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
* Bitnami images are built on CircleCI and automatically pushed to the Docker Hub.
* All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading linux distribution.

# Supported tags and respective `Dockerfile` links

* [`3`, `3.3.8-r7`, `latest` (3/Dockerfile)](https://github.com/bitnami/bitnami-docker-etcd/blob/3.3.8-r7/3/Dockerfile)
* [`3-ol-7`, `3.3.8-ol-7-r5` (3/ol-7/Dockerfile)](https://github.com/bitnami/bitnami-docker-etcd/blob/3.3.8-ol-7-r5/3/ol-7/Dockerfile)

Subscribe to project updates by watching the [bitnami/etcd GitHub repo](https://github.com/bitnami/bitnami-docker-etcd).

# Prerequisites

To run this application you need [Docker Engine](https://www.docker.com/products/docker-engine) >= `1.10.0`. [Docker Compose](https://www.docker.com/products/docker-compose) is recommended with a version `1.6.0` or later.

# Get this image

The recommended way to get the Bitnami etcd Docker Image is to pull the prebuilt image from the [Docker Hub Registry](https://hub.docker.com/r/bitnami/etcd).

```bash
$ docker pull bitnami/etcd:latest
```

To use a specific version, you can pull a versioned tag. You can view the
[list of available versions](https://hub.docker.com/r/bitnami/etcd/tags/)
in the Docker Hub Registry.

```bash
$ docker pull bitnami/etcd:[TAG]
```

If you wish, you can also build the image yourself.

```bash
$ docker build -t bitnami/etcd:latest https://github.com/bitnami/bitnami-docker-nginx.git
```

# Connecting to other containers

Using [Docker container networking](https://docs.docker.com/engine/userguide/networking/), a etcd server running inside a container can easily be accessed by your application containers using a etcd client.

Containers attached to the same network can communicate with each other using the container name as the hostname.

## Using the Command Line

In this example, we will create a etcd client instance that will connect to the server instance that is running on the same docker network as the client.

### Step 1: Create a network

```bash
$ docker network create app-tier --driver bridge
```

### Step 2: Launch the etcd server instance

Use the `--network app-tier` argument to the `docker run` command to attach the etcd container to the `app-tier` network.

```bash
$ docker run -d --name etcd-server \
    --network app-tier \
    --publish 2379:2379 \
    --publish 2380:2380 \
    --env ALLOW_NONE_AUTHENTICATION=yes \
    --env ETCD_ADVERTISE_CLIENT_URLS=http://etcd-server:2379 \
    bitnami/etcd:latest
```

### Step 3: Launch your etcd client instance

Finally we create a new container instance to launch the etcd client and connect to the server created in the previous step:

```bash
$ docker run -it --rm \
    --network app-tier \
    --env ALLOW_NONE_AUTHENTICATION=yes \
    bitnami/etcd:latest etcdctl --endpoints http://etcd-server:2379 set /message Hello
```

## Using Docker Compose

When not specified, Docker Compose automatically sets up a new network and attaches all deployed services to that network. However, we will explicitly define a new `bridge` network named `app-tier`. In this example we assume that you want to connect to the etcd server from your own custom application image which is identified in the following snippet by the service name `myapp`.

```yaml
version: '2'

networks:
  app-tier:
    driver: bridge

services:
  etcd:
    image: 'bitnami/etcd:latest'
    environment:
      - ALLOW_NONE_AUTHENTICATION=yes
      - ETCD_ADVERTISE_CLIENT_URLS=http://etcd:2379
    ports:
      - 2379:2379
      - 2380:2380
    networks:
      - app-tier
  myapp:
    image: 'YOUR_APPLICATION_IMAGE'
    networks:
      - app-tier
```

> **IMPORTANT**:
>
> 1. Please update the placeholder `YOUR_APPLICATION_IMAGE` in the above snippet with your application image
> 2. In your application container, use the hostname `etcd` to connect to the etcd server

Launch the containers using:

```bash
$ docker-compose up -d
```

# Configuration

The configuration can easily be setup by mounting your own configuration file on the directory `/opt/bitnami/etcd/conf`:

```
docker run --name etcd -v /path/to/etcd.conf.yml:/opt/bitnami/etcd/conf/etcd.conf.yml bitnami/etcd:latest
```

After that, your configuration will be taken into account in the server's behaviour.

Using Docker Compose:

```yaml
version: '2'

services:
  etcd:
    image: bitnami/etcd:latest
    environment:
      - ALLOW_NONE_AUTHENTICATION=yes
      - ETCD_ADVERTISE_CLIENT_URLS=http://etcd:2379
    ports:
      - '2379:2379'
      - '2380:2380'
    volumes:
      - /path/to/etcd.conf.yml:/opt/bitnami/etcd/conf/etcd.conf.yml
```

You can find a sample configuration file on this [link](https://github.com/coreos/etcd/blob/master/etcd.conf.yml.sample)

Apart from providing your custom configuration file, you can also modify the server behavior via configuration flags exposed as environment variables.

For example if you want to modify the flag `--my-flag`, you will need to set the `ETCD_MY_FLAG` environment variable.

The previous rule applies to all [etcd flags](https://coreos.com/etcd/docs/latest/op-guide/configuration.html).

# Further documentation

For further documentation, please check [etcd documentation](https://coreos.com/etcd/docs/latest/) or its [GitHub repository](https://github.com/coreos/etcd)

# Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/bitnami-docker-etcd/issues), or submit a [pull request](https://github.com/bitnami/bitnami-docker-etcd/pulls) with your contribution.

# Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/bitnami-docker-etcd/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

# License

Copyright 2018 Bitnami

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.