---
title: 카페24에서 Node.js 서버로 Angular 배포하기
comments: true
description: 카페24에서 제공해주는 Node.js 서버를 이용하여 로컬에서 개발한 Angular를 배포하는 방법에 대한 포스팅입니다.
categories:
  - Angular
tags: angular tutorial deploy
---

## 카페24에서 Node.js 서버 구매

- **카페24 호스팅 구매하기 - 서버 선택하기**

![Cafe24 Node.js 호스팅-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/cafe24_01.png)

- **카페24 호스팅 구매하기 - 서버 신청하기**

![Cafe24 Node.js 호스팅-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/cafe24_02.png)

전 그지 깽꺵이라 제일 싼걸로 신청하겠습니다 ㅠ

- **카페24 호스팅 구매하기 - 서버 결제하기**

![Cafe24 Node.js 호스팅-03](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/cafe24_03.png)

여기서부터는 차례대로 하시면 됩니다. 필요한건 **돈** (돈 많이 벌자!)

## Node.js 서버에 앱 생성하기

- **앱 생성하기 - 나의서비스관리**

![앱 생성하기-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/app_01.png)

[[카페24 홈페이지](https://www.cafe24.com)]에서 로그인을 하고 **나의서비스관리**에 들어갑니다!!

- **앱 생성하기 - 서버 선택하기**

![앱 생성하기-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/app_02.png)

제일 상단에서 구입한 서버를 선택합니다.

- **앱 생성하기 - 메뉴 선택하기**

![앱 생성하기-03](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/app_03.png)

우측 메뉴에서 **앱 생성/관리**를 선택합니다.

- **앱 생성하기 - 앱 생성하기**

![앱 생성하기-04](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/app_04.png)

서버로 배포할 어플리케이션의 이름을 적어주시고 **Node.js 앱 만들기 버튼**을 눌러 앱을 생성합니다.
**<앱이름>.cafe24app.com** 은 배포되는 도메인 입니다. 앱 생성 후 변경이 가능합니다!!

- **앱 생성하기 - 앱 확인하기**

![앱 생성하기-05](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/app_05.png)

이제 앱이 생성되었습니다. 카페24 Node.js 서버는 **FTP**가 아닌 **Git**으로 파일 관리를 하기에 앱을 생성하면 Git 저장소도 동시에 생성됩니다.

> 혹시 **앱 생성/관리** 메뉴를 눌러도 아무것도 안되는 현상이 발생하게 되면.. 카페24 고객센터에 문의하세요. 저도 그렇게 해결 했습니다.

## public key 생성하기

- **[[카페24 public key 생성 가이드 링크](https://help.cafe24.com/cs/cs_manual_view.php?idx=46&page=1&categoryIdx=509&s_key=&s_value=&man_no=1)]**

      	+ **Git 설치하기**
      		* [[Git 다운로드 링크](https://git-scm.com/downloads)]

      	+ **Node.js 설치하기**
      		* [[Node.js 다운로드 링크](https://nodejs.org/ko/download/)]

> 위 링크를 통해서 Git과 Node.js를 설치해 주세용~

- **ssh public key 생성 - Git 실행하기**

![ssh public key-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/ssh_01.png)

검색창에 **`Git Bash`**를 검색해서 실행시켜주세요.

- **ssh public key 생성**

{% highlight bash linenos %}
wk647@DESKTOP-ELCDM29 MINGW64 ~
\$ ssh-keygen -t ras -C "wkddnjset@naver.com"
{% endhighlight %}

위 명령어는 ssh public key를 생성하는 명령어 입니다. 뒤에 이메일은 **키 명칭**인데 굳이 이메일이 아니여도 됩니다.

{% highlight bash linenos %}
wk647@DESKTOP-ELCDM29 MINGW64 ~
\$ ssh-keygen -t ras -C "wkddnjset@naver.com"
Generating puublic/private rsa key pair.
Enter file in which to save the key (/c/Users/wk647/.ssh/id_rsa):
{% endhighlight %}

생성된 Key 파일이 저장될 **경로**를 입력합니다. `Enter`를 누르면 자동으로 저장되니 `Enter`를 눌러주세요!

{% highlight bash linenos %}
...
Generating puublic/private rsa key pair.
Enter file in which to save the key (/c/Users/wk647/.ssh/id_rsa):
/c/User/wk647/.ssh/id_rsa already exists.
Overwrite (y/n)? y
{% endhighlight %}

입력한 경로에 이미 Key가 저장되어 있어서 **덮어쓰기** 여부를 확인하는 겁니다.

{% highlight bash linenos %}
...
Enter file in which to save the key (/c/Users/wk647/.ssh/id_rsa):
/c/User/wk647/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for. no passphrase):
{% endhighlight %}

비밀번호를 입력하고, 비밀번호 확인을 입력하면 생성완료!

![ssh public key-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/ssh_06.png)

위와 같은 창이 뜨면 성공한겁니다!!!

![ssh public key-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/ssh_07.png)

저장된 경로에 가면 **3개**의 파일이 생성된 걸 확인하실 수 있으실 겁니다.

## public key 등록하기

- **ssh public key 등록 - key 복사하기**

![ssh public key-03](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/register_01.png)

shh key를 저장한 경로에서 **[id_rsa.pub]**파일을 메모장으로 열어서 내용을 `Ctrl+C`합니다.

- **ssh public key 등록 - 메뉴 선택하기**

![ssh public key-04](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/register_02.png)

다시 **나의서비스관리**로 가서 우측 상단에 **Public key 관리**메뉴를 선택합니다.

- **ssh public key 등록 - key 등록하기**

![ssh public key-05](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/register_03.png)

위 이미지처럼 입력 후 **Public key**를 등록해주세요. key 이름은 아무거나 하셔도 됩니다.

- **ssh public key 등록 - key 확인하기**

![ssh public key-06](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/register_04.png)

**Key 리스트**에서 등록하신 Public key를 확인하실 수 있습니다.

- **ssh public key 등록 - key 할당**

![ssh public key-06](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/register_05.png)

**앱 생성/관리하기** 메뉴로 이동 후 생성한 앱을 선택하여 **key 할당**탭을 클릭합니다.

![ssh public key-06](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/register_06.png)

**사용 가능한 키**를 **사용중인 키**로 이동 후 확인을 눌러주세요.

## Angular 빌드하기

- [[**Angular 빌드하기** 포스팅 링크](https://wkddnjset.github.io/angular/2018/01/30/angular-deploy-cafe24-02/)]

## Angular 배포 준비하기

- [[**Angular 배포 준비하기** 포스팅 링크](https://wkddnjset.github.io/angular/2018/01/31/angular-deploy-cafe24-03/)]

## Git 저장소에 업로드하기

{% highlight bash linenos %}
C:\Users\wk647\Desktop\my_new_project\myapp\deploy> git init
C:\Users\wk647\Desktop\my_new_project\myapp\deploy> git remote add origin 저장소
C:\Users\wk647\Desktop\my_new_project\myapp\deploy> git pull origin master
{% endhighlight %}

![deploy-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/deploy_01.png)

`저장소`는 **앱 생성/관리하기** 메뉴에 저장소에서 앞에 "git"을 빼고 적어주시면 됩니다.

> 저 같은 경우 `tryangular@tryangular.cafe24app.com:tryangular_tryangular` 입니다.

{% highlight bash linenos %}
C:\Users\wk647\Desktop\my_new_project\myapp\deploy> git add -A
C:\Users\wk647\Desktop\my_new_project\myapp\deploy> git commit -m "upload"
C:\Users\wk647\Desktop\my_new_project\myapp\deploy> git push origin master
{% endhighlight %}

저장소를 `remote`하고 업로드하는 방식은 기존에 Github를 사용하는 방법과 동일하게 진행하면 됩니다.

## 앱 실행하기

![start_app-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/start_app_01.png)

**실행**버튼을 눌러 앱을 실행 시켜주세요!

![start_app-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-01-30/start_app_02.png)

**앱 상태**가 `처리중`에서 `실행`으로 바뀌면 웹 브라우저에 등록된 도메인을 치면!!! 배포 성공입니다!!
