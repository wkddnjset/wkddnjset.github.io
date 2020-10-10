---
title: (Django) FBV와 CBV의 사용
comments: true
description: Ask Django에서 공부한 내용을 정리 했습니다.
categories:
 - Django
tags: Django
---

## 정리
- 각 CBV 동작은 소스코드를 직접 살펴보고 이해해야한다.
- 파이썬 멤버함수 호출순서는 MRO순서를 따른다.[[참고](https://github.com/Tirrilee/TechTalk/wiki/MRO)]

## FBV
```python
def post_edit(request, id):
    instance = get_object_or_404(Post, id=id)    # Case 1) 인스턴스 획득 조건을 커스텀   
 
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES, instance=instance)
        if form.is_valid():
            post = form.save()
            return redirect('/success_url/')     # Case 2) 이동할 URL 변경
    else:
        form = PostForm()
    return render(request, 'templates/post_form.html', {'form' : form})  # Case 3) 추가 파라미터 지정

def article_edit(request, id):
    # 위 post_edit 뷰와 비슷한 루틴인데, 중복을 최소화하고 재사용성을 어떻게 높일 수 있을까요?
    raise NotImplementedError
```

## CBV

### 클래스 정의

```python
from django.views import View
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponse

class EditFormView(View):
    model = None
    form_class = None
    success_url = None
    template_name = None
   
    def get_object(self):
        pk = self.kwargs['pk']
        return get_object_or_404(self.model, id=pk)

    def get_success_url(self):
        return self.success_url

    def get_template_name(self):
        return self.tempate_name

    def get_form(self):
        form_kwargs = {
            'instance' : self.get_object(),
        }
        if self.request.method == 'POST':
            form_kwargs.update({
                'date' : self.request.POST,
                'files' : self.request.FILES,  
                # File을 업로드 하기위해 Template에서 Form태그 enctype속성 값을 "multipart/form-data"로 설정해야한다.
            })
        return self.form_class(**form_kwargs)

    def get_context_data(self, **kwargs):
        if 'form' not in kwargs:
            kwargs['form'] = self.get_form()
        return kwargs
    
    def get(self, *args, **kwargs):
        return render(self.request, self.get_template_name(), self.get_context_data())
    
    def post(self, *args, **kwargs):
        form = self.get_form()
        if form.is_valid():
            form.save()
            return redirect(self.get_success_url())
        return render(self.request, self.get_template_name(), self.get_context_data(form=form)


```

### 클래스 호출
```python
post_edit = EditFormView.as_view(
    model = Post,
    form_class = PostForm,
    success_url = '/post-success',
    template_name = 'blog/post_form.html'
)

# 또는

class PostEditFormView(EditFormView):
    model = Post
    form_class = PostForm
    success_url = '/post-success'
    template_name = 'blog/post_form.html'

post_edit = PostEditFormView.as_view()
```
### 클래스에 각 인스턴스 함수들을 재정의
```python
from django.shortcuts import resolve_url

class PostEditFormView(EditFormView):
    model = Post
    form_class = PostForm
    template_name = 'blog/post_form.html'
   
    def get_object(self):
        pk = self.kwargs['pk']
        return get_object_or_404(self.model, id=pk, created_at__year=2018) # 2018년 포스팅 중에서

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['dummy_params'] = '더미 데이터'
        return context
   
    def get_success_url(self):
        # resolve_url은 urls.py에서 정의한 name을 사용하기 위한 함수 
        return resolve_url('blog:post_detail', self.kwargs['pk'])

post_edit = PostEditFormView.as_view()
```