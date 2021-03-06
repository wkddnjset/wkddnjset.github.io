---
title: ionic 3 앱 개발 (Django 모델설계) - [여행플랜/백엔드]
comments: true
description: 상품정보의 이미지, 타이틀, 서브타이트르이 데이터를 갖고 있는 API를 Django로 만들어서 ionic과 연동 해보도록 하겠습니다.
categories:
 - Ionic
tags: django app develop backend api
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](http://jangwon.io/ionic/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](http://jangwon.io/ionic/2018/02/04/ionic3-tourplan-project-02/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](http://jangwon.io/ionic/2018/02/05/ionic3-tourplan-project-03/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](http://jangwon.io/ionic/2018/02/06/ionic3-tourplan-project-04/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](http://jangwon.io/ionic/2018/02/07/ionic3-tourplan-project-05/)]
- [[ionic 3 앱 개발 (드롭다운) 포스팅](http://jangwon.io/ionic/2018/02/09/ionic3-tourplan-project-06/)]
- [[ionic 3 앱 개발 (슬라이드) 포스팅](http://jangwon.io/ionic/2018/02/11/ionic3-tourplan-project-07/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]



![result-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-11/result_01.gif)

이제 프런트는 완성했습니다! API를 만들기 이전에 모델설계를 해보도록하겠습니다.

## 설치

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django> pip install Django==1.11
{% endhighlight %}

 **django**를 설치합니다. 저는 1.11버전을 사용했습니다.

## 프로젝트 생성 및 설정

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django> mkdir src && cd src
C:\Users\wk647\Desktop\Tourplan_Django\src> django-admin startproject Tourplan .
{% endhighlight %}

**src**라는 폴더를 생성하고 해당 위치에 **Tourplan**이라는 프로젝트를 생성합니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django\src> python manage.py startapp Product
C:\Users\wk647\Desktop\Tourplan_Django\src> python manage.py createsuperuser
{% endhighlight %}

**Product**라는 애플리케이션을 만들고, 관리자 아이디를 만듭니다.

{% highlight python linenos %}
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
{% endhighlight %}

미디어와 관련된 모델을 만들기 위해 미디어 파일을 저장할 경로를 지정해주는 **MEDIA_ROOT**와 각 미디어 파일에 대한 url 경로인 **MEDIA_URL**를 설정해준다.

|    ID      |     이미지url   |     타이틀     |   서브타이틀     |
|:----------:|:-------------:|:------------:|:-------------:|
| 1          |   http://...  | Souel, Korea |     text      |
| 2          |   http://...  | Tokoy, Japan |     text      |
| 3          |   http://...  |      ...     |     text      |

위와 같은 테이블을 모델로 설계해보도록 하겠습니다.

### Product/models.py

{% highlight python linenos %}
from django.db import models

class ProductList(models.Model):
    img_url      = models.ImageField(blank=True)
    title        = models.CharField(max_length=100)
    sub_title    = models.TextField()

    def __str__(self):
        return str(self.title)
{% endhighlight %}

**id 필드**는 설정하지 않아도 자동적으로 **AutoField**로 생성됩니다. 아래 같이 다른 방법으로도 **id 필드**를 추가할 수 있습니다.

> **id = models.AutoField(primary_key=True)**

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django\src> pip install pillow
C:\Users\wk647\Desktop\Tourplan_Django\src> python manage.py makemigrations
C:\Users\wk647\Desktop\Tourplan_Django\src> python manage.py migrate
{% endhighlight %}

**라이브러리** 설치 후 마이그레이션 명령을 실행시켜 **DB**를 생성합니다.

>**ImageField**를 마이그레이션하기 위해서는 Python 이미지 라이브러리인 **Pillow**를 설치해야 합니다.

### Product/admin.py

{% highlight python linenos %}
from .models import ProductList

class ProductListAdmin(admin.ModelAdmin):
    list_display = ('id', 'img_url', 'title')

admin.site.register(ProductList, ProductListAdmin)
{% endhighlight %}

**관리자 페이지**에서 해당 모델을 관리할 수 있도록 **admin.py**를 수정해줍시다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan_Django\src> python manage.py runserver
{% endhighlight %}

**Django**를 실행합니다.

![admin-page-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/admin_page_01.png)

**관리자 페이지**에 로그인하게 되면, **Product lists**를 통해 아래 그림과 같이 **이미지 파일**을 추가할 수 있는 페이지를 볼 수 있습니다. 

![admin-page-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/admin_page_02.png)

### Tourplan/urls.py

{% highlight python linenos %}
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
{% endhighlight %}

**Project** 폴더에 **urls.py**에 이미지 url에 대한 **urlpattern**을 추가합니다.

![image-page-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/image_page_01.png)

이미지를 **관리자 페이지**에서 등록 후 아래와 같은 URL로 들어가면 해당 이미지를 불러올 수 있습니다.

> **http://localhost/media/[이미지파일명]**

## 다음 내용

- [[ionic 3 앱 개발 (Django API 만들기) 포스팅](http://jangwon.io/ionic/2018/02/13/ionic3-tourplan-project-9)]
