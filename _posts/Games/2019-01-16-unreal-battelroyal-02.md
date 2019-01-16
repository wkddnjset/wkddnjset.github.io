---
title: (언리얼엔진) 배틀그라운드 따라만들기 - 1.폴리지
comments: true
description: 배틀그라운드 따라 만들기 강좌를 보고 내용을 정리했습니다. 게임 배경에 폴리지를 사용해 나무를 심는 과정을 진행해보았습니다.
categories:
 - Unreal
tags: Unreal, Game 
---

## Overview

+) 4.20버전으로 진행하였습니다.

[언론데브](https://alonedev.com/)에 유료 강좌 **배틀그라운드 만들기 11~14강**를 보고 정리한 내용입니다.

## 폴리지

### 폴리지란?

Static Mesh를 빠르게 배치하고 싶을때 사용하는 기능입니다.

> 나무나, 잔디를 심을때 사용한다고 하네요.

### 엣셋 추가하기

![foliage_1](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-16_battle_01.png?raw=true)

언리얼 엔진의 Migration 기능을 사용해 미리 추출해 놓은 파일을 그대로 복사해서 프로젝트 폴더에 있는 Content 폴더에 붙쳐넣기를 하면 아래처럼 언리얼 엔진에 자동으로 Import 되는 것을 확인 할 수 있습니다.

![foliage_2](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-16_battle_02.png?raw=true)

> 엣셋이 필요하신분은 댓글에 메일 주소 남겨주시면 제가 보내드리도록 하겠습니다! 에픽게임즈의 연날리기 데모에서 가져오셨다고 합니다!

### 폴리지 등록

![foliage_3](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-16_battle_03.png?raw=true)

입력시킨 엣셋 폴더 하위에 Foliage 폴더 생성한 다음 Foliage Type에 Mesh를 생성해줍니다.
해당 옵션을 더블클릭 한 다음, 아래와 같이 세팅해주세요.

- Align to Normal : 지형의 경사도에 맞춰서 Mesh를 생성할지, 수직으로 생성할지에 대한 T/F
- Randon Yaw : Mesh의 방향을 랜덤하게 할 것 인지에 대한 T/F
- Ground Slope Angle : Mesh를 생성할 수 있는 최소, 최대의 경사각도 입력
- Cull Distance : Mesh 간의 거리를 설정
- Collision : Mesh의 충돌에 대한 설정 (BlockAll -> 나무)

### 폴리지 옵션
	- 특정 렌드스케이프 레이어에만 폴리지 생성하기

### 폴리지 사용