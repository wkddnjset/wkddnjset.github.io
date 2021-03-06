---
title: (언리얼엔진) 모바일 디펜스게임 개발하기 - 1.환경설정
comments: true
description: 언리얼 엔진을 사용해서 모바일 게임을 개발하기 위해 환경을 세팅하는 과정을 정리해 보았습니다.
categories:
 - Unreal
tags: Unreal, Mobile, Game 
---

## Overview

언리얼 엔진을 모바일 환경으로 테스트할 수 있도록 환경설정을 해보았습니다.

- Unreal Engine v4.20.3
- CodeWorksforAndroid v1R6u1

## CodeWorksforAndroid 설치

우선 언리얼 엔진 4.20 버전을 설치합니다. 

> 최신 버전은 Anroid CodeWorks 버전이 맞지 않아 4.20버전으로 설치했습니다.

![unreal_set_1](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_1.png?raw=true)

설치가 끝나면 해당 경로로 이동하여 CodeWorksforAndroid 를 설치합니다.

> \Program Files\Epic Games\UE_4.20\Engine\Extras\AndroidWorks\Win64

## Unreal Engine 환경설정

### 프로젝트 생성

설치가 완료 되었으면, 환경설정을 하기 위해 프로젝트를 생성합니다. 
![unreal_set_2](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_2.png?raw=true)

저는 MobileTest라는 이름으로 프로젝트를 생성했습니다.

### Map 저장하기

![unreal_set_4](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_4.png?raw=true)

`Ctrl+Alt+s`를 눌러 기본으로 생성된 맵을 저장합니다. 경로는 위와 같이 Content/Maps에 Main이라는 이름으로 맵을 저장합니다.

### Project Settings 창 열기

![unreal_set_5](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_5.png?raw=true)

저의 경우 Project Settings 창이 탭으로 이미 열려있지만, 그렇지 않을 경우 Edit -> Project Settings를 눌러 세팅 창을 열어줍니다.

### Default Map 설정

![unreal_set_6](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_6.png?raw=true)

세팅 창에서 Project -> Maps & Modes 메뉴에서 Default Map을 설정해줍니다.

### Android Platforms 설정


안드로이드 설정을 위해 `Configure Now`를 선택 하고 빨간박스의 값들을 사진과 같이 바꿔주세요. 패키지 이름의 경우 본인이 직접 설정해주시고, 다 완료되면 최종적으로 `Accept SDK License` 버튼을 눌러 라이센스에 동의해주세요.

![unreal_set_7](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_7.png?raw=true)

![unreal_set_8](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_8.png?raw=true)

Android 설정이 완료되면, 위에서 설치했던 `CodeWorksforAndroid`가 설치된 파일을 Android SDK와 연결해야합니다. 아래 그림을 참고해서 진행해 주세요.

![unreal_set_9](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_9.png?raw=true)

### Test Android

이제 실제 안드로이트 폰으로 테스트를 진행 해보도록 하겠습니다. 우선 아래 그림을 따라 Android(ATC)로 프로젝트를 저장해주세요.

![unreal_set_10](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_10.png?raw=true)

패키징이 완료되면, 저장한 경로에 아래와 같은 파일들이 생성됩니다.

![unreal_set_11](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/unreal_set_11.png?raw=true)

맨 위에 `install_..` 파일을 실행하면 PC와 연결된 안드로이드 폰에 개발한 게임이 설치가 됩니다.

> 연결된 안드로이드 폰은 개발자 모드로 세팅 되어있어야 합니다. [[여기](http://editorizer.tistory.com/224)]를 참고해서 작업해주세요. 