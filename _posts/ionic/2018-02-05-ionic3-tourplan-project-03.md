---
title: ionic 3 앱 개발 (탭 아이콘 변경) - [여행플랜/프런트]
comments: true
description: zeplin에서 아이콘을 SVG형태로 추출하고, SVG파일을 Font awesome과 같은 아이콘 형태의 폰트로 만들어 ionic에 적용하는 방법에 대한 포스팅입니다.
categories:
 - Ionic
tags: ionic app develop frontend
---

## 이전 내용

- [[ionic 3 앱 디자인 (여행플랜) 포스팅](http://jangwon.io/ionic/2018/02/03/ionic3-tourplan-project-01/)]
- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](http://jangwon.io/ionic/2018/02/04/ionic3-tourplan-project-02/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]


![ionic-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-04/ionic_02.png){: width="400px"}

여기까지 완성되었습니다. 이제 탭 네비게이션에서 아이콘들을 바꿔보도록 하겠습니다.

## 재플린에서 아이콘 추출

![zeplin-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/export_01.png)

재플린에서 아이콘을 추출하기 위해서는 포토샵에서 해당 아이콘에 대한 레이어들을 `Ctrl+g`로 그룹으로 만든 다음 그룹명 앞에 `-e-`를 붙여주세요.

![zeplin-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/export_02.png)

그룹명 앞에 `-e-`를 붙이게 되면, 위에 사진처럼 해당 아이콘을 **PNG**, **SVG**로 다운받을 수 있습니다.

## SVG파일을 폰트로 만들기

![zeplin-03](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/export_03.png)

재플린에서 폰트로 만들 아이콘들을 **SVG**로 출력하세요! 

![import-icon-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/import_icon_01.png)

[[icomoon - 폰트 만들어주는 사이트](https://icomoon.io/app/#/select)]
링크로 이동 한 뒤에 **SVG** 파일들을 **업로드** 해줍니다!

![import-icon-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/import_icon_02.png)

위 처럼 아이콘을 **업로드** 했다면 **출력할 아이콘**을 선택하고 아래 **Generate Font** 버튼을 눌러서 폰트로 만들어주세요!!

## 아이콘 변경하기

![change-icon-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/change_icon_01.png)

`/Desktop/Tourplan/src/assets/`경로에 만들어진 **icomoon.zip** 압축파일을 풀어주세요.

![change-icon-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/change_icon_02.png)

`demo.html`을 실행시키면 어떤 아이콘들이 있는지 확인할 수 있습니다!

### **/pages/home/home.scss**

{% highlight css linenos %}
@font-face {
    font-family: 'icomoon';
    src: url('../assets/fonts/icomoon.eot?3e91zf');
    src: url('../assets/fonts/icomoon.eot?3e91zf#iefix') format('embedded-opentype'),
    url('../assets/fonts/icomoon.ttf?3e91zf') format('truetype'),
    url('../assets/fonts/iconmoon/icomoon.woff?3e91zf') format('woff'),
    url('../assets/fonts/icomoon.svg?3e91zf#icomoon') format('svg');
    font-weight: normal;
    font-style: normal;
}
 
@mixin makeIcon($arg, $val) {
    .ion-ios-#{$arg}:before ,
    .ion-ios-#{$arg}-circle:before ,
    .ion-ios-#{$arg}-circle-outline:before ,
    .ion-ios-#{$arg}-outline:before ,
    .ion-md-#{$arg}:before ,
    .ion-md-#{$arg}-circle:before ,
    .ion-md-#{$arg}-circle-outline:before ,
    .ion-md-#{$arg}-outline:before {
    font-family: "icomoon" !important;
    content: $val;
    }
}
/* 아이콘을 입력해주세요 */
@include makeIcon(icon-icon-off , '\e902');
@include makeIcon(menu-icon , '\e904');
@include makeIcon(search-icon , '\e905');
@include makeIcon(sort-icon , '\e906');
@include makeIcon(tag-icon , '\e907');
@include makeIcon(dropdown-icon , '\e900');
/* 아이콘을 입력해주세요 */
page-home {
}
{% endhighlight %}

아이콘을 입력할 페이지에 scss파일을 다음처럼 수정해주세요!
그리고 중간에 아이콘을 입력하는 부분은 `demo.html`을 참고해서 입력해주시면 됩니다.

### **/pages/home/home.html**

{% highlight html linenos %}
...
<button ion-button menuToggle>
    <ion-icon name="menu-icon"></ion-icon>
</button>
...
<ion-tabs>
    <ion-tab [root]="tab1Root" tabTitle="item one" tabIcon="icon-icon-off">
    </ion-tab>
    <ion-tab [root]="tab2Root" tabTitle="item two" tabIcon="icon-icon-off">
    </ion-tab>
    <ion-tab [root]="tab3Root" tabTitle="item three" tabIcon="icon-icon-off">
    </ion-tab>
</ion-tabs>
...
{% endhighlight %}

**html**에 이렇게 **icon**을 추가하면!!

![result-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/result_01.png){: width="400px"}

짜자잔~

### **/pages/home/home.html**

{% highlight html linenos %}
...
<ion-navbar>
    <ion-buttons left>
      <button ion-button menuToggle>
        <ion-icon name="menu-icon"></ion-icon>
      </button>
    </ion-buttons>
    <ion-buttons right>
        <button ion-button icon-only>
            <ion-icon name="search-icon"></ion-icon>
        </button>
    </ion-buttons>
</ion-navbar>
...
{% endhighlight %}

**header**에 검색 버튼을 추가하고, `ion-buttons`태그를 사용해 양쪽에 배치하도록하겠습니다. 디자인에 맞춰서 타이틀도 지워주세요!

![result-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-05/result_02.png)

!!!

## Github

- [[미니프로젝트 여행플랜 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]
- [[ionic 3 사이드메뉴&탭 결합 기본세팅소스](https://github.com/wkddnjset/ionic3-combine-sidemenu-and-tabs)]

## 다음 내용

- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](http://jangwon.io/ionic/2018/02/06/ionic3-tourplan-project-04)]
