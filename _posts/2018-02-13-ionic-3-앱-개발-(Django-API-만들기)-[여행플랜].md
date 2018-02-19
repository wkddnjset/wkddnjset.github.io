---
title: ionic 3 앱 개발 (Django API 만들기) - [여행플랜/백엔드]
comments: true
description: Django에서 설계한 모델을 ionic에서 연동할 수 있도록 RestFul API를 만들어 보도록 하겠습니다.
categories:
 - MiniProject/TourPlan
tags: django app develop backend api
---

## 이전 내용

- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/04/ionic-3-%ED%95%98%EC%9D%B4%EB%B8%8C%EB%A6%AC%EB%93%9C-%EC%95%B1-%EA%B0%9C%EB%B0%9C%ED%95%98%EA%B8%B0-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/05/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%ED%83%AD-%EC%95%84%EC%9D%B4%EC%BD%98-%EB%B3%80%EA%B2%BD)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/06/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EA%B2%80%EC%83%89%EB%B0%94-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/07/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EB%8B%A4%EC%96%91%ED%95%9C-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (드롭다운) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/09/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EB%93%9C%EB%A1%AD%EB%8B%A4%EC%9A%B4)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (슬라이드) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/11/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (Django 모델설계) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/12/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(Django-%EB%AA%A8%EB%8D%B8%EC%84%A4%EA%B3%84)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

저번 시간에 만든 모델과 **Django Rest Framework**를 이용해 **API**를 만들어보도록 하겠습니다.

## 설치 및 설정

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django> pip install django-cors-headers==2.1.0
C:\Users\wk647\Desktop\Tourplan_Django> pip install django-rest-framework==0.1.0
{% endhighlight %}

**API**를 만들기 위해서 **Django-Rest-Framework**를 설치합니다. `django-cors-headers`는 **ionic**과 **django**를 동시에 로컬에서 돌리면서 **http request**를 하기 위해서 보안으로 인해 접근이 중단되는 것을 막아주는 라이브러리 입니다.

### Tourplan/settings.py

{% highlight python linenos %}
INSTALLED_APPS = [
    ...
    'corsheaders',
    'Product',
    'rest_framework',
]
MIDDLEWARE = [
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
]
CORS_URLS_REGEX = r'^/api/.*$'
CORS_ORIGIN_ALLOW_ALL = True
{% endhighlight %}

**settings.py**에 생성한 앱과 라이브러리들을 등록하고, **CORS**세팅을 해줍니다.

> [[미들웨어에 대한 자세한 설명](http://uiandwe.tistory.com/1160)]을 참고하세요!

## API를 위한 파일 생성하기

{% highlight python linenos %}
|-- Product
|   |-- api
|   |   |-- serializer.py
|   |   |-- urls.py
|   |   |-- views.py
|   |-- migrations
|-----
{% endhighlight %}

**Product**에 하위 폴더로 **api폴더**를 생성해주시고, 3개의 파일을 만듭니다!!

> **serializer.py** 파일은 DB에 저장된 데이터를 Json 형식으로 변환해주는 역할을 합니다. **urls.py**와 **views.py**는 기존에 **Django**와 비슷하게 활용되어지고 있습니다.

### api/serializer.py
### api/urls.py
### api/views.py

