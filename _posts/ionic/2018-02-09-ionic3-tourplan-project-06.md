---
title: ionic 3 앱 개발 (드롭다운) - [여행플랜/프런트]
comments: true
description: 이번에는 PopoverController API를 사용해 Popover(드롭다운) 버튼을 만들어 보도록하겠습니다~
categories:
 - Ionic
tags: ionic app develop frontend
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](http://jangwon.io/ionic/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](http://jangwon.io/ionic/2018/02/04/ionic3-tourplan-project-02/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](http://jangwon.io/ionic/2018/02/05/ionic3-tourplan-project-03/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](http://jangwon.io/ionic/2018/02/06/ionic3-tourplan-project-04/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](http://jangwon.io/ionic/2018/02/07/ionic3-tourplan-project-05/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

![result-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-07/result_02.gif)

저번 포스팅에는 애니메이션 부분을 구현해 보았습니다.

## Popover(드롭다운)

![Popover-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-09/dropdown_01.png){: width="400px"}

이번 포스팅은 빨간 박스로 친 부분을 드롭다운 형식으로 구현해보도록 하겠습니다.

## Popover 페이지 생성

{% highlight bash linenos %}
C:\Users\wk647\Desktop\Tourpaln> ionic generate page popover
{% endhighlight %}

**PopoverController API**를 사용하기 위해 **popover** 페이지를 생성합니다.

### app/app.module.ts

{% highlight typescript linenos %}
...
import { PopoverPage } from '../pages/popover/popover';
...
declarations: [
    ...
    PopoverPage
],
entryComponents: [
    ...
    PopoverPage
  ]
...
{% endhighlight %}

**app.module.ts**에 생성한 **PopoverPage**를 추가합니다.

### itemone/itemone.ts

{% highlight typescript linenos %}
...
import { IonicPage, NavController, NavParams, PopoverController  } from 'ionic-angular';
import { PopoverPage } from '../popover/popover';
...
export class ItemonePage {
  selectedData:any;
  constructor(public navCtrl: NavController, 
    public navParams: NavParams, 
    public popoverCtrl: PopoverController) {
  }
  presentPopover(myEvent) {
    /* 목적지에 따른 아이템을 보여주기 위한 드롭다운 메뉴이므로 각 나라를 목적지로 설정했습니다. */
    let listData = [{title:"KOREA",id:1},{title:"JAPAN",id:2},{title:"CHINA",id:3},{title:"UNITESTATE",id:4}]
    let popover = this.popoverCtrl.create(PopoverPage, { listData });
    popover.present({
      ev: myEvent
    });
    /* PopoverPage에서 가져온 데이터를 변수에 저장하는 부분입니다. */
    popover.onDidDismiss(data => {
      if(data!=null){
         this.selectedData = data.title
      }
      else{
        this.selectedData = "DESTINATION"
      }
      console.log(this.selectedData)
    });
  }
}
{% endhighlight %}

**PopoverController**을 통해서 **Json** 데이터를 **PopoverPage**와 함께 파라미터로 넘겨줍니다. **PopoverPage**에서 **ItemonePage**에서 **data**를 전달받아 **selectedData**라는 변수에 저장합니다.

> 이때 저는 타이틀 정보만 받아서 넣었습니다!! 그리고 만약 아무것도 누르지않으면 **DESTINATION**을 기본 값으로 저장하게 로직을 구성했습니다.

### popover/popover.ts

{% highlight typescript linenos %}
...
import { IonicPage, NavController, NavParams, ViewController } from 'ionic-angular';
...
export class PopoverPage {
  items:any;
  constructor(
    public navCtrl: NavController, 
    public navParams: NavParams,
    public viewCtrl: ViewController,
    ) {
    /* NavParams를 이용해 ItemonePage에서 보낸 listData를 받아옵니다. */
    this.items = this.navParams.get('listData');
    // console.log(this.items)
  }
  /* ViewController로 data를 PopoverPage로 보냅니다. */
  dismiss(item) {
    let data = item;
    this.viewCtrl.dismiss(data);
  }
}
{% endhighlight %}

**NavParams**로 ListData를 받고, **ViewController**를 사용해 Data를 넘깁니다.

### popover/popover.html

{% highlight html linenos %}
<ion-content >
    <ion-list>
      <ion-item *ngFor="let item of items" (click)="dismiss(item)">
        <ion-label>{{item.title}}</ion-label> 
      </ion-item>
    </ion-list>
</ion-content>
{% endhighlight %}

**PopoverPage**에서 파싱한 **listData**를 **For문**을 돌려 출력합니다. 이때 **.ts**에서 생성한 `dismiss함수`를 click옵션으로 적용시켜 해당 아이템이 클릭되면 데이터를 **ItemonePage**로 보내도록 만듭니다.

### itemone/itemone.html

{% highlight html linenos %}
...
<div id="dropdown-head">
        <div class="dropdown-title" *ngIf= "selectedData">{{ selectedData }}</div>
        <div class="dropdown-title" *ngIf= "!selectedData">DESTINATION</div>
...
{% endhighlight %}

조건문을 사용해 **PopoverPage**에서 부터 데이터가 넘어오면 해당 데이터의 타이틀을 출력하고, 그렇지 않을 경우 **DESTINATION**을 출력합니다.

![Result_01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-09/result_01.gif)

CSS를 바꿔서 디자인을 좀 수정했습니다. CSS에 대한 내용은 따로 포스팅 하지 않고 **[[Github](https://github.com/wkddnjset/MiniProject-TourPlan)]**에 올려두도록 하겠습니다!

## 다음 내용

- [[ionic 3 앱 개발 (슬라이드) 포스팅](http://jangwon.io/ionic/2018/02/11/ionic3-tourplan-project-07)]





