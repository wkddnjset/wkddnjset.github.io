---
title: ionic 3 앱 개발 (다양한 애니메이션) - [여행플랜/프런트]
comments: true
description: 이번에는 Animate.css 라이브러리를 사용해서 좀 더 인터렉티브한 애니메이션을 다뤄보는 방법에 대해 포스팅을 해보도록 하겠습니다.
categories:
 - Ionic
tags: ionic app develop frontend
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](http://jangwon.io/ionic/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](http://jangwon.io/ionic/2018/02/04/ionic3-tourplan-project-02/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](http://jangwon.io/ionic/2018/02/05/ionic3-tourplan-project-03/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](http://jangwon.io/ionic/2018/02/06/ionic3-tourplan-project-04/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

![gif-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-06/gif_02.gif)

여기까지 완성되었습니다. 오늘은 Animate.css 라이브러리를 적용하는 방법에 대해 알아보도록 하겠습니다.

## 모듈 설치 및 적용

- Animate.css를 사용하면 더 다양한 애니메이션을 사용할 수 있습니다.
  + [[다양한 애니메이션 확인하기](https://daneden.github.io/animate.css/)]
  + [[Github 바로가기](https://github.com/daneden/animate.css/)]

### CMD

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourpaln>npm install --save css-animator 
{% endhighlight %}

우선 Animate.css를 사용하기 위해서 라이브러리를 다운로드합니다.

### app/app.module.ts

{% highlight typescript linenos %}
...
import { AnimationService, AnimatesDirective } from 'css-animator'
...
  declarations: [
    ...
    ItemtwoPage,
    ItemthreePage,
    /* 추가 부분 */
    AnimatesDirective
  ],
...
  providers: [
    StatusBar,
    SplashScreen,
    {provide: ErrorHandler, useClass: IonicErrorHandler},
    /* 추가 부분 */
    AnimationService
  ]
...
{% endhighlight %}

라이브러리를 `app.module.ts`에 적용시켜줍니다. 여기서 각각 항목을 **declarations**과 **providers**에 적용하는 것입니다. 

### index.html

{% highlight html linenos %}
...
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css">
...
{% endhighlight %}

애니메이션이 적용될 수 있도록 **CDN** 링크를 프로젝트 파일에 **index.html**에 입력합니다!

### pages/itemone/itemone.html

{% highlight html linenos %}
<ion-content padding>
<h1>item one page</h1>
/* 추가 부분 */
<div animates #animation="animates" text-center>
    <button ion-button (click)="animation.start({type:'wobble', duration:'1000'})">Click me!</button>, 
</div>

</ion-content>
{% endhighlight %}

이렇게 간단하게 CSS를 적용하여 인터렉티브한 애니메이션을 구현할 수 있습니다.

![result-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-07/result_01.gif)

크으.. 신기하네요 ㅋㅋ

> 근데 알아본 결과..**css-animator**은 사용하기 쉽지만 제약사항이 많아 잘 사용하지 않는다고 합니다 ㅠㅠ 좀 어렵더라도 **web-animation-js**를 사용하는걸 권장하네요!

## 검색바 애니메이션 추가!

저는 귀가 얇아서 **web-animation-js**를 사용해서 검색바 애니메이션을 좀더 고급스럽게!! 바꿔보도록 하겠습니다!!

### pages/home/home.ts

{% highlight typescript linenos %}
...
animations:[
    trigger('mysearchbar', [
        ...
        transition('invisible => visible', [
            style({transform: 'translateX(100%)', opacity:'0'}),
            animate('400ms ease-out')
          ]),
        transition('visible => invisible', [
            animate('400ms ease-in', style({transform: 'translateX(100%)', opacity:'0'}))
          ])
      ])
  ]
})
...
{% endhighlight %}

애니메이션 부분을 이렇게 수정하게 되면, `invisible`에서 `visible`로 바뀌면 우에서 좌로 나타나고, `visible`에서 `invisible`로 바뀌면 좌에서 우로 사라지는 애니메이션이 만들어 집니다! 자연스럽게 하기 위해서 **ease-in**, **ease-out**을 추가했습니다.

### pages/home/home.html

{% highlight html linenos %}
...
<ion-buttons right>
  <button *ngIf="visibleState == 'invisible'" (click)="toggleVisible()" ion-button icon-only>
    <ion-icon name='search-icon'></ion-icon>
  </button>
  <button *ngIf="visibleState == 'visible'" (click)="toggleVisible()" ion-button icon-only>
    <ion-icon name='close'></ion-icon>
  </button>
</ion-buttons>
...
{% endhighlight %}

그리고 추가로 **home.html**에서는 `*ngIf`를 사용해서 `invisible`시 검색버튼이 나오고 `visible`시 취소 버튼이 나오도록 만들었습니다!

![result-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-07/result_02.gif)

으아아아 프런트 어렵습니다.. 백엔드와 프런트 둘다 해보고 있지만 개인적으로 프런트가 조금 더 어려운거 같습니다ㅠㅠ 전혀 몰랐어

## 다음 내용

- [[ionic 3 앱 개발 (드롭다운 및 디자인) 포스팅](http://jangwon.io/ionic/2018/02/09/ionic3-tourplan-project-06)]