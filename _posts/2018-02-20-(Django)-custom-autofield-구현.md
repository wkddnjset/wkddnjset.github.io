---
title: (Django) 모델에서 Custom AutoField 구현
comments: true
description: 질문을 위해 포스팅을 했습니다. 제ㅔ에엥발 알려주세요.
categories:
 - Django
tags: django develop 
---

## 누추한 곳까지 찾아와주시다니 정말 감사합니다ㅎㅎ

### myapp/models.py

{% highlight python linenos %}
def custom_increment:
	...

class Product(models.Model):
    id     = models.CharField(max_length=2, primary_key=True, default=custom_increment)
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
        print(model)
        field_name = self.field_name
        keyword = self.keyword
        last_field = model.objects.all().order_by(field_name).last()
        if not last_field:
            return keyword + '0000'
        old_code = last_field.field_name
        slice_code = int(old_code[2:6])
        new_code = slice_code + 1
        new_code = keyword + str(new_code).zfill(4)
        return new_code

    if __name__ == '__main__':
        four_padding()
{% endhighlight %}

그래서 다음과 같은 클래스를 갖고 있는 **Python** 파일을 만들었습니다.

- **model**
	- 체크하고 싶은 필드가 어떤 모델에 있는지를 정의
- **field_name**
	- custom_increment 하고 싶은 필드명
- **keyword**
	- keyword는 저장될 앞자리 String 정의
> keyword = "ID" => ID0000, ID0001, ID0002...

### myapp/models.py
{% highlight python linenos %}
from creater import IncrementCreater
...

class Product(models.Model):
    id     = models.CharField(max_length=2, primary_key=True, default=custom_increment)
    name   = models.CharField(max_length=20, null=False, blank=False)

    def __str__(self):
        return str(self.name)
# Product모델에 있는 id필드를 읽어오고, keyword는 ID로 설정했습니다.
custom_increment = IncrementCreater(Product, "id", "ID")
print(custom_increment)
{% endhighlight %}

**creater.py**에서 **IncrementCreater** 클래스를 **import**했습니다..이제 이걸 **Product** 클래스에 **id** 필드에 **default** 속성에 넣어주고 싶은데 이걸 어떻게 구현해야하는지 감이안옵니다.ㅠㅠ 제발 알려주세염
