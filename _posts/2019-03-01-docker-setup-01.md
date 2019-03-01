---
title: "1. Mac에 Docker 설치하기"
date: 2019-03-01 23:00:00 -0900
categories: docker
tags: 
  - Docker
lastmod: 2019-03-01 23:00:00 -0900
---

# Mac에 Docker 설치하기

- [Mac에 Docker 설치하기](#mac%EC%97%90-docker-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)
  - [Docker 구동환경](#docker-%EA%B5%AC%EB%8F%99%ED%99%98%EA%B2%BD)
  - [Mac OS에 Docker 설치](#mac-os%EC%97%90-docker-%EC%84%A4%EC%B9%98)
  - [Docker Destop for Mac](#docker-destop-for-mac)
  - [참고](#%EC%B0%B8%EA%B3%A0)

## Docker 구동환경

Docker CE(이하 docker)는 설치환경 및 목적에 따라 서로 다른 설치방법이 있다.

예로써 아래 그림을 참고해서 보면 Docker는 Docker Client / Daemon 구조로 되어 있어(Docker daemon을 dockerd로 병행해서 쓰인다.) Mac 및 Window 상에서 Docker를 설치 할 경우, Docker Client는 Host OS(Windows, Mac)에 설치되고 가상Linux서버를 생성해 Docker daemon이 설치되며, Linux에 설치하는 경우에는 Docker client, daemon이 동일한 Host(Linux)에 설치됩니다. 

![installostype](https://user-images.githubusercontent.com/14902468/53614730-38bb5f80-3c1e-11e9-905d-03bf94b38d14.PNG)

## Mac OS에 Docker 설치

본인은 Mac OS를 사용 중이기 때문에 Mac에 Docker 설치하는 방법에 대해서 포스팅해보겠다.

앞서 말했듯이, Mac OS는 Unix 기반이고, Docker는 Linux 기반이기 때문에 Mac에서 Docker를 돌리기 위한 가상 Linux 서버인 Docker daemon을 설치해야만 한다.

Docker측에서 공식적으로 지원하는 [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)으로 설치하는 방법과 [Boot2Docker](https://github.com/boot2docker/boot2docker)라는 virtualed docker engine 경량가상머신을 설치하는 방법, 그리고 별도로 VirtualBox와 같은 가상머신으로 Linux 환경을 구성해서 설치하는 방법 등이 있다.

최근 Docker측에서 나름 활발하게 Docker Desktop for Mac 을 업데이트해주고 있고, Kubernetes를 지원하는 버전도 있기 때문에 여기서는 Docker Destop for Mac을 이용해서 Docker를 설치해보고자 한다.

## Docker Destop for Mac

먼저 [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac) 링크로 이동해서 ```Docker.dmg``` 파일을 다운받고, 인스톨해보자.

인스톨을 완료하고 실행하게 되면, Mac 상단 상태 바에 아래와 같은 귀여운 고래 아이콘이 나타나게 된다. ![](https://d1q6f0aelx0por.cloudfront.net/icons/whale-in-menu-bar.png)

여기까지 완료됐다면 Docker Daemon이 돌아가고 있기 때문에 Terminal을 열어서 ```docker version``` 같은 명령어가 정상적으로 작동함을 확인하면 된다.

```bash
❯ docker --version
Docker version 18.09.2, build 6247962

❯ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

❯ docker-compose --version
docker-compose version 1.23.2, build 1110ad01

❯ docker-machine --version
docker-machine version 0.16.1, build cce350d7
```

이 외에 ```docker run hello-world``` 커맨드로 도커가 이상없이 설치되었는지 확인하는 절차도 있다.

```bash
❯ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

...
```



## 참고

1. [Get started with Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/)
2. [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)
3. [Mac OS X에서 docker 설치하기(시작하기)](http://blog.saltfactory.net/upgrade-latest-docker-using-with-homebrew/)
4. [Docker for mac 훑어보기](https://nolboo.kim/blog/2016/08/02/docker-for-mac/)





