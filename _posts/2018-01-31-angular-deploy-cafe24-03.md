---
title: Angular 배포 준비하기
comments: true
description: Angular를 배포하기 위해 빌드를 하고 서버가 인식할 수 있도록 파일 설정들을 변경하는 방법에 대한 포스팅입니다.
categories:
 - Angular
tags: angular tutorial
---

## Angular 빌드하기
- [[**Angular 빌드하기** 포스팅 링크](https://wkddnjset.github.io/angular/2018/01/30/angular-deploy-cafe24-02/)]

## 세팅 파일 만들기

* **procfile**

{% highlight plain text linenos %}
web: node web.js
{% endhighlight %}

**procfile** 뒤에 확장자명은 붙지 않습니다.

* **package.json**

{% highlight bash linenos %}
C:\Users\wk647\Desktop\my_new_project\myapp> npm init
...
{% endhighlight %}

앱을 생성한 폴더에서 `npm init` 명령어 후 계속 엔터를 치면 **package.json** 파일을 만들 수 있습니다.

{% highlight json linenos %}
{
  "name": "myapp",
  "version": "0.0.0",
  "license": "MIT",
  "scripts": {
    "ng": "ng",
...
{% endhighlight %}

**json**파일을 확인해 보면 위와 같은 포맷으로 정의되어 있을 겁니다. **package.json**파일을 배포 파일을 저장하는 폴더로 옮겨 주세요.

* **web.js**

{% highlight javascript linenos %}
var express = require('express');
var path = require('path');
var app = express();

const port = process.env.PORT || '5000';

app.set('port', port);
app.use(express.static(__dirname + '/public'));

app.get('/[^\.]+$', function(req, res) {
    res.set('Content-Type', 'text/html')
        .sendFile(path.join(__dirname, '/public/index.html'))
});

app.listen(app.get('port'), function() {
  console.log("Node app is running at localhost:" + app.get('port'))
});
{% endhighlight %}

이렇게 만들어진 3개의 파일을 배포 파일을 저장하는 폴더로 옮겨주세요.

![deploy-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-31/deploy_01.png)

배포 파일을 저장하는 `deploy` 폴더에 이미지와 같은 파일들이 있으면 준비가 끝난겁니다!!

## 카페24로 Angular 배포하기

- [[**카페24로 Angular 배포하기** 포스팅 링크](https://wkddnjset.github.io/angular/2018/01/30/angular-deploy-cafe24-02/#git-저장소에-업로드하기)]
