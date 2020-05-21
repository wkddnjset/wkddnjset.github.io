---
title: CodePush를 사용해 AppCetner로 안드로이드 배포하기 [ReactNative]
comments: true
description: CodePush를 사용해서 AppCetner로 안드로이드를 배포하는 글입니다. ReactNative 기반으로 작성되었습니다.
categories:
  - React
tags: react react-native appcenter codepush
---

## CodePush 세팅하기

- 라이브러리 설치

> [code-push github](https://github.com/microsoft/react-native-code-push) > [code-push github #AndroidSetup](https://github.com/microsoft/react-native-code-push/blob/master/docs/setup-android.md)

```
$ npm install --save react-native-code-push
```

```
$ yarn add --save react-native-code-push
```

- `android/settings.gradle`

> `settings.gradle` 파일 최하단에 아래와 같이 입력해주세요.

```
...
// include ':app'
include ':app', ':react-native-code-push'
project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
```
