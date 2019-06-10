# Table of Contents
- [Overview](#overview)
- [FAQ](#faq)
- [Image](#image)
  - [Dockerfile](#dockerfile)
- [Container](#container)
- [Networking](#networking)
  - [Overview](#overview-1)
  - [Bridge Network](#bridge-network)
  - [Overlay](#overlay)
  - [Host](#host)
  - [Macvian](#macvian)
  - [Summary:](#summary)
  - [docker network CLI](#docker-network-cli)
- [Storage](#storage)
  - [Overview](#overview-2)
  - [Volumes](#volumes)
  - [Bind Mounts](#bind-mounts)
  - [tmpfs Mounts](#tmpfs-mounts)
  - [docker volume CLI](#docker-volume-cli)
- [Docker Compose](#docker-compose)

## Overview
![overview](https://docs.docker.com/engine/images/engine-components-flow.png)

## FAQ
* How to minimize docker image size?
  * Build Context
    * [Understand build context](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context)
    * [Exclude with .dockerignore](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#exclude-with-dockerignore)
  * [Use multi-stage builds](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#use-multi-stage-builds)
  * [Minimize the number of layers](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#minimize-the-number-of-layers)
  * [Leverage build cache](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)

## Image
* [docker image CLI](https://docs.docker.com/engine/reference/commandline/image/)
    ```bash
    $ docker image --help
    ```

### Dockerfile
* [dockerfile reference](https://docs.docker.com/engine/reference/builder/)
* [General guidelines and recommendations](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
  * [Understand build context](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context)
  * [Exlucde with .dockerignore](Exclude with .dockerignore)
  * [Use multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
    ```Dockerfile
    FROM golang:alpine AS build-env
    ADD . /src
    RUN cd /src && go build -o app
     
    # final stage
    FROM alpine
    WORKDIR /app
    COPY --from=build-env /src/app /app/
    ENTRYPOINT ./app
    ```
  * [Minimize the number of layers](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#minimize-the-number-of-layers)
  * In older versions of Docker, it was important that you minimized the number of layers in your images to ensure they were performant. The following features were added to reduce this limitation:
    * **Only the instructions RUN, COPY, ADD create layers**. Other instructions create temporary intermediate images, and do not increase the size of the build.
    * **Where possible, use multi-stage builds**, and only copy the artifacts you need into the final image. This allows you to include tools and debug information in your intermediate build stages without increasing the size of the final image.



## Container
* [docker container CLI](https://docs.docker.com/engine/reference/commandline/container/)
    ```bash
    $ docker container --help
    ```


## [Networking](https://docs.docker.com/network/)
### [Overview](https://docs.docker.com/network/)
### [Bridge Network](https://docs.docker.com/network/bridge/)
* A bridge network uses a software bridge which allows containers connected to the same bridge network to communicate, while providing isolation from containers which are not connected to that bridge network.
* [Differences between user-defined bridges and the default bridge](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge)
  * User-defined bridges provide **better isolation and interoperability** between containerized applications.
  * User-defined bridges provide **automatic DNS resolution** between containers.
  * Containers can be attached and detached from user-defined networks on the fly.
  * Each user-defined network creates a configurable bridge.
  * Linked containers on the default bridge network share environment variables.
### [Overlay](https://docs.docker.com/network/overlay/)
### [Host](https://docs.docker.com/network/host/)
### [Macvian](https://docs.docker.com/network/macvlan/)
### Summary:
* User-defined bridge networks:
  *  When you need multiple containers to communicate **on the same Docker host**.
* Host networks
  * When **the network stack should not be isolated from the Docker host**, but you want other aspects of the container to be isolated.
* Overlay networks
  * When you need containers running **on different Docker hosts** to communicate, or when multiple applications work together using swarm services.
* Macvlan networks
  * When you are migrating from a VM setup or need your containers to look like physical hosts on your network, each with a unique MAC address.
* Third-party network plugins
  * Allow you to integrate Docker with specialized network stacks.
### [docker network CLI](https://docs.docker.com/engine/reference/commandline/network/)
```bash
 docker network --help
 ```


## [Storage](https://docs.docker.com/storage/)
### [Overview](https://docs.docker.com/storage/)
![overview](https://docs.docker.com/storage/images/types-of-mounts.png)
### [Volumes](https://docs.docker.com/storage/volumes/)
* Volumes are **stored in a part of the host filesystem which is managed by Docker** (/var/lib/docker/volumes/ on Linux). Non-Docker processes should not modify this part of the filesystem. Volumes are the best way to persist data in Docker.
### [Bind Mounts](https://docs.docker.com/storage/bind-mounts/)
* Bind mounts may be stored anywhere **on the host system**. They may even be important system files or directories. Non-Docker processes on the Docker host or a Docker container can modify them at any time.

### [tmpfs Mounts](https://docs.docker.com/storage/tmpfs/)
* mounts are stored in the **host system’s memory only**, and are never written to the host system’s filesystem.

### [docker volume CLI](https://docs.docker.com/engine/reference/commandline/volume/)
 ```bash
 docker volume --help
 ```

## Docker Compose
* Define and run multi-container applications with Docker.
* [version3 reference](https://docs.docker.com/compose/compose-file/)
* [docker-compose CLI](https://docs.docker.com/compose/reference/)
    ```bash
    docker-compose --help
    ```
* example:
    ```yaml
    version: "3.7"

    services:
    db:
        image: mysql
        container_name: mysql_common
        restart: "no"
        networks:
        - common_net
        ports:
        - 3306:3306
        environment:
        MYSQL_ROOT_PASSWORD: 123

    redis:
        image: redis
        container_name: redis_common
        restart: "no"
        networks:
        - common_net
        ports:
        - 6379:6379

    common:
        depends_on:
        - db
        - redis
        build:
        context: ./
        dockerfile: dockerfile
        args:
            work_dir: /tmp/common
        image: common:develop
        container_name: common
        restart: "no"
        networks:
        - common_net
        volumes:
        - type: bind
            source: ./
            target: /tmp/common
        tty: true


    networks:
    common_net:
        driver: bridge
        name: "common_net"
    ```

