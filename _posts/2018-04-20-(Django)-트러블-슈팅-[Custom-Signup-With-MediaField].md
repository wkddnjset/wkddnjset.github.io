---
title: (Django) 트러블 슈팅 [Custom Signup with MediaField]
comments: true
description: 개인프로젝트를 진행하면서 오류를 해결한 내용을 기록하는 포스팅 입니다. Ask Django에 도움을 받았습니다.
categories:
 - Django/TroubleShoot
tags: Django 
---

## 설명

우선 회원가입 시 입력받는 데이터를 추가하고 싶을 경우 User 테이블을 상속받는 다른 하나의 테이블을 만들어야 합니다.

> 저는 Profile이라는 테이블을 만들어서 진행했습니다.

## 코드

### models.py

{% highlight python linenos %}
class Profile(models.Model):
    sex_choice = (
        ('남', '남'),
        ('여', '여'),
    )
    user = models.OneToOneField(settings.AUTH_USER_MODEL)
    name = models.CharField(max_length=10)
    sex = models.CharField(max_length=2, choices=sex_choice)
    phone_number = models.CharField(max_length=20)
    img = models.ImageField(upload_to="profile_pic")
{% endhighlight %}

- **ImageField**를 사용하기 위해서 [다음](https://wayhome25.github.io/django/2017/05/10/media-file/)을 참조하세요.
- `upload_to="profile_pic"`은 저장되는 이미지 파일의 경로를 설정해주는 것입니다.

> /media/profile_pic/XXXx.jpg 다음과 같은 경로로 이미지는 저장됩니다.

### views.py

{% highlight python linenos %}
def signup(request):
    if request.method == 'POST':
        form = SignupForm(request.POST, request.FILES)
        if form.is_valid():
            user = form.save()
            return redirect(settings.LOGIN_URL) # default : "/accounts/login/"
    else:
        form = SignupForm()
    return render(request, 'accounts/signup_form.html', {
        'form' : form,
    })
{% endhighlight %}

### accounts/signup_form.html

{% highlight html linenos %}
<form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    <table>
        {{ form.as_table }}
    </table>
    <input type="submit"/>
</form>
{% endhighlight %}

## 주의사항

- **views.py**에서 **request.POST** 뿐만아니라 **request.FILES**를 인자로 받아야 **Media 파일**을 입력 받을 수 있습니다.
- **form.html**에서도 form 태그의 **enctype 속성**이 **multipart/form-data**로 설정되어 있지 않다면 파일이 아닌 파일 이름만 입력되게 됩니다.


