---
title: (Django) 모델에서 Custom AutoField 구현
comments: true
description: Django 모델에서 Field의 default 값을 설정 해줌으로서 AutoField를 구현하는 과정에서 생긴 문제 및 질문과 해결 방벙에 대한 포스팅입니다.
categories:
 - Django
tags: django develop 
---

## 문제점

### myapp/models.py

{% highlight python linenos %}
def custom_increment:
	...

class Product(models.Model):
    id     = models.CharField(max_length=6, primary_key=True, default=custom_increment)
    name   = models.CharField(max_length=20, null=False, blank=False)

    def __str__(self):
        return str(self.name)
{% endhighlight %}

현재 저의 **models.py**에 다음과 같은 함수와 클래스가 있습니다. 근데 계속 개발을 하다보니 **custom_increment** 함수에 대해 사용 횟수가 늘어나면서 **custom_increment** 함수를 **클래스**로 만들고 **상속**받아서 사용하고 싶었습니다...

### myapp/creater.py

{% highlight python linenos %}
class IncrementCreater:

    def __init__(self, model, field_name, keyword):
        self.model = model
        self.field_name = field_name
        self.keyword = keyword

    def four_padding(self):
        model = self.model
        field_name = self.field_name
        keyword = self.keyword

        # model에서 field_name로 정렬하고 그중 제일 마지막 값을 읽어온다.
        last_field = model.objects.all().order_by(field_name).last()
        # last_field의 값이 없을 경우 "ID0000"을 리턴한다.
        if not last_field:
            return keyword + '0000'
        old_code = last_field.field_name
        # last_field의 field_name을 불러와서 숫자부분만 slice한다.
        slice_code = int(old_code[2:6])
        new_code = slice_code + 1
        new_code = keyword + str(new_code).zfill(4)
        return new_code

    if __name__ == '__main__':
        four_padding()
{% endhighlight %}

그래서 다음과 같은 클래스를 갖고 있는 **Python** 파일을 만들었습니다.

- **model** - 체크하고 싶은 필드가 어떤 모델에 있는지를 정의
- **field_name** - custom_increment 하고 싶은 필드명
- **keyword** - keyword는 저장될 앞자리 String 정의
> keyword = "ID" => ID0000, ID0001, ID0002...

### myapp/models.py
{% highlight python linenos %}
from creater import IncrementCreater
...

class Product(models.Model):
    id     = models.CharField(max_length=6, primary_key=True, default=custom_increment)
    name   = models.CharField(max_length=20, null=False, blank=False)

    def __str__(self):
        return str(self.name)
# Product모델에 있는 id필드를 읽어오고, keyword는 ID로 설정했습니다.
custom_increment = IncrementCreater(Product, "id", "ID")
print(custom_increment)
{% endhighlight %}

**creater.py**에서 **IncrementCreater** 클래스를 **import**했습니다..이제 이걸 **Product** 클래스에 **id** 필드에 **default** 속성에 넣어주고 싶은데 이걸 어떻게 구현해야하는지 감이안옵니다.ㅠㅠ 제발 알려주세염

## Ask Django 권재원님 도움을 받아 해결했습니다.

### myapp/models.py
{% highlight python linenos %}
def get_industry_code():
    result = IncrementCreater(Industry, "code", "ID")
    return result.four_padding()

class Industry(models.Model):
...
    code     = models.CharField(max_length=6, primary_key=True, default=get_industry_code)
...
{% endhighlight %}

**get_industry_code** 라는 함수를 만들어 주고 생성한 **IncrementCreater** 클래스에 파라미터로 값을 넘겨주어 결과 값을 반환받습니다. 그리고 생성한 **Industry** 모델에서 **default**값으로 함수의 결과값을 넣습니다. 

### myapp/creater.py

{% highlight python linenos %}
class IncrementCreater:
    ...
    def four_padding(self):
        last_model = self.model.objects.all().order_by(self.field_name).last()
        if not last_model:
            return self.keyword  + '0000'
        # getattr를 사용해서 String상태의 field_name으로 필드 값을 가져왔다
        last_field = getattr(last_model, self.field_name)
        last_field_int = int(last_field[2:6])
        new_field_val = last_field_int + 1
        new_field_val = self.keyword + str(new_field_val).zfill(4)
        return new_field_val
{% endhighlight %}

위에 **get_industry_code** 함수에서 파라미터로 넘긴 **field_name**이 String 형태로 되어있기 때문에 이를 해결하기 위해서 **getattr**를 사용했습니다!!!






















