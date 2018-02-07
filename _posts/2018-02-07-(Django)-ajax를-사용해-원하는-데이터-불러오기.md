---
title: (Django) Ajax를 사용해서 원하는 데이터 불러오기
comments: true
description: Django를 사용해 Json 형태의 데이터를 뿌려주고, 뷰에서 Context로 받아온 데이터 값을 통해 다른 모델에 정보를 템플릿으로 가져오는 방법에 대한 포스팅입니다.
categories:
 - Django
tags: django, develop, backend
---

## 저는 이럴때 Ajax를 사용합니다.

특정 기능을 구현할때, ***Ajax***를 사용해야 하는지, Django로도 충분히 구현이 가능한지 여부에 대해서는 저도 자세하게 알지 못합니다ㅠ 다만 제가 ***Ajax***를 사용할 때는 이럴때입니다.

### 예시

저번에 사용했던 테이블로 예를 들어보도록 하겠습니다. 

- **대출 테이블에 대한 필드**
	- ID (PrimaryKey)
	- 사용자 ID (ForeignKey)
	- 상태 (대출완료, 회수중, 회수완료)
	- 대출금

- **회수 테이블에 대한 필드**
	- ID (PrimaryKey)
	- 대출모델 ID (ForeignKey)
	- 회수금

위 테이블에서 보시면 **회수 테이블**은 **대출 테이블**의 `ID`를 갖고 있기 때문에 템플릿에서 템플릿태그를 사용해 대출 테이블에 접근을 할 수 있습니다. 근데 만약 **대출 테이블**을 템플릿으로 넘겨주면 **회수 테이블**에 접근하기 까다롭습니다. 바로 이런 상황에서 저는 ***Ajax***를 사용합니다.

|    ID      |     사용자ID  	 |  상태     |   대출금  	 |    회수금	  |
|:----------:|:-------------:|:--------:|:----------:|:----------:|
| 1  		 |     서장원		 | 	대출완료	|  1,000,000 |			  |
| 2  		 |     서장원	     |  회수중 	|  1,000,000 |			  |
| 3   		 | 	   홍길동		 |  회수완료	|  1,000,000 |			  |		


이런 테이블을 사용자에게 보여준다고 가정해 봅시다. 현재 입력되어있는 **ID**, **사용자ID**, **상태**, **대출금**은 **대출 테이블**에서 가져올 수 있는 정보입니다. 하지만 여기서 회수금은 대출 테이블에 없는 정보이기에 ***Ajax***를 사용하는겁니다!

> 물론 ***Ajax***말고 다양한 방법으로 해결할 수 있습니다. 아마도...

## Ajax 사용하기

### views.py

{% highlight python linenos %}
from django.http import JsonResponse
from django.shortcuts import get_object_or_404
from .models import Payback

def PaybackAjax(request, pk):
    object = get_object_or_404(Payback, pk=pk)

    data = {
        'id'          : Payback.id,
        'lone_id'     : Payback.lone_id,
        'payback'     : Payback.payback,
    }
    return JsonResponse(data)
{% endhighlight %}

**Ajax**를 사용하기 위해서 데이터를 **Json**형태로 출력해주는 함수를 만듭니다!

### urls.py

{% highlight python linenos %}
from django.conf.urls import url
from .views import PaybackAjax

urlpatterns = [
    url(r'ajax/payback/(?P<pk>[\w-]+)/$', PaybackAjax, name='payback')
]
{% endhighlight %}

**urls.py**에 **pk**에 따라 해당 데이터를 불러 올 수 있도록!! 다음과 같이 입력해주세요~

### urls.py