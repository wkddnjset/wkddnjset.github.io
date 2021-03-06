---
title: ionic 3 앱 개발 (사이드메뉴&amp;탭 결합) - [여행플랜/프런트]
comments: true
description: Photoshop으로 디자인한 안드로이드 UI를 ionic 3를 이용해 프런트 개발을 해보도록하겠습니다. 나눠서 포스팅 할 것이며, 이번 포스팅에서는 사이트메뉴와 탭을 결합하는 방법에 대해 포스팅 하도록하겠습니다.
categories:
 - Ionic
tags: ionic app develop frontend
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](http://jangwon.io/ionic/2018/02/03/ionic3-tourplan-project-01/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]


## 디자인

![design-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-01/design_01.png){: width="400px"}

디자인 관련 내용은 [[여행플랜/디자인](https://wkddnjset.github.io/photoshop/2018/02/02/ionic3-tourplan-project-01/)]에서 확인 하실 수 있습니다.

## ionic 프로젝트 생성

{% highlight bash linenos %}
C:\Users\wk647\Desktop> ionic start Tourplan sidemenu
C:\Users\wk647\Desktop> cd Tourplan 
C:\Users\wk647\Desktop\Tourplan>
{% endhighlight %}

바탕화면에 `sidemenu` 설정으로 **ionic** 앱을 생성한 다음 해당 폴더로 이동합니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourplan> ionic g page itemone
C:\Users\wk647\Desktop\Tourplan> ionic g page itemtwo
C:\Users\wk647\Desktop\Tourplan> ionic g page itemthree
{% endhighlight %}

사이드 메뉴를 `Default`로 생성했기 때문에 탭을 구성할 페이지를 **생성**합니다.

![img-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-04/img_01.png)

**사이드 메뉴**와 **탭**을 함께 사용하는 방식을 위에..그림처럼 ㅎㅎㅎ 제가 직접 그렸는데 아주 이해하기 쉽지않나요ㅋㅋ
직접 만들면서 이해를 높혀봅시다.
> **APP**에 사이트메뉴를 나타내는 **ListPage**랑 메인페이지인 **HomePage**로 구성되고 메인페이지에 탭을 구성하는 **Tab1Page**, **Tab2Page**, **Tab3Page**를 **Import**시키는 구조로 생각하시면 됩니다.

### **src/app/app.module.ts**
{% highlight typescript linenos %}
...
import { ItemonePage } from '../pages/itemone/itemone';
import { ItemtwoPage } from '../pages/itemtwo/itemtwo';
import { ItemthreePage } from '../pages/itemthree/itemthree';

declarations: [
    MyApp,
    HomePage,
    ListPage,
    ItemonePage,
    ItemtwoPage,
    ItemthreePage
  ],
  ...
  entryComponents: [
    MyApp,
    HomePage,
    ListPage,
    ItemonePage,
    ItemtwoPage,
    ItemthreePage
  ],
  ...
{% endhighlight %}

생성한 페이지를 사용하기 위해서 **app.module.ts**에 생성한 페이지들을 **Import** 해줘야합니다.
> **Import** 시 생성한 페이지 **capitalization**을 해줘야 합니다. 그리고 뒤에 **Page**를 붙혀야만 합니다!! 안그러면 에러가 뜨더라구요.

### **page/home/home.ts**
{% highlight typescript linenos %}
...
import { ItemonePage } from '../itemone/itemone';
import { ItemtwoPage } from '../itemtwo/itemtwo';
import { ItemthreePage } from '../itemthree/itemthree';

export class HomePage {
    tab1Root = ItemonePage;
    tab2Root = ItemtwoPage;
    tab3Root = ItemthreePage;
  ...
{% endhighlight %}

**HomePage**에 탭을 구성하는 페이지를 **RootPage**로 **Import**해줍니다!!

### **page/home/home.html**
{% highlight html linenos %}
<ion-content>
...
  <ion-tabs>
    <ion-tab [root]="tab1Root" tabTitle="item one" tabIcon="home"></ion-tab>
    <ion-tab [root]="tab2Root" tabTitle="item two" tabIcon="information-circle"></ion-tab>
    <ion-tab [root]="tab3Root" tabTitle="item three" tabIcon="contacts"></ion-tab>
  </ion-tabs>
</ion-content>
{% endhighlight %}

이제 **RootPage를** **Html**로 불러오면!! 되겠죠??ㅎㅎ

![ionic-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-04/ionic_01.png){: width="400px"}

저를 따라하셧다면 위와 같은 이미지가 나올 겁니다!!!탭 페이지들이 **HomePage** 내부에 있기때문에 지금 **header가** 중복되서 나오는 것 처럼 보입니다. 각 탭 페이지 **Html**로 가서 **header**를 지워주세요!

![ionic-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-04/ionic_02.png){: width="400px"}

최종 결과물입니다~ 이제 기본 세팅이 끝났습니다!!

## Github

- [[미니프로젝트 여행플랜 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]
- [[ionic 3 사이드메뉴&탭 결합 기본세팅소스](https://github.com/wkddnjset/ionic3-combine-sidemenu-and-tabs)]

## 다음 내용

- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](http://jangwon.io/ionic/2018/02/05/ionic3-tourplan-project-03/)]
