---
title: ionic 3 앱 개발 (슬라이드) - [여행플랜/프런트]
comments: true
description: 이번에는 여행 상품정보를 보여주는 슬라이드를 만들어보도록 하겠습니다.
categories:
 - MiniProject/TourPlan
tags: ionic app develop frontend
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/04/ionic3-tourplan-project-02/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/05/ionic3-tourplan-project-03/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/06/ionic3-tourplan-project-04/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/07/ionic3-tourplan-project-05/)]
- [[ionic 3 앱 개발 (드롭다운) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/09/ionic3-tourplan-project-06/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

![Result_01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-09/result_01.gif)

이제 내용을 채워보도록 하겠습니다.

## 슬라이드

![design-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-01/design_01.png){: width="400px"}

가운데 **이미지**와, **타이틀**, **서브타이틀**, **태그** 버튼을 구현해 보도록하겠습니다.

### itemone/itemone.html

{% highlight html linenos %}
<ion-content padding>
    <ion-slides>
      <ion-slide class="slide-background">
        <img class="slide-img" src="assets/icon/producr-img.png">
        <div class="slide-card">
          <h2 class="slide-title">Seoul, Korea</h2>
          <p class="slide-subtitle">The National Palace Museum of Korea is 
          located south of Heungnyemun Gate.</p>
          <button class="tag-icon">
              <ion-icon name="tag-icon"></ion-icon>
          </button>
        </div>
      </ion-slide>
    </ion-slides>
</ion-content>
{% endhighlight %}

슬라이드를 사용하는 방법은 아주 간단합니다. `<ion-slides>` 태그 안에 `<ion-slide>` 갯수에 따라 보여지는 슬라이드의 수를 조절할 수 있습니다.

> 데이터를 리스트로 가져와서 **ngFor**를 이용해서 만들면 쉽게 만들 수 있겠쬬!?!


### itemone/itemone.css

{% highlight css linenos %}
...
.slide-img{
    width: 300px;
    box-shadow: 0.1px 0.1px 60px #999;
    border-radius: 10px;
}
.scroll-content{
    padding:0 !important;
}
.slide-card{
    position: absolute;
    bottom: 65px;
    background: #FFF;
    padding: 10px;
    width: 286px;
    left: 70px;
    border-radius: 10px 0px 0px 10px;
}
.slide-title{
    text-align: left;
    margin: 8px 8px 8px 8px !important;
}
.slide-subtitle{
    margin: 8px 8px 30px 8px;
    font-size: 14px;
    text-align: left;
    color: #999;
}
.tag-icon{
    background: #FFF0;
    font-size: 50px;
    position: absolute;
    top: 70px;
    left: 220px;
    color: #777777;
}
{% endhighlight %}

저는 슬라이드에 대한 **CSS**를 이렇게 적용해봤습니다. 여러분들도 각자 취향에 맞춰서..해보시면 좋을 것 같습니다! CSS는 개취

### Result

![result-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-11/result_01.gif)

드디어 프런트 완성입니다. 이제 백엔드를..해볼까요?크..

## 다음 내용

- [[ionic 3 앱 개발 (Django 모델설계) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/12/ionic3-tourplan-project-08)]
