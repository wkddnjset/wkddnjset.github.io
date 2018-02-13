---
title: ionic 3 앱 개발 (API 만들기) - [여행플랜/백엔드]
comments: true
description: 상품정보의 이미지, 타이틀, 서브타이트르이 데이터를 갖고 있는 API를 Django로 만들어서 ionic과 연동 해보도록 하겠습니다.
categories:
 - MiniProject/TourPlan
tags: django, app, develop, backend, api
---

## 이전 내용

- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/04/ionic-3-%ED%95%98%EC%9D%B4%EB%B8%8C%EB%A6%AC%EB%93%9C-%EC%95%B1-%EA%B0%9C%EB%B0%9C%ED%95%98%EA%B8%B0-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/05/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%ED%83%AD-%EC%95%84%EC%9D%B4%EC%BD%98-%EB%B3%80%EA%B2%BD)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/06/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EA%B2%80%EC%83%89%EB%B0%94-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/07/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EB%8B%A4%EC%96%91%ED%95%9C-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (드롭다운) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/09/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EB%93%9C%EB%A1%AD%EB%8B%A4%EC%9A%B4)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (슬라이드) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/11/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]


![result-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-11/result_01.gif)

이제 프런트는 완성했습니다! 백엔드와 결합을 하기위해 Restful API를 만들어 보겠습니다.

## 설치

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django> pip install Django==1.11
C:\Users\wk647\Desktop\Tourplan_Django> pip install django-cors-headers==2.1.0
C:\Users\wk647\Desktop\Tourplan_Django> pip install django-rest-framework==0.1.0
{% endhighlight %}

**API**를 만들기 위해서 **Django-Rest-Framework**를 설치합니다. `django-cors-headers`는 **ionic**과 **django**를 동시에 로컬에서 돌리면서 **http request**를 하기 위해서 보안으로 인해 접근이 중단되는 것을 막아주는 라이브러리 입니다.

## 프로젝트 생성 및 설정

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django> mkdir src && cd src
C:\Users\wk647\Desktop\Tourplan_Django\src> django-admin startproject Tourplan .
{% endhighlight %}

**src**라는 폴더를 생성하고 해당 위치에 **Tourplan**이라는 프로젝트를 생성합니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django\src> python manage.py startapp Product
{% endhighlight %}

**Product**라는 애플리케이션을 만듭니다.

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


### Product/models.py

|    ID      |     이미지url   |     타이틀     |   서브타이틀     |
|:----------:|:-------------:|:------------:|:-------------:|
| 1          |   http://...  | Souel, Korea |     text      |
| 2          |   http://...  | Tokoy, Japan |     text      |
| 3          |   http://...  |      ...     |     text      |


