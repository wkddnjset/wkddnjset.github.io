---
title: (DjangoCMS) 소셜로그인 플러그인 만들기 01
comments: true
description: Django CMS 기반의 플러그인을 만들기 위한 과정입니다.
categories:
 - DjangoCMS/Study
tags: djangcms django develop backend
---


우선 Django CMS의 플러그인을 만들기 전에 재사용 가능한 앱을 만드는 과정에 대해서 먼저 이해하고 있어야합니다.
플러그인 자체도 결국 하나의 앱으로 구성되어있기 때문...

## 참고

- [How to write reusable apps](https://docs.djangoproject.com/en/2.2/intro/reusable-apps/)
- [setuptools documentation](https://setuptools.readthedocs.io/en/latest/)

## 요구사항 분석

- 기존의 Django 라이브러리인 allauth를 사용해서 구현
- 사용할 Provider를 선택할 수 있는 모델 구현
- Template으로 버튼 구현

## 개발하기

```
$ mkdir plugin && cd plugin
$ django-admin startapp allauth_cms
```

plugin 폴더를 생성 후 allauth_cms라는 이름의 App을 만든다.

```
project/
    ...
    plugin/
        allauth_cms/
            templates/
            __init__.py
            admin.py
            apps.py
            cms_plugins.py
            models.py
            tests.py
            views.py
        ...
    ...
```

App 안에 `templates` 폴더와 `cms_plugins.py` 파일을 생성한다.

cms_plugins.py
## CMS 기본  구조

```
mysite/
    manage.py
    mysite/
        static/
        templates/
        __init__.py
        settings.py
        urls.py
        wsgi.py
    ...
```