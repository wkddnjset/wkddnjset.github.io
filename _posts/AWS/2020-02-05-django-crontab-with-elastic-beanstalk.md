---
title: Elastic Beanstalk로 Django Crontab 배포하기
comments: true
description: 이번 포스팅에서는 사이드 프로젝트를 하면서 생긴 문제를 해결하는 과정에서 배운 내용을 정리하였으며, Django Crontab을 EB로 배포하는 아주 간단한 방법에 대해서 설명해보려고 합니다.
categories:
  - aws
tags: backend, elastic-beanstalk, aws, django, crontab, extension
---

우선 제가 EB(Elastic Beanstalk)를 사용하는 이유는 `빠른 배포` 떄문입니다.

사이드 프로젝트를 다양하게 하고 있는 저에겐 EB는 참 좋은 서비스 입니다! 인프라에 대한 지식이 적어도 알아서 세팅해주니...정말 편하게 사용하고 있습니다.

이번 포스팅에서는 사이드 프로젝트를 하면서 생긴 문제를 해결하는 과정에서 배운 내용을 정리하였으며, Django Crontab 을 EB로 배포하는 아주 간단한 방법에 대해서 설명해보고자 합니다.

<br/>

### Elastic Beanstalk 란?

EB는 AWS에서 제공해주는 서비스 중 하나이며, 통합툴 같은 느낌으로 EC2, S3, ELB 등 간단한 인프라 구조에서 부터 디테일한 서버 세팅까지 콘솔 또는 웹사이트 상에서 단순 클릭으로 쉽게 서버를 세팅할 수 있도록 도와주는 아아아주 좋은 서비스 입니다. 그 중 제가 가장 유용하고 제가 많이 사용하는 녀석은 `eb cli` 입니다.

> `eb cli`는 마치 깃허브 처럼 터미널에서 `eb deploy`와 같은 간단한 명령어로 서버 배포가 가능합니다.

- [스타트업에 딱 맞는 플랫폼, AWS Elastic Beanstalk 사용하기](https://www.bespinglobal.com/introduction-aws-beanstalk/)

<br/>

### EB CLI를 사용해 Django 배포하기

#### Django 배포 준비

```bash
(venv) ~/project$ pip freeze > requirements.txt
```

프로젝트를 개발한 환경에서 사용중인 라이브러리를 `pip freeze` 명령어를 사용해 `requirements.txt`로 저장합니다.

파일의 위치는 프로젝트와 동일한 선상에 있어야하며, 실제 배포하는 과정에서 자동으로 환경을 생성하며 해당 라이브러리를 설치합니다.

또한 저는 현재 체코에 있기 때문에 timezone 설정도 변경하였습니다.

```bash
(venv) ~/project$ mkdir .ebextensions
```

`.ebextensions`를 통해 서버 환경을 구축하며, 다양한 커스텀이 가능합니다. 우선 배포를 위해 생성된 디렉토리에 `django.config` 라는 구성파일을 추가합니다.

```text
// ~/project/.ebextensions/django.config
option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: project/wsgi.py
```

위 WSGIPath는 EB가 해당 서버를 시작하는 데 사용하는 WSGI 스크립트의 위치를 지정하는 부분입니다. 위 설정은 아래의 프로젝트 구조에 맞춰서 세팅한 내용이니 참고바랍니다.

```
~/project/
|-- .ebextensions
|   `-- django.config
|-- project
|   |-- __init__.py
|   |-- settings.py
|   |-- urls.py
|   `-- wsgi.py
|-- db.sqlite3
|-- manage.py
`-- requirements.txt
```

이걸로 Django 배포 준비 완료되었습니다. 이제 [이 곳](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install-advanced.html)을 참고해 EB CLI를 설치하면 됩니다.

#### EB CLI로 배포하기

```
~/project$ eb init
Do you want to set up SSH for your instances?
(y/n): y
Select a keypair.
1) my-keypair
2) [ Create new KeyPair ]
```

처음 `eb init` 명령어로 EB repo를 초기화하면 SSH 설정을 통해 EC2에 직접 접속할 수 있는 권한에 대해 물어보는데, 이 부분은 EC2 세팅과 다르게 추후 쉽게 추가 가능하니 초기세팅 때는 추가를 안하는 것을 추천드립니다.

```
Select a default region
1) us-east-1 : US East (N. Virginia)
2) us-west-1 : US West (N. California)
3) us-west-2 : US West (Oregon)
4) eu-west-1 : Europe (Ireland)
5) eu-central-1 : Europe (Frankfurt)
6) ap-south-1 : Asia Pacific (Mumbai)
7) ap-southeast-1 : Asia Pacific (Singapore)
8) ap-southeast-2 : Asia Pacific (Sydney)
9) ap-northeast-1 : Asia Pacific (Tokyo)
10) ap-northeast-2 : Asia Pacific (Seoul)
11) sa-east-1 : South America (São Paulo)
12) cn-north-1 : China (Beijing)
13) cn-northwest-1 : China (Ningxia)
14) us-east-2 : US East (Ohio)
15) ca-central-1 : Canada (Central)
16) eu-west-2 : Europe (London)
17) eu-west-3 : Europe (Paris)
18) eu-north-1 : Europe (Stockholm)
19) ap-east-1 : Asia Pacific (Hong Kong)
20) me-south-1 : Middle East (Bahrain)
(default is 3): 10
```

바로 다음으로는 리전을 선택할 수 있으며 `10`이 서울 리전입니다.

```
You have not yet set up your credentials or your credentials are incorrect.
You must provide your credentials.
(aws-access-id):
(aws-secret-key):
```

만약 처음 EB를 사용한다면, `aws-access-id`와 `aws-secret-key`를 요구할텐데, 이는 aws 홈페이지에서 `내 보안 자격 증명`에서 사용자를 설정해 키를 발급받을 수 있습니다. 이때 루트 계정의 전체 권한이 부여된 키를 사용하는것은 되도록 피하도록 하자

> 만약 다른 계정으로 배포를 진행하고 싶을 경우 `eb init`을 할때 `eb init --profile tirrilee` 처럼 `--profile` 이라는 명령어로 지정할 수 있으며, 해당 계정의 키는 윈도우의 경우 `C://User/.aws/config` 맥의 경우 `~/.aws/config` 파일에서 설정 가능합니다.

