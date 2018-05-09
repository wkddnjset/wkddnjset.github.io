---
title: (Django) FBV와 CBV의 정의
comments: true
description: Ask Django에서 공부한 내용을 정리 했습니다.
categories:
 - Django/Study
tags: Django 
---

## 정리

* Function Base View (FBV - 함수 기반 뷰)
    * 함수로 구현한 뷰
* Class Base View (CBV - 클래스 기반 뷰)
    * 클래스로 함수를 구현한 뷰

```python
# 함수 기반 뷰
def about(request):
    return HttpResponse('안녕하세요. AskDjango입니다.')

# 클래스 기반 뷰
class AboutView(object):
    @classmethod
    def as_view(cls, message):
        def view_fn(request):
    return view_fn

# 클래스 기반 뷰를 통해 만들어낸 함수
about = AboutView.as_view('안녕하세요. AskDjango입니다')
```

## 클래스의 3가지 함수 형식

* instance method : instance를 통한 호출. 첫번째 인자로 instance가 자동 지정 (self에 대입)
* class method : class를 통한 호출. 첫번째 인자로 Class가 자동 지정 (cls에 대입)
* static method : class를 통한 호출. 자동으로 지정되는 인자가 없음. 활용은 class method와 동일.

```python
class Person(object):
    def __init__(self, name, country):
        self.name = name
        self.contry = country
    def say_hello(self):
        return '안녕. 나는 {}이야. {}에서 왔어'.format(self.name, self.country)

    @staticmethod
    def new_korean1(name):
        return Person(name, 'Korea')

    @classmethod
    def new_korea2(cls, name):
        return cls(name, 'Korea')
```

## Function Base View

```python
from django.shortcuts import get_object_or 404, render

# 특정 id의 post detail 뷰 함수
def post_detail(request, id):
    post = get_object_or_404(Post, id=id)
    return render(request, 'blog/post_detail.html', {
        'post' : post,
    })

# 특정 id의 article detail 뷰 함수
def article_detail(request, id):
    post = get_object_or_404(Article, id=id)
    return render(request, 'blog/article_detail.html', {
        'article' : article,
    })
```

위 처럼 FBV가 반복되어 사용되어질 경우 아래 처럼 CBV를 활용하는 방법이 효율적 일 수 있다.

## Class Base View
```python
clasee DetailView(object)
    @classmethod
    def as_view(cls, model):
        def view_fn(request, id):
            instance = get_object_or_404(model, id=id)
            instance_name = model._meta.model_name
            template_name = '{}/{}_detail.html'.format(model._meta.app_label, instance_name)
            return render(request, template_name, {
                instance_name : instance,
            })
        return view_fn

post_detail = DetailView.as_view(Post)
article_detail = DetailView.as_view(Article)
```

CBV는 재사용성에는 용이하지만 조금 복잡해 질 수 있다.