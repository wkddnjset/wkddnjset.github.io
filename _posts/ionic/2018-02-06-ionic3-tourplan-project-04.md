---
title: ionic 3 앱 개발 (검색바 애니메이션) - [여행플랜/프런트]
comments: true
description: ionic에서 사용하는 web-animations-js 모듈을 설치하여 검색 버튼을 누르면 검색바가 애니메이션되는 방법에 대한 내용을 포스팅하겠습니다.
categories:
 - MiniProject/TourPlan
tags: ionic app develop frontend
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/04/ionic3-tourplan-project-02/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/05/ionic3-tourplan-project-03/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

![result-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/result_02.png){: width="400px"}

여기까지 완성되었습니다. 여기서 우측 검색버튼을 누르면 검색바가 애니메이션 되게 만들어 보겠습니다!

## 모듈 설치 및 적용

### **Commend**
{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourpaln> npm install web-animations-js @angular/animations@5.0.3 --save
...
{% endhighlight %}

커맨드에서 애니메이션 라이브러리를 설치합니다. 라이브러리의 버전은 **package.json**파일에서 `@angular/core`의 버전과 동일하게 설치해주시면 됩니다.

> ...
> "@angular/core": "5.0.3",
> ...

### **app/app.module.ts**
{% highlight typescript linenos %}
...
import { StatusBar } from '@ionic-native/status-bar';
import { SplashScreen } from '@ionic-native/splash-screen';
/* 추가 내용 */
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
...
imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp),
     /* 추가 내용 */
    BrowserAnimationsModule,
  ],
...
{% endhighlight %}

설치한 모듈을 `app/app.module.ts`에 **import**합니다.

## 검색바 애니메이션 만들기

### pages/home/home.ts

{% highlight typescript linenos %}
...
import { trigger, style, state } from '@angular/core';
...
{% endhighlight %}

**애니메이션 라이브러리**에서 사용할 모듈을 **import** 해줍니다.

### pages/home/home.ts

{% highlight typescript linenos %}
...
@Component({
  selector: 'page-home',
  templateUrl: 'home.html',
  /* 추가 내용 */
  animations:[
    trigger('mysearchbar', [
        state('visible', style({
          opacity: 1
        })),
        state('invisible', style({
          opacity: 0
        }))
      ])
  ]
})
export class HomePage {
    visibleState = 'invisible';
...
  toggleVisible(){
    this.visibleState = (this.visibleState == 'visible') ? 'invisible':'visible';
  }
...
{% endhighlight %}

**home.ts**에서 애니메이션을 생성해야합니다!! `@Component` 부분에 **animations** 배열을 만들어주고 toggleVisible이라는 기능을 만들어줍니다.

> 간단하게 코드리뷰를 하자면 **toggleVisible**에서 **visibleState** 값을 확인해서 `visible`이면 `invisible` 혹은 `invisible`이면 `visible`로 변환을 해주는 로직이며, **visibleState**값에 따라 **trigget**로 설정된 태그가 지정해준 값에 따라 동작하는 방식입니다.

즉, **trigger**는 검색바가 되고! 검색버튼에는 **toggleVisible** 기능을 넣어주면 됩니다!

### pages/home/home.html
{% highlight html linenos %}
...
<ion-searchbar [@mysearchbar]='visibleState' showCancelButton="True" placeholder="검색" animated="True">
</ion-searchbar>
<ion-buttons right>
  <button (click)="toggleVisible()" ion-button icon-only >
    <ion-icon name="search-icon" ></ion-icon>
  </button>
</ion-buttons>
...
{% endhighlight %}

이런식으로 하게 되면, 검색버튼을 누를때마다 **visibleState** 값이 바뀌면서 검색바가 나타났다 사라지고를 반복합니다.

![gif-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-06/gif_01.gif)

여기서 **transition** 효과를 추가해서 좀더 애니메이션 같이 만들어 보도록 하겠습니다.

### pages/home/home.ts

{% highlight typescript linenos %}
...
/* 추가 내용 */
import { trigger, style, state, transition, animate } from '@angular/core';
...
@Component({
  selector: 'page-home',
  templateUrl: 'home.html',
  animations:[
    trigger('mysearchbar', [
        state('visible', style({
          opacity: 1
        })),
        state('invisible', style({
          opacity: 0
        })),
        /* 추가 내용 */
        transition('* => *', animate('.5s'))
      ])
  ]
})
...
{% endhighlight %}

**transition**과 **animate** 모듈을 **import**해주고, **state**아래 다음과 같이 추가하면!!

![gif-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-06/gif_02.gif)

짜잔~ㅎㅎ 다음시간에는 **web-animation-js** 와 함께 사용하면 더욱 **인터렉티브**한 화면을 디자인 할 수 있는 라이브러리에 대해 소개하도록 하겠습니다~

## 다음 내용

- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/07/ionic3-tourplan-project-05)]
