---
title: Angular 설치 및 프로젝트 생성&amp;빌드하기
comments: true
description: Angular를 시작하기에 앞서  Angular CLI를 설치하고, 프로젝트를 만들어서 빌드하는 방법에 대한 포스팅입니다. 
categories:
 - Angular
tags: angular tutorial
---

## Angular CLI 설치하기

+ **Node.js 설치하기**
    * [[Node.js 다운로드 링크](https://nodejs.org/ko/download/)]

> Node.js가 설치되어 있어야 합니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop> npm install -g @angular/cli
{% endhighlight %}

커맨드창을 **관리자모드**로 실행한 다음 **Angular CLI**를 설치합니다.

{% highlight bash linenos %}
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.1.3 (node_modules\@angular\cli\no.....
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.3: wanted {"os.....
{% endhighlight %}

다음 같은 경고문이 나오는데 무시하고 해도 잘 실행 됩니다!

## 앱 생성 및 실행하기

{% highlight bash linenos %}
C:\Users\wk647\Desktop>
C:\Users\wk647\Desktop> mkdir my_new_project && cd my_new_project
C:\Users\wk647\Desktop\my_new_project>
{% endhighlight %}

바탕화면에 **my_new_project**폴더를 만들고 해당 폴더로 들어갑니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\my_new_project> ng new myapp
{% endhighlight %}

`ng new <생성할 앱 이름>`으로 앱을 생성합니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\my_new_project> cd myapp
C:\Users\wk647\Desktop\my_new_project\myapp> ng serve
{% endhighlight %}

생성된 myapp 폴터에서 `ng serve` 명령어를 통해 로컬서버로 앱을 실행 할 수 있습니다.

![앱 실행](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-31/ng_serve_01.png)

위 이미지와 같은 창이 뜨면 앱 실행 성공!

## 앱 빌드하기

이제 생성한 앱을 **배포**하기 위해 **빌드**를 해보도록 하겠습니다!

{% highlight bash linenos %}
C:\Users\wk647\Desktop\my_new_project\myapp> mkdir deploy
C:\Users\wk647\Desktop\my_new_project\myapp> ng build --prod --output-path /배포경로/public
{% endhighlight %}

배포파일을 저장할 `deploy` 파일을 생성합니다. `ng build` 로 아주 쉽게 앱을 빌드할 수 있습니다.
> 저는 **배포경로**를 `/User/wk647/Desktop/my_new_project/mpapp/deploy` 로 설정했습니다.

![앱 빌드](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-31/ng_build_01.png)

프로젝트 폴더에 가보면 요런식으로 되어있습니다.

## Angular 배포 준비하기

- [[**Angular 배포 준비하기** 포스팅 링크](https://wkddnjset.github.io/angular/2018/01/30/angular-deploy-cafe24-02/)]