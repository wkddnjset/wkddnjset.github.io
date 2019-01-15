---
title: (Django) 다양한 Generic View
comments: true
description: Ask Django에서 공부한 내용을 정리 했습니다.
categories:
 - Django/Study
tags: Django 
---

## 정리

- CBV는 재사용성에 초점을 둔 뷰
    - 기존 패턴을 사용할 경우에는 CBV가 단순하지만,
    - 새로운 루틴의 뷰를 구현할 경우 FBV를 사용하는게 훨씬 단순할 수 있다.
- 동일 멤버함수가 여러 CBV에 걸쳐서 구현되었을 경우, MRO 순서대로 호출된다.
- 더 다양한 기능을 구현 할 수 있는 써드파티 Class Based View [[Django-Braces](http://django-braces.readthedocs.io/en/latest/)]

### django.views.generic.base
- **TemplateView**(TemplateResponseMixin, ContextMixin, Veiw)

```python
from django.views.generic.base import TemplateView

class HomePageView(TemplateView):
    template_name = 'home.html'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['latest_articles'] = Article.objects.all()[:5]
        return context
```
### django.views.generic.date
- **ArchiveIndexView**(MultipleObjectTemplateResponseMixin, BaseArchiveIndexView)
- **YearArchiveView**(MultipleObjectTemplateResponseMixin, BaseYearArchiveView)

```python
from django.views.generic.date import ArchiveIndexView, YearArchiveView

class ArticleArchiveIndexView(ArchiveIndexView):
    model = Article
    date_field = 'date'

class ArticleYearArchiveView(YearArchiveView):
    queryset = Article.objects.all()
    date_field = 'date'
    make_object_list = True
    allow_future = True
```
### django.views.generic.detail
- **DetailView**(SingleObjectTemplateResponseMixin, BaseDetailView)
```python
from django.views.generic.detail import DetailView

class ArticleDetailView(DetailView):
    model = Article

```
### django.views.generic.edit
- **FormView**(TemplateResponseMixin, BaseFormView)
- **CreateView**(SingleObjectTemplateResponseMixin, BaseCreateView)
- **UpdateView**(SingleObjectTemplateResponseMixin, BaseUpdateView)
- **DeleteView**(SingleObjectTemplateResponseMixin, BaseDeleteView)
```python
from django.views.generic.edit import FormView, CreateView, UpdateView, DeleteView

class ArticleFormView(FormView):
    form_class = ArticleForm
    success_url = '/success/'

class ArticleCreateView(CreateView):
    model = Article
    form_class = ArticleCreateForm
    success_url = '/success/create'

class ArticleUpdateView(UpdateView):
    model = Article
    form_class = ArticleUpdateForm
    success_url = '/success/update'

class ArticleDeleteView(DeleteView):
    model = Article
    success_url = '/success/delete'
```
### django.views.generic.list
- **ListView**(MultipleObjectTemplateResponseMixin, BaseListView)
```python
from django.views.generic.list import ListView

class ArticleListView(ListView):
    model = Article
```