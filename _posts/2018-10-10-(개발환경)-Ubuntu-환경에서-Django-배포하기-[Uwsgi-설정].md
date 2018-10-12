---
title: (개발환경) Ubuntu 환경에서 Django 배포하기 - [uWSGI]
comments: true
description: Amazone EC2에서 생성한 Ubuntu 환경에서 uWSGI, NGINX를 사용해 배포하는 과정이다.
categories:
 - Dev
tags: Dev, Django 
---

## Overview

Ubuntu 환경에서 Python 3.6을 설치하고 Django 서버를 세팅 해보도록 할 것이다. EC2 인스턴스를 생성 하였으며, SSH 연결까지 된 상황이라고 가정하고 진행했다.

> [이 곳](https://medium.freecodecamp.org/django-uwsgi-nginx-postgresql-setup-on-aws-ec2-ubuntu16-04-with-python-3-6-6c58698ae9d3)을 참고해 진행했다.

## 목차

- [Ubuntu 환경에서 Django 배포하기 - [Django]](http://jangwon.me/dev/2018/10/08/(%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD)-Ubuntu-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Django-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-Django-%EC%84%A4%EC%A0%95/)
- [Ubuntu 환경에서 Django 배포하기 - [uWSGI]](http://jangwon.me/dev/2018/10/10/(%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD)-Ubuntu-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Django-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-Uwsgi-%EC%84%A4%EC%A0%95/)
- [Ubuntu 환경에서 Django 배포하기 - [NGINX]](http://jangwon.me/dev/2018/10/13/(%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD)-Ubuntu-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Django-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-NGINX-%EC%84%A4%EC%A0%95/)

## uWSGI 어플리케이션 서버 설정

Now that we’ve got our project set up and ready to go, we can configure uWSGI to serve our app to the web instead of the lightweight development server provided by Django.

If you’re thinking of running the runserver command on a screen, drop it. The dev server with Django is terribly lightweight, highly insecure, and not scalable.

You can install uWSGI either in virtualenv or globally and configure it accordingly.

In this tutorial, we’ll be installing uWSGI in virtualenv. Before we can install uWSGI, we need the Python development files that the software relies on.

### 가상환경 내부에서 패키지 설치

```bash
$ sudo apt-get install python3-dev
$ sudo su - Jangwon
Jangwon@ip-172-31-5-231:~$ source bin/activate
(Rspirit)Jangwon@ip-172-31-5-231:~$ pip install uwsgi
```

#### uwsgi 실행하기

```bash
(project_name)Jangwon@ip-172-31-5-231:~$ uwsgi --http :8000 --home <path-to-virtualenv> --chdir <path-to-manage.py-dir> -w <project-name>.wsgi
```
> uwsgi --http :8000 --home /Tirrilee-dev/Project/ --chdir /Tirrilee-dev/Project/ -w Rspirit.wsgi

### uWSGI configuration 파일 생성하기

```bash
(Rspirit)Jangwon@ip-172-31-5-231:~$ mkdir conf
(Rspirit)Jangwon@ip-172-31-5-231:~$ cd conf
(Rspirit)Jangwon@ip-172-31-5-231:~$ nano uwsgi.ini
```
#### uwsgi.ini

> 본인 프로젝트에 맞춰 디렉토리 설정을 바꿔야한다.

```
[uwsgi]

# telling user to execute file
uid = Jangwon

# telling group to execute file
gid = Tirrilee-dev

# name of project you during "django-admin startproject <name>"
project_name = Project

# building base path to where project directory is present [In my case this dir is also where my virtual env is]
base_dir = /Tirrilee-dev/%(project_name)

# set PYTHONHOME/virtualenv or setting where my virtual enviroment is
virtualenv = %(base_dir)

# changig current directory to project directory where manage.py is present
chdir = %(base_dir)

# loading wsgi module
module =  %(project_name).wsgi:application

# enabling master process with n numer of child process
master = true
processes = 4

# enabling multithreading and assigning threads per process
# enable-threads  = true
# threads = 2

# Enable post buffering past N bytes. save to disk all HTTP bodies larger than the limit $
post-buffering = 204800

# Serialize accept() usage (if possibie).
thunder-lock = True


# Bind to the specified socket using default uwsgi protocol.
uwsgi-socket = %(base_dir)/run/uwsgi.sock

# set the UNIX sockets’ permissions to access
chmod-socket = 666

# Set internal sockets timeout in seconds.
socket-timeout = 300

# Set the maximum time (in seconds) a worker can take to reload/shutdown.
reload-mercy = 8

# Reload a worker if its address space usage is higher than the specified value (in megabytes).
reload-on-as = 512

# respawn processes taking more than 50 seconds
harakiri = 50

# respawn processes after serving 5000 requests
max-requests = 5000

# clear environment on exit
vacuum = true

# When enabled (set to True), only uWSGI internal messages and errors are logged.
disable-logging = True

# path to where uwsgi logs will be saved
logto = %(base_dir)/log/uwsgi.log

# maximum size of log file 20MB
log-maxsize = 20971520

# Set logfile name after rotation.
log-backupname = %(base_dir)/log/old-uwsgi.log

# Reload uWSGI if the specified file or directory is modified/touched.
touch-reload = %(base_dir)

# Set the number of cores (CPUs) to allocate to each worker process.
# cpu-affinity = 1

# Reload workers after this many seconds. Disabled by default.
max-worker-lifetime = 300
```

> http-socket = :8000 을 통해 서버IP로 접속 가능하도록 설정가능 하지만, 데몬을 실행시킬 때 충돌이 일어나기에 제거한 상태로 진행

#### 파일 생성

```bash
(Project)Jangwon@ip-172-31-5-231:~$ mkdir log
(Project)Jangwon@ip-172-31-5-231:~$ mkdir run
(Project)Jangwon@ip-172-31-5-231:~$ touch log/uwsgi.log
```

#### 권한 설정

> 해당 파일에 모든 사용자가 접근 가능하도록 권한 설정

```bash
$ sudo chmod 777 /Tirrilee-dev/Project/run
$ sudo chmod 777 /Tirrilee-dev/Project/log
```

#### `uwsgi.ini` 파일을 통해 서버 실행하기

```bash
(Project)Jangwon@ip-172-31-5-231:~$ uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini
```
- 에러발생 시
    - https://stackoverflow.com/questions/21669354/rebuild-uwsgi-with-pcre-support

> 실제 정상작동 해도 http://[Your-IP]:8000 으로 접속이 불가능하다. Daemon 설정까지 완료한 후 에러를 확인하는게 좋다.

#### uWSGI 백그라운드에서 실행하기 위해 Systemd 설정 [Daemon 설정]

**sudo 권한으로 이동**

```bash
$ sudo vi /etc/systemd/system/uwsgi.service
```

> User, Group, Directory.. 등 본인 프로젝트에 맞게 수정한다.

```
[Unit]
Description=uWSGI instance to serve Tirrilee project
After=network.target

[Service]
User=Jangwon
Group=Tirrilee-dev
WorkingDirectory=/Tirrilee-dev/Project
Environment="PATH=/Tirrilee-dev/Project/bin"
ExecStart=/Tirrilee-dev/Project/bin/uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini
Restart=always
KillSignal=SIGQUIT
Type=notify
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```

**systemctl 데몬을 다시 로드하여 systemd Manager 구성을 다시 로드한ㄷ다.**

```bash
$ sudo systemctl daemon-reload
```

**Uwsgi 서비스를 System Reboot에서 시작할 수 있도록 설정**

```bash
$ sudo systemctl enable uwsgi
```

**Uwsgi 서비스 시작하기**

```bash
$ sudo service uwsgi start
# $ sudo service uwsgi restrat
```

**Uwsgi 서비스 상태확인**


```bash
$ sudo service uwsgi status
```

```bash
● uwsgi.service - uWSGI instance to serve Tirrilee project
   Loaded: loaded (/etc/systemd/system/uwsgi.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2018-10-10 16:39:55 UTC; 36s ago
 Main PID: 3987 (uwsgi)
   Status: "uWSGI is ready"
    Tasks: 5
   Memory: 24.3M
      CPU: 288ms
   CGroup: /system.slice/uwsgi.service
           ├─3987 /Tirrilee-dev/Project/bin/uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini
           ├─3991 /Tirrilee-dev/Project/bin/uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini
           ├─3992 /Tirrilee-dev/Project/bin/uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini
           ├─3993 /Tirrilee-dev/Project/bin/uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini
           └─3994 /Tirrilee-dev/Project/bin/uwsgi --ini /Tirrilee-dev/Project/conf/uwsgi.ini

Oct 10 16:39:55 ip-172-31-21-103 systemd[1]: Starting uWSGI instance to serve updateMe project...
Oct 10 16:39:55 ip-172-31-21-103 uwsgi[3987]: [uWSGI] getting INI configuration from /Tirrilee-dev/Project/conf/uwsgi.ini
Oct 10 16:39:55 ip-172-31-21-103 systemd[1]: Started uWSGI instance to serve updateMe project.
```