이후 안내에 따라 원하는 옵션들을 선택하면 EB 어플리케이션 설정이 마무리 됩니다.

```
~/project$ eb create
```

환경 설정 후 `eb create` 명령어를 통해 EB 환경 생성되며, 코드가 배포 됩니다. 배포 후 `eb open` 을 통해 배포된 사이트를 확인 할 수 있습니다.

<br/>

### .ebextansion을 활용해 crontab 실행하기

crontab을 돌리기 위해 [django_crontab](https://pypi.org/project/django-crontab/)을 설치하고, crontab을 돌릴 함수를 만들어줍니다.

```python
// ~project/apps/sender/cron.py
from django.utils import timezone

def crontab_test():
  print("======================")
  print("현재시간 : " + str(timezone.now()))
  print("crontab_test")
  print("======================")
```

간단하게 테스트를 위해 `apps` 디렉토리에 생성한 `sender` 앱에 `cron.py`를 생성했습니다.

```python
// ~project/project/settins.py
...
INSTALLED_APPS = [
    ...
    'django_crontab',
]
...
CRONJOBS = [
    ('* * * * *', 'apps.sender.cron.crontab_test', '>> /var/log/crontab.log'),
]
```

`settings.py`에 설치한 django_crontab 과 작성한 함수를 등록해 줍니다. 뒤에 붙은 `>> /var/log/crontab.log` 부분은 로그 파일의 저장 위치입니다.

> crontab은 기본적으로 `분(0 - 59) 시(0 - 23) 일(1 - 31) 월(1 - 12) 요일(0 - 6)` 별로 스케줄을 등록할 수 있으며, 현재 `* * * * *`은 매분 실행하는 것을 의미합니다.

```
// ~project/.ebextansion/00_remove_crontab.config
container_commands:
  00_remove_cron:
    command: "/opt/python/run/venv/bin/python /opt/python/current/app/manage.py crontab remove"
  01_logging_crontab:
      command: "touch /var/log/crontab.log"
  02_chmod_crontab:
      command: "chown ec2-user:ec2-user /var/log/crontab.log && chmod 777 /var/log/crontab.log"
```

우선 EB 특성상 배포가 진행될 때 마다 자동적으로 새로운 환경을 세팅해주고 있기 때문에, 이전 환경에 등록된 `crontab`을 제거해주는 작업이 필요합니다. `00_remove_crontab.config` 에서 앞에 00이 해당 구성파일을 실행하는 순서이며, 기본적으로 배포가 진행되기전에 실행됩니다. 현재 해당 구성파일에 입력된 커맨드는 3가지가 있는데 자세한 내용은 아래를 참고하자.

- `00_remove_cron`은 위에서 언급한 이전 환경에 등록된 crontab을 제거하는 커맨드
- `01_logging_crontab`은 로깅파일을 생성하는 커맨드
- `02_chmod_crontab`은 로깅파일에 접근 권한을 부여하는 커맨드

```
// ~project/.ebextansion/01_start_cron.config
commands:
  create_post_dir:
    command: "mkdir /opt/elasticbeanstalk/hooks/appdeploy/post"
    ignoreErrors: true
files:
  "/opt/elasticbeanstalk/hooks/appdeploy/post/start_cron.sh":
    mode: "000755"
    owner: root
    group: root
    content: |
      #!/usr/bin/env bash
      /opt/python/run/venv/bin/python /opt/python/current/app/manage.py crontab add
```

`01_start_cron.config`은 배포가 완료되는 시점에 새로 등록된 환경에서 다시 `crontab`을 실행해주는 구성파일 입니다. 마치 django의 `post_save`의 기능 처럼 배포되는 시점을 인식해 미리 만들어 놓은 bash 파일을 불러오는 느낌으로 구현되는 듯 합니다. 이렇게 두개의 파일 설정으로 `django_crontab` 설정은 완료되었습니다. 이제 배포만 하면 끝!

```
~project$ eb deploy
```

### 로그 결과

<img src='https://github.com/Tirrilee/tirrilee.github.io/blob/master/images/2020-02-04.png?raw=true' width='400px'/>

### P.S

아래 코드를 `~project/.ebextansion/01_start_cron.config` 여기에 추가해서 배포하면, 로그를 AWS 웹상에서 확인할 수 있습니다.

```
files:
  "/opt/elasticbeanstalk/tasks/taillogs.d/crontab.conf" :
    mode: "000755"
    owner: root
    group: root
    content: |
      /var/log/crontab.log
```
