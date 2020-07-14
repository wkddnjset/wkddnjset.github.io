---
title: (Django) Celery로 Django 스케줄러 구현하기 - [배포]
comments: true
description: Celery와 Celery Beat를 사용해 스케줄러를 구현하고, 이를 배포하는 과정까지 진행해보도록 하겠습니다.
categories:
 - Django
tags: Dev, Django
---

## Overview

개발 환경(윈도우)에서 Celery와 RabbitMQ를 사용해 스케줄러를 구현 후, Ubuntu 환경에서 서버 세팅하는 과정까지 진행하도록 하겠습니다.

- 참고 블로그
  - [Celery 기본 세팅](https://m.blog.naver.com/PostView.nhn?blogId=c_ist82&logNo=220777045214&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)
  - [Celery 스케줄링](https://blog.naver.com/c_ist82/220777624611)
  - [RabbitMQ 우분투 설치](https://openmind8735.com/aws/rabbitmq/2017/07/17/AWS-EC2%EC%97%90-RabbitMQ-%EC%84%9C%EB%B2%84%EC%98%AC%EB%A6%AC%EA%B8%B0/)

## 목차

- [Celery로 Django 스케줄러 구현하기 - [테스트]]()
- [Celery로 Django 스케줄러 구현하기 - [배포]]()

## 우분투 환경에 배포하기

우분투에 Django가 배포되었다는 가정하에 진행하며, 만약 안되어있을시 [이 곳](<http://jangwon.io/dev/2018/10/08/(%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD)-Ubuntu-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Django-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-Django-%EC%84%A4%EC%A0%95/>)을 확인할수 있습니다.

### RabbitMQ 설치

RabbitMQ 서버 설치

```bash
$ sudo apt-get install rabbitmq-server
```

Management Plugin 활성화

```bash
$ sudo rabbitmq-plugins enable rabbitmq_management
$ sudo service rabbitmq-server restart
```

계정 추가하기

```bash
$ sudo rabbitmqctl add_user wkddnjset [PASSWORD]
$ sudo rabbitmqctl set_user_tags wkddnjset administrator
# $ sudo rabbitmqctl set_permissions -p / wkddnjset ".*" ".*" ".*"
```

**http://[Your-IP]:15672/에서 RabbitMQ가 작동하는 것을 확인 할 수 있다.**

> guest/guest 계정은 로컬에서만 적용가능하다. 계정을 추가하는 이유는 정상작동을 확인하기 위함이다. Django 세팅과 전혀 상관없다.

### Celery Daemon으로 실행하기

[3.1.25버전 Docs](http://docs.celeryproject.org/en/3.1/tutorials/daemonizing.html)

#### 실행 파일 생성

1. `/etc/init.d/celeryd` 파일 생성
2. [celery repo](https://github.com/celery/celery/blob/master/extra/generic-init.d/celeryd)에 내용을 복붙한다.

```bash
$ sudo vi /etc/init.d/celeryd
```

> vi로 파일 생성 후 [celery repo](https://github.com/celery/celery/blob/master/extra/generic-init.d/celeryd) 코드를 복붙한다.

#### Config 파일 생성

1. `/etc/default/celeryd` 파일 생성

```bash
$ sudo vi /etc/default/celeryd
```

```
CELERY_BIN="/CheckYourSite/CheckYourSite/bin/celery"

# App instance to use
CELERY_APP="CheckYourServer"

# Where to chdir at start.
CELERYD_CHDIR="/CheckYourSite/CheckYourSite"

# Extra arguments to celeryd
CELERYD_OPTS="--time-limit=300 --concurrency=8"

# Name of the celery config module.
CELERY_CONFIG_MODULE="CheckYourServer.settings"

# %n will be replaced with the nodename.
CELERYD_LOG_FILE="/CheckYourSite/CheckYourSite/log/celery-%n.log"
CELERYD_PID_FILE="/CheckYourSite/CheckYourSite/log/celery-run-%n.log"

# Workers should run as an unprivileged user.
CELERYD_USER="Jangwon"
CELERYD_GROUP="CheckYourSite"

# Name of the projects settings module.
export DJANGO_SETTINGS_MODULE="CheckYourServer.settings"
```

#### 권한설정

```bash
$ sudo chmod 755 /etc/init.d/celeryd
$ sudo chown root:root /etc/init.d/celeryd
```

#### 실행하기

```bash
$ sudo /etc/init.d/celeryd start
# $ sudo /etc/init.d/celeryd restart
celery init v10.1.
Using config script: /etc/default/celeryd
celery multi v3.1.24 (Cipater)
> Starting nodes...
        > celery@ip-172-31-xx-xx: OK
```

### Celery beat Daemon으로 실행하기

#### 실행파일 생성

1. `/etc/init.d/celerybeat` 파일 생성
2. [celery repo](https://github.com/celery/celery/blob/master/extra/generic-init.d/celerybeat)에 내용을 복붙한다.

```bash
sudo vi /etc/init.d/celerybeat
```

#### Config 파일 생성

Celeryd Config 파일을 생성했기 때문에 따로 Celery Beat Config 파일을 만들필요없다. 혹시 두개의 모듈이 다른 세팅에서 돌아야 한다면, [Docs](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#init-script-celerybeat)를 참고해서 수정하기 바란다.

#### 권한설정

```bash
$ sudo chmod 755 /etc/init.d/celeryd
$ sudo chown root:root /etc/init.d/celeryd
```

#### 실행하기

```bash
$ sudo /etc/init.d/celerybeat start
celery init v10.1.
Using configuration: /etc/default/celeryd
Stopping celerybeat... OK
```

### 결과

개인적으로 `Celery Beat`가 동작하면서, **CheckLog**라는 모델에 **Create** 되도록 설정해놨다. 아래 사진에 생성날짜를 확인해보면 1분 간격으로 `Celery Beat`가 동작하는 것을 확인 할 수 있다.
![Celery-Beat-Result](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2018-10/celery-beat.png?raw=true)
