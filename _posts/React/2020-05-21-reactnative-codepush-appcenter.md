---
title: CodePush를 사용해 AppCetner로 안드로이드 배포하기 [ReactNative]
comments: true
description: CodePush를 사용해서 AppCetner로 안드로이드를 배포하는 글입니다. ReactNative 기반으로 작성되었습니다.
categories:
  - React
tags: react react-native appcenter codepush
---

## AppCenter 세팅하기

- 계정 생성

[AppCenter 홈페이지](https://appcenter.ms/)에서 계정을 생성하고 로그인 합니다.

- 라이브러리 설치하기

> [공식 문서 Getting Started](https://docs.microsoft.com/en-us/appcenter/sdk/getting-started/react-native)

```
$ npm install appcenter appcenter-analytics appcenter-crashes --save-exact
```

```
$ yarn add appcenter appcenter-analytics appcenter-crashes --exact
```

## CodePush 세팅하기

- 라이브러리 설치

> [code-push github](https://github.com/microsoft/react-native-code-push) > [code-push github #AndroidSetup](https://github.com/microsoft/react-native-code-push/blob/master/docs/setup-android.md)

```
$ npm install --save react-native-code-push
$ npm install -g code-push-cli
```

```
$ yarn add --save react-native-code-push
$ yarn global add code-push-cli
```

- `android/settings.gradle`

> `settings.gradle` 파일 최하단에 아래와 같이 입력해주세요.

```
...
// include ':app'
include ':app', ':react-native-code-push'
project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
```

- `android/app/build.gradle`

> `Cannot add task ‘bundleDebugJsAndAssets’ as a task with that name already exists.` 에러가 발생하면 apply from: “../../node_modules/react-native/react.gradle” 이 부분은 제거해주세요.

```
...
apply from: "../../node_modules/react-native/react.gradle"
apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
```

- `android/app/src/.../MainApplication.java`

```
...
// 1. CodePush 패키지를 Import 해주세요.
import com.microsoft.codepush.react.CodePush;

public class MainApplication extends Application implements ReactApplication {

    private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
        ...

        // 2. getJSBundleFile을 Override 해주세요.
        @Override
        protected String getJSBundleFile() {
            return CodePush.getJSBundleFile();
        }
    };
}
```
