---
title: (개발환경) Ubuntu 환경에서 Django 배포하기 - [Django]
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
- uWSGI 어플리케이션 서버 설정하기 ()
- EC2에서 uWSGI를 위한 NGINX 설정하기 ()

## Ubuntu 패키지 업데이트

```bash
$ sudo apt-get update && sudo apt-get upgrade -y
```

## Installing Python3.6.x on AWS EC2 (ubuntu 16.04)

We will download the tar.xz file from official site and than manually install it. Before that we need to install some required dependencies.

### 패키지 설치

```bash
$ sudo apt install build-essential checkinstall
$ sudo apt install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
```

### Python 3.6 버전 설치

```bash
$ cd /opt && sudo wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tar.xz
$ sudo tar -xvf Python-3.6.6.tar.xz
$ cd Python-3.6.6/
$ sudo ./configure
$ sudo make && sudo make install
```

### 다운로드 파일 삭제

```bash
$ sudo rm -rf Python-3.6.6.tar.xz
```

### Python 버전 확인

```bash
$ python3 -V
Python 3.6.6
```

## 어플리케이션을 위한 우분투 유저 세팅

Django itself is very secure framework, I agree. But web applications are still vulnerable. It is good practice to run your application as system users with limited privileges which has limited access to resources on your server. So in this section, we will be adding a new user & permission group to our EC2 instance.

### 우분투 시스템 그룹(RSP-dev)을 추가 하고 유저(Jangwon) 등록

```bash
$ sudo groupadd --system RSP-dev
$ sudo useradd --system --gid RSP-dev --shell /bin/bash --home /RSP-dev/Rspirit Jangwon
$ sudo passwd Jangwon
```
> 여기서 `Rspirit`은 Django 프로젝트 생성시 설정할 프로젝트 이름이다.

### 프로젝트를 저장할 폴더 생성

```
$ sudo mkdir -p /RSP-dev/Rspirit/
$ sudo chown Jangwon /RSP-dev/Rspirit/
```

### 설정한 그룹 외 유저들이 해당 폴더에 접근하지 못하도록 설정

```bash
$ sudo chown -R Jangwon:users /RSP-dev/Rspirit
$ sudo chmod -R g+w /RSP-dev/Rspirit
```

### 설정한 유저로 변경

```bash
$ sudo su - Jangwon

// 콘솔이 아래처럼 변경됩니다
Jangwon@ip-172-31-5-231:~$
```

## EC2에서 Python 가상환경에 Git을 통해 Django 앱 배포하기

Deploying your app using a virtual environment allows your app and its requirements to be handled separately. It is good practice to keep your app isolated.

Using the environment concept is handy when you are deploying more than one Django app on a single instance to keep them and their dependencies isolated from each other.

We will be creating a virtual environment in our system user (bunny) directory. Before that we will be installing git as a sudo user.

### Git을 설치하고 저장소에서 소스 불러오기

```bash
$ sudo apt-get install git
$ sudo su - Jangwon
// change to your repo https or ssh link
Jangwon@ip-172-31-5-231:~$ git init
Jangwon@ip-172-31-5-231:~$ git remote add origin https://github.com/wkddnjset/RSP-Django
Jangwon@ip-172-31-5-231:~$ git pull origin master
```

### 동일한 디렉토리에 Python 3.6 가상환경 설치

```bash
Jangwon@ip-172-31-5-231:~$ python3.6 -m venv .
Jangwon@ip-172-31-5-231:~$ source bin/activate
(Rspirit)Jangwon@ip-172-31-5-231:~$ pip install -r requirements.txt
```

### Django 환경 설정 및 실행

```bash
(Rspirit)Jangwon@ip-172-31-5-231:~$ python manage.py migrate
(Rspirit)Jangwon@ip-172-31-5-231:~$ python manage.py createsuperuser
(Rspirit)Jangwon@ip-172-31-5-231:~$ python manage.py collectstatic
(Rspirit)Jangwon@ip-172-31-5-231:~$ python manage.py runserver 0.0.0.0:8000
```

> http://[Your-IP]:8000

> 위 링크에서 정상 작동하는 것을 확인 할 수 있습니다.