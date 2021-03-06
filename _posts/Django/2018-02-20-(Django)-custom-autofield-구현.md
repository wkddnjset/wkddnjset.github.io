---
title: (Django) 모델에서 Custom AutoField 구현
comments: true
description: Django 모델에서 Field의 default 값을 설정 해줌으로서 AutoField를 구현하는 과정에서 생긴 문제 및 질문과 해결 방벙에 대한 포스팅입니다.
categories:
 - Django
tags: django develop 
---

## 문제점

**Django**에서 **Models**를 상속 받아서 필드를 새로 만드는것이 아니라 **CharField**에서 **Default** 값이 자동으로 증가하는 클래스를 만들고 해당 클래스를 사용해서 **AutoField**를 구현해보려고 합니다.

### myapp/creater.py

{% highlight python linenos %}
class IncrementCreater:

    def __init__(self, last_col, last_field, keyword):
        self.last_col = last_col
        self.last_field = last_field
        self.keyword = keyword

    def four_padding(self):
        # 마지막 열을 확인 후 존재하지 않을 경우 '0000'을 리턴한다.
        if not self.last_col:
            return self.keyword  + '0000'
        slice_code = int(self.last_field[2:6])
        new_code = slice_code + 1
        new_code = self.keyword + str(new_code).zfill(4)
        return new_code

    if __name__ == '__main__':
        four_padding()
{% endhighlight %}

그래서 다음과 같은 클래스를 갖고 있는 **Python** 파일을 만들어줍니다.

- **last_col** - Auto Increment Field를 포함한 모델의 마지막 열을 불러옵니다.
- **last_field** - 마지막 열의 Auto Increment Field값을 불러옵니다.
- **keyword** - keyword는 저장될 앞자리 String 정의합니다.

> keyword = "ID" => ID0000, ID0001, ID0002...

### myapp/models.py
{% highlight python linenos %}
from creater import IncrementCreater
...

def custom_increment():
    object = Industry.objects.all()
    last_col = object.order_by("code").last()
    last_field = last_col.code
    result = IncrementCreater(last_col, last_field, "ID").four_padding()
    return result

class Product(models.Model):
    id     = models.CharField(max_length=6, primary_key=True, default=custom_increment)
    name   = models.CharField(max_length=20, null=False, blank=False)
...
{% endhighlight %}

**creater.py**에서 **IncrementCreater** 클래스를 **import**했습니다..이제 이걸 **Product** 클래스에 **id** 필드에 **default** 속성에 넣어주고 싶은데 이걸 어떻게 구현해야하는지 감이안옵니다.ㅠㅠ 제발 알려주세염

## Ask Django 권재원님 도움을 받아 해결했습니다.

### myapp/models.py
{% highlight python linenos %}
def custom_increment():
    result = IncrementCreater(Product, "code", "ID")
    return result.four_padding()

class Product(models.Model):
    id = models.CharField(max_length=6, primary_key=True, default=custom_increment)
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
