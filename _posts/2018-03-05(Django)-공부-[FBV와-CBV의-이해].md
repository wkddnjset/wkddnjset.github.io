---
title: (Django) 공부 [FBV와 CBV의 정의]
comments: true
description: Ask Django에서 공부한 내용을 정리 했습니다.
categories:
 - Django/Study
tags: Django 
---

## 정리
- 클래스형 뷰는 재사용성을 위해 만들어졌다.
- 함수형 뷰 보다는 클래스형 뷰가 복잡하기 때문에 적절하게 섞어 사용하는 것이 좋다.
- 개발 시 함수형 뷰로 구현을 한 다음 클래스형 뷰로 리펙토링 하는 것을 추천한다.

## Fuction Base View 예시

#### Style 1
```python
def greeting(request, message='Good Day'):
  return HttpResponse(message)

def morning_greeting(request):
  return greeting(request, 'Good Morning')

def evening_greeting(request)
  return greeting(request, 'Good Evening')
```
#### Style 2
```python
def greeting_view(message):
  def view_fn(request):
    retunr HttpResponse(message)
  return view_fn

greeting = greeting_view('Good Day')
morning_greeting = greeting_view('Good Morning')
evening_greeting = greeting_view('Good Evening')
```

## Class Base View 예시
```python
from django.http import HttpResponse
from django.views import View

class GreetingView(View):
  message = 'Good Day'  # Class Variable
  
  def get(self, *args, **kwargs):
    return HttpResponse(self.message)
    
greeting = GreetingView.as_view()
    
class MorningGreetingView(View):
  message = 'Good Morning'  # Class Variable 재정의
  
morning_greeting = MorningGreetingView.as_view()
evening_greeting = GreetingView.as_view(message='Evening to ya')
```