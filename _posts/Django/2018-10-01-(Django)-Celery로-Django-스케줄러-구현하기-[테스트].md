---
title: (Django) Celery로 Django 스케줄러 구현하기 - [테스트]
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

## Celery 란?

Celey는 백엔드에서 발생하는 병목현상을 해결하기 위해서 비동기 처리를 도와주는 놈이고, Django 에서 주로 사용된다.

> 메일을 보내거나, 큰 파일을 저장할 경우 처리시간이 길어지는데 이를 비동기 처리 함으로써 병목현상 해결

## Broker 란?

Broker는 Celery에 작업을 요청할 때 사용된다. 기본적으로 Broker로 사용되는 툴은 RabbitMQ와 Redis가 있다.
- [RabbitMQ](https://www.rabbitmq.com/)
  - Celery를 사용하는 유저들 중 사용 비율이 제일 높아보인다.
  - Window, Celery 최신 버전에서 RabbitMQ 지원을 안한다.
- [Redis](https://redis.io/)
  - Amazon ElastiCache에서 지원하기에 Aws를 사용할 경우 함께 사용하면 좋을 것 같다.
  - 다만, EC2에서 자체적으로 구축하면 좀 더 저렴하게 사용가능해 보인다.

### RabbitMQ 설치
- window
  - [RabbitMQ 설치](https://www.rabbitmq.com/install-windows.html)
  - [플러그인 설치](http://yi-chi.tistory.com/74)
  - http://localhost:15672/ `id:guest, pw:guest`

> window의 경우 erlang을 따로 설치해야 하는데 RabbitMQ 최신 버전에서는 erlang을 설치할 수 있는 페이지로 리다이렉트 시켜준다. 다른 OS에 대한 설치도 [[RabbitMQ 설치](https://www.rabbitmq.com/install-windows.html)] 이곳에서 확인 가능합니다.

### Django 세팅

3.1.xx 버전과 4.0.xx 버전의 세팅이 다르니 주의해주세요. RabbitMQ가 설치되었다는 가정 하에 개발을 진행했습니다.

#### 설치

```bash
$ pip install celery
$ pip install celery==3.1.24
```

> window를 사용할 경우 3.1.24를 사용해야합니다. 무조건!!

#### Project/Project/Celery.py

```python
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery
from datetime import timedelta
from django.conf import settings

# set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'Project.settings')

app = Celery('Project', broker='amqp://guest:guest@localhost:5672//')

# v4.0 이상 일 경우
# app.config_from_object('django.conf:settings', namespace='CELERY')
# v3.1 일 경우
app.config_from_object('django.conf:settings')

# v4.0 이상 일 경우
# app.autodiscover_tasks()
# v3.1 일 경우
app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)

app.conf.update(
    CELERY_TASK_SERIALIZER='json',
    CELERY_ACCEPT_CONTENT=['json'],  # Ignore other content
    CELERY_RESULT_SERIALIZER='json',
    CELERY_TIMEZONE='Asia/Seoul',
    CELERY_ENABLE_UTC=False,
    CELERYBEAT_SCHEDULE = {
        'say_hello-every-seconds': {
            "task": "App.tasks.CheckSite",
            'schedule': timedelta(seconds=30),
            'args': ()
        },
    }
)
```

#### Project/App/tasks.py

```python
from __future__ import absolute_import

from CheckYourServer.celery import app

@app.task
def say_hello():
    print("hellow world!")
```

> 이때 파일명은 무조건 `tasks.py`여야 한다.

### 테스트

#### Celery worker 실행

스케줄러는 Celery beat에서 제공해주는 기능이며, Celery worker가 실행된 상태에서만 가능하다.

```bash
$ celery -A Project worker -l info
...
...
[2018-10-10 09:22:34,440: WARNING/MainProcess] celery@DESKTOP-123 ready.
```

ready가 되면 Celery worker가 준비되었다는 의미이다.

#### Celery beat 실행

```bash
$ celery beat -A CheckYourServer --loglevel=INFO
celery beat v3.1.24 (Cipater) is starting.
...
```
Celery beat에 대한 동작은 `Project/Project/Celery.py`에서 설정이 가능하다.