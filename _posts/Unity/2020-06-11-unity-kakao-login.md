---
title: Unity로 카카오 로그인 구현하기
comments: true
description: Unity에서 안드로이드로 카카오 로그인을 구현하는 방법에 대한 정리 입니다.
categories:
  - Unity
tags: unity kakao kakao-login
---

## 목차

- Manifest 파일과 Custom Gradel 파일 세팅
- Kakao 개발자 센터에 접속해서 App 등록하기
  1. App 생성하기
  2. Android 플랫폼 등록
  3. 키 해시 생성 및 등록하기
- Android에 Unity classes.jar import 하기
  1. Unity classes.jar 복사 하기
  2. Android Module 생성
  3. classes.jar import 하기
- Unity와 Kotlin 연동하기

## Custom Gradel 파일과 Manifest 파일 세팅

![step-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step1.png)

`Edit` > `Project Settings` > `Publish Settings` > `Build` 에서 이미지와 같이 `Custom Main Mainfest`, `Custom Main Gradle Template`, `Custom Base Gradle Template`를 체크해줍니다.

> `Build`를 하면 `Assets` > `Plugins`에 `Android` 폴더가 생성된걸 확인할 수 있으며, `AndroidManifest.xml`, `mainTemplate.gradle`, `baseProjectTemplate.gradle` 파일 이 생성된걸 확인할 수 있습니다.

## Kakao 개발자 센터에 접속해서 App 등록하기

1. App 생성하기

![step-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step2.png)

2. Android 플랫폼 등록

![step-03](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step3.png)

패키지 명의 경우 `Edit` > `Project Settings` > `Other Settings` 에서 확인할 수 있습니다.

3. 키 해시 생성 및 등록하기

- 우선 안드로이드 프로젝트를 생성합니다.

![step-04](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step4.png)

Unity에서 사용한 패키지 명과 동일하게 세팅해주세요.

- Android MainActivity 부분을 수정합니다.

```
...
import android.util.*
import java.security.MessageDigest
import java.security.NoSuchAlgorithmException


class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        getKeyHash()
    }
    // getKeyHash 함수를 만들어 줍니다.
    fun getKeyHash() {
        val packageInfo: PackageInfo = packageManager.getPackageInfo(packageName, PackageManager.GET_SIGNATURES)
        for (signature in packageInfo.signatures) {
            try {
                val md: MessageDigest = MessageDigest.getInstance("SHA")
                md.update(signature.toByteArray())
                Log.d("Keyhash",  Base64.encodeToString(md.digest(), Base64.NO_WRAP))
            } catch (e: NoSuchAlgorithmException) {
            }
        }
    }
}
```

App 실행 후, 하단에 `Logcat`에서 `Keyhash`를 검색하면 아래 이미지 처럼 `키 해시 값`을 확인 할 수 있습니다. 해당 값을 카카오에 등록해주세요!

![step-05](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step5.png)

## Android에 Unity classes.jar import 하기

1. Unity classes.jar 복사 하기

`Unity` > `Hub` > `Editor` > `[VERSION]` > `PlaybackEngines` > `AndroidPlayer` > `Variations` > `mono` > `Release` > `Classes` 에 있는 `classes.jar`를 복사합니다.

2. Android Module 생성

![step-06](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step6.png)

`app`을 우클릭 하여 Module 을 생성합니다. 모듈이름은 `unity-kakao-login`으로 생성하였습니다.

3. classes.jar import 하기

![step-07](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step7.png)

좌측 프로젝트 디렉토리 바에서 `Android`를 `Project` 로 변경한 뒤, 생성한 모듈에 `lib` 파일에 Unity에서 복사한 `classes.jar`를 불러옵니다.

![step-08](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step8.png)

`File` > `Project Structure` > `Dependencies` > `unity-kakao-login` 에서 상단 `+` 버튼을 눌러 `Jar Dependency`를 추가합니다.

![step-09](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/kakao-login-step9.png)

앞서 복사한 경로인 `lib/classes.jar`를 선택하면 완료!

## Kakao 기본 세팅

1. Android에 세팅하기

- `android/gradle`

```
allprojects {
  repositories {
    ...
    // 카카오 레파지토리
    maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
  }
}

```

- `android/unity-kakao-login/gradle`

```
dependencies {
  ...
  // 카카오 로그인을 위한 플러그인
  implementation group: 'com.kakao.sdk', name: 'usermgmt', version: '1.29.0'
}
```

- `android/unity-kakao-login/manifest`

```
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET"/>
</manifest>
```

2. Unity에 세팅하기

- `Assets/Android/baseProjectTemplate`

```
allprojects {
  ...
  repositories {
    ...
    // 카카오 레파지토리
    maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
  }
}
```

- `Assets/Android/mainTemplate`

```
dependencies {
 ...
 implementation group : 'com.kakao.sdk', name: 'usermgmt', version: '1.29.0'
}
```

- `Assets/Android/AndroidMainfest.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
	<application ...>

	</application>
</manifest>
```
