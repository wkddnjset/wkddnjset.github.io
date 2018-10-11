---
title: (개발환경) Ubuntu 환경에서 Django 배포하기 - [NGINX]
comments: true
description: Amazone EC2에서 생성한 Ubuntu 환경에서 uWSGI, NGINX를 사용해 배포하는 과정입니다.
categories:
 - Dev
tags: Dev, Django 
---

## Overview

Ubuntu 환경에서 Python 3.6을 설치하고 Django 서버를 세팅 해보도록 하겠습니다. EC2 인스턴스를 생성 하였으며, SSH 연결까지 된 상황이라고 가정하고 진행하였습니다.

> [이 곳](https://medium.freecodecamp.org/django-uwsgi-nginx-postgresql-setup-on-aws-ec2-ubuntu16-04-with-python-3-6-6c58698ae9d3)을 참고해 진행하였습니다.

## 목차

- [Python 설치 및 Django 환경 설정하기](http://jangwon.me/dev/2018/10/08/(%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD)-Ubuntu-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Django-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-Django-%EC%84%A4%EC%A0%95/)
- [uWSGI 어플리케이션 서버 설정하기](http://jangwon.me/dev/2018/10/10/(%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD)-Ubuntu-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Django-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-Uwsgi-%EC%84%A4%EC%A0%95/)
- EC2에서 uWSGI를 위한 NGINX 설정하기 ()

## uWSGI를 위한 NGINX 세팅하기

### 시작하기에 앞서 nginx를 설치합니다.

```bash
$ sudo apt-get install nginx
```

> 설치 후 http://[Your-IP] 해당 링크에서 `Welcome to nginx` 페이지를 확인 할 수 있습니다.

nginx는 2개의 폴더를 갖고 있다. 상황에 따라 두 파일을 수정하기도 하지만, 세팅을 위해 삭제를 하도록 한다.
- `sites-available`
  - 특정 인스턴스의 사용 가능한 모든 사이트에 대한 모든 config 파일을 저장한다.
- `sites-enabled `
  - `sites-available` 폴더에 있는 사이트에 심볼 링크를 저장한다.

```bash
$ sudo rm -rf /etc/nginx/sites-available/default
$ sudo rm -rf /etc/nginx/sites-enabled/default
```

`site-available` 폴더에 **nginx-uwsgi.conf** 파일을 생성한다.

```bash
$ sudo vi /etc/nginx/sites-available/nginx-uwsgi.conf
```

```
upstream RSP_dev {
    server unix:/RSP-dev/Rspirit/run/uwsgi.sock;
}

server {
    listen 80;
    server_name 13.209.88.232;
    charset utf-8;

    client_max_body_size 128M;

    location /static {
    # exact path to where your static files are located on server 
    # [mostly you won't need this, as you will be using some storage service for same]
        alias /RSP-dev/Rspirit/static;
    }

    location /media {
    # exact path to where your media files are located on server 
    # [mostly you won't need this, as you will be using some storage service for same]
        alias /RSP-dev/Rspirit/media;
    }

    location / {
        include uwsgi_params;
        uwsgi_pass RSP_dev;
        uwsgi_read_timeout 300s;
        uwsgi_send_timeout 300s;
    }

    access_log /RSP-dev/Rspirit/log/dev-nginx-access.log;
    error_log /RSP-dev/Rspirit/log/dev-nginx-error.log;
}
```

`sites-enabled` 폴더에 동일한 파일을 생성한다.

```bash
$ sudo ln -s /etc/nginx/sites-available/nginx-uwsgi.conf /etc/nginx/sites-enabled/nginx-uwsgi.conf
```

**Daemon을 실행한다.**

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl enable nginx
$ sudo service nginx start
```

nginx를 테스트 하고, 에러가 발생했을 경우 로그파일을 확인한다.

```bash
$ sudo nginx -t
...
$ tail -f /webapps/updateMe/log/nginx-error.log
$ tail -f /webapps/updateMe/log/nginx-access.log
```

nginx 서버 재시작

```bash
$ sudo service nginx restart
```

nginx 상태 확인

```bash
$ sudo service nginx status
```

```
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2018-10-11 09:14:33 UTC; 17min ago
 Main PID: 15896 (nginx)
   CGroup: /system.slice/nginx.service
           ├─15896 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─15897 nginx: worker process
```

> nginx가 정상작동 할 경우 http://[Your-IP] 여기로 작업한 내용들을 확인 할 수 있다.