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


{% highlight bash linenos %}
C:\Users\wk647\Desktop\how-to-use-github> git fetch
C:\Users\wk647\Desktop\how-to-use-github> git checkout Jangwon
{% endhighlight %}

- **git fetch** : 브런치에 대해 변경되거나 추가된 사항에 대해 정보를 업데이트 합니다.
- **git checkout [브런치 이름]** : 브런치를 변경합니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\how-to-use-github> git status
On Branch Jangwon
...
{% endhighlight %}

**git status**라는 명령어로 브런치 **checkout** 상태를 확인할 수 있습니다.

### Git Push 하기

#### branch.py 생성하기

{% highlight python linenos %}
def branch_add(x, y):
    return x+x
{% endhighlight %}

이번에는 **branch.py**을 생성합니다.

#### Push 하기

{% highlight bash linenos %}
C:\Users\wk647\Desktop\how-to-use-github> git add -A
C:\Users\wk647\Desktop\how-to-use-github> git commit -m "commit"
C:\Users\wk647\Desktop\how-to-use-github> git push origin Jangwon
{% endhighlight %}

- **git add -A** : 해당 폴더에 모든 파일을 추가 합니다.
- **git commit -m "[커밋메세지]"** : 추가된 파일을 커밋합니다.
- **git push origin Jangwon** : origin이라는 저장소에 Jangwon브런치로 push합니다.

## Merge 및 충돌 대처하기

### Merge
{% highlight bash linenos %}
C:\Users\wk647\Desktop\how-to-use-github> git checkout master
C:\Users\wk647\Desktop\how-to-use-github> git merge Jangwon
C:\Users\wk647\Desktop\how-to-use-github> git push origin master
{% endhighlight %}

- **git checkout master** : master 브런치를 변경합니다.
- **git merge Jangwon** : Jangwon 브런치와 merge합니다.
- **git push origin master** : origin이라는 저장소에 master브런치로 push합니다.

### 충돌이 발생

두개의 브런치에서 동시에 Push를 하고 merge를 진행하게 되면!!! 충돌이 발생하게 됩니다.


