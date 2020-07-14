---
title: ionic 3 앱 개발 (API 연동하기) - [여행플랜/백엔드]
comments: true
description: Django를 통해 로컬로 배포된 API를 ionic을 통해 연결해보도록 하겠습니다.
categories:
 - Ionic
tags: django app develop backend api
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/04/ionic3-tourplan-project-02/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/05/ionic3-tourplan-project-03/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/06/ionic3-tourplan-project-04/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/07/ionic3-tourplan-project-05/)]
- [[ionic 3 앱 개발 (드롭다운) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/09/ionic3-tourplan-project-06/)]
- [[ionic 3 앱 개발 (슬라이드) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/11/ionic3-tourplan-project-07/)]
- [[ionic 3 앱 개발 (Django 모델설계) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/12/ionic3-tourplan-project-08/)]
- [[ionic 3 앱 개발 (Django API 만들기) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/13/ionic3-tourplan-project-09/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

![api-result-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/api_result-01.png)

위에 보이는 **Json** 데이터를 ionic으로 가져와 보겠습니다.

## Provider 생성

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan\Tourpaln_ionic> ionic generate provider product
[OK] Generated a provider named product!
{% endhighlight %}

product라는 이름의 provider를 생성합니다. provider는 서비스 객체를 제공하는 역할을 하는 객체를 의미합니다.

### app/app.module.ts

{% highlight typescript linenos %}
...
import { ProductProvider } from '../providers/product/product';
...
providers: [
    ...
    ProductProvider
  ]
...
{% endhighlight %}

**app.module.ts** 파일을 가면 자동으로 위와 같은 코드가 **자동생성**된 것을 확인 하실수 있습니다.

### providers/product.ts

{% highlight typescript linenos %}
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';

@Injectable()
export class ProductProvider {
  url;
  constructor(public http: HttpClient) {
    console.log('Hello ProductProvider Provider');
    this.url = "http://127.0.0.1:8000/api/list/?format=json";
  }
  getProductList(){
    return this.http.get(this.url)
  }
}
{% endhighlight %}

**HttpClient** 모듈을 사용해서 API를 **Get 메소드**로 불러옵니다.

### pages/itemone.ts

{% highlight typescript linenos %}
...
import { ProductProvider } from '../../providers/product/product';
data:any;

export class ItemonePage {

  constructor(
    ...
    private ProductProvider: ProductProvider) {
  }
...
{% endhighlight %}

**provider**를 **import**합니다. 이렇게 하신 다음 새로고침 하면 아마 에러가 뜰겁니다. **HttpClient** 모듈이 **app.module.ts** 파일에 import가 되어있지않기 때문입니다.

> **Json** 데이터를 받아서 저장하기 위해 **변수 data**를 선언했습니다.

### app/app.module.ts

{% highlight typescript linenos %}
...
import { HttpClientModule } from '@angular/common/http';
...
imports: [
    ...
    HttpClientModule,
],
...
{% endhighlight %}

위 코드를 따라서 **HttpClientModule**를 **import** 합니다.

### pages/itemone.ts

{% highlight typescript linenos %}
...
ionViewWillEnter(){
    this.ProductProvider.getProductList().subscribe(data => {
      this.data = data
    });
}
...
{% endhighlight %}

**ProductProvider** 클래스에서 **getProductList** 함수를 호출합니다. 호출된 **Json** 데이터는 **this.data**로 저장됩니다. 

### pages/itemone.html

{% highlight html linenos %}
...
<ion-slides>
    <ion-slide class="slide-background"  *ngFor = "let product of data">
        <img class="slide-img" src="{{ product.img_url }}">
        <div class="slide-card">
          <h2 class="slide-title">{{ product.title }}</h2>
          <p class="slide-subtitle">{{ product.sub_title }}</p>
          <button class="tag-icon">
              <ion-icon name="tag-icon"></ion-icon>
          </button>
        </div>
    </ion-slide>
</ion-slides>
...
{% endhighlight %}

**itemone.ts** 파일에서 선언한 변수를 **html**로 불러와서 `*ngFor`를 이용해서 데이터를 입력시켜줍니다.

### Result

![api-result-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/result_02.gif)
