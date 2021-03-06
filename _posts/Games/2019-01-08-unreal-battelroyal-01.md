---
title: (언리얼엔진) 배틀그라운드 따라만들기 - 1.랜드스케이프 작업
comments: true
description: 배틀그라운드 따라 만들기 강좌를 보고 내용을 정리했습니다. 게임의 배경이 되는 맵을 만드는 과정을 정리한 내용입니다.
categories:
 - Unreal
tags: Unreal, Game 
---

## Overview

+) 4.20버전으로 진행하였습니다.

[언론데브](https://alonedev.com/)에 유료 강좌 **배틀그라운드 만들기 11~14강**를 보고 정리한 내용입니다.




## 시작하기

### 프로젝트 생성

3인칭 시점으로 프로젝트를 생성해줍니다.

![create_project_1](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_1.png?raw=true)

### 프로젝트 세팅

- Packaging 설정

![create_project_2](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_2.png?raw=true)

`Blueprint Nativization Method`를 `Inclusive`로 변경하면, 블루프린터로 작성한 내용을 C++ 코드로 변환해서 패키징 됩니다.

> C++로 만들어진 코드가 성능적인 측면에서 더 퀄리티가 높다고 하네요.

- Light Render 설정

![create_project_3](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_3.png?raw=true)

`Rendering`에 `Allow Static Lighting`을 `False`로 체크해줍니다.

> 8k 맵을 생성할 예정이라, 해당 옵션을 켜 놓을 경우 렉이 많이 걸릴 수 있습니다. 제껀 컴이 좋지 않아서..

## 랜드스케이프 작업하기

### Map 생성하기

`Maps` 폴더를 생성하고 `Level`을 생성하여 `Jungle`이라는 이름으로 변경해줍니다.

![create_project_4](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_4.png?raw=true)

### Lighting 세팅

- **태양 만들기**

1. 우측 상단에 Lighting 폴더를 만들어줍니다.
2. `Directional Light`를 가져와 `Sun`으로 이름을 변경하고, `Rotation Y 값`을 `-70`으로 수정해줍니다.
3. `Mobility` 옵션을 `Movable`로 변경합니다.

![create_project_5](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_5.png?raw=true)

> -70도는 정오의 태양 각도를 표현하기 위함입니다.

- **하늘 생성하기**

1. `sky`를 검색하여 `BP_Sky_Sphere`를 가져와 `Sky`로 이름을 변경한 다음 `Lighting` 폴더에 넣습니다.
2. `Directional Light Actor`에 방금 만들어 놓은 `Sun`으로 변경해줍니다.

![create_project_6](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_6.png?raw=true)

> `Sun`의 각도를 변경할 때마다 `Directional Light Actor`를 재설정 해줘야 합니다.

- **자연스러운 그림자 만들기**

1. `Sky Light`를 불러오면 자동으로 `Sky`에 설정에 따라 빛이 생기며, 자연스러운 그림자를 만들어 냅니다.
2. Recapture 기능은 `Sun`의 변화에 따라 다시 세팅하기 위해서 `Sun`이 수정되고 나면 한번씩 눌러줘야 합니다.

![create_project_7](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_7.png?raw=true)

- **카메라의 HDR(High Dynamic Range)효과 없애기**

![create_project_8](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_8.png?raw=true)

### Heightmap 생성하기

- **Heightmap 이란?**

Heightmap은 검정색과 힌색으로 이뤄져있으며, 지형의 높낮이를 나타낸 이미지입니다. 검정색이 제일 낮은 곳을 의미하며, 흰색이 제일 높은 곳을 의미합니다.

- **Instant Terra 설치**

1. [링크](https://www.wysilab.com/Editions_Pricing.html)에서 Free Trial 버전으로 설치해줍니다.
2. Free Trial 버전은 한달간 무료로 사용가능한 것으로 보입니다.(아마도..)


![create_project_9](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_9.png?raw=true)

- **마스킹 이미지 만들기**

1. 8161 X 8161 사이즈의 파일을 생성해 간단한 마스킹 이미지를 만들어줍니다.
2. PNG 파일로 저장합니다.

![create_project_10](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_10.png?raw=true)

- **지형 만들기**

1. `Perlin noise`노드에서 가로, 세로의 길이를 8161 X 8161로 변경합니다.
2. `Apply color ramp`와 `Color ramp`노드를 사용해서 바다와 지형을 나눌수 있습니다.
3. `Import mask`노드를 사용해 가져온 마스킹 이미지를 `Sum Using Mask`노드를 통해 기존에 지형과 합쳐줍니다.
4. `Cone`은 일부 지형을 원불형태로 돌출시키기 위함이며, `Scale&offset`노드를 사용해 전체적인 지형의 높낮이를 선택할 수 있습니다.
5. `Apply curve`와 `Smooth`노드를 사용해 지형을 평평하게 만들어줍니다.
6. 마지막으로 `Export terrain`노드를 사용해 Heightmap을 추출합니다.

![create_project_11](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_11.png?raw=true)

> 저장은 16비트 PNG로 해야합니다.

## Landscape 생성

- **지형 생성하기**

1. 좌측 상단에 `Modes`를 `Landscape`로 변경해줍니다.
2. `Import form File`을 선택하고, 저장해논 Heightmap을 불러옵니다.
3. 해상도를 맞추기 위해 하단에 `Fit to Data`를 누르고 `Import`를 통해 지형을 생성합니다.

![create_project_12](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_12.png?raw=true)

> 주의사항으로 실제 Instant Terra에서 가져온 이미지의 최대 최소와, 언리얼 엔진에서 나타내는 최대 최소 높이가 다르기 때문에, 가져올때 `Scale Z` 값과 해수면 높이에 맞는 `Location Z`값을 적용해주어야한다.

+) 계산법

- Scale Z 계산 = [(실제 지형의 최대 높이 - 최소 높이) / 512] X 100 
- Location Z = [(실제 지형의 최대 높이 - 최소 높이) / 2 - 실제 지형의 최소 높이] X 100

- **해수면 만들기**

1. 평면 액터 추가하고 `Ocean`으로 이름을 변경합니다.
2. Location 값을 (0, 0, 0) 으로 변경하고, Scale은 x, y를 10000으로 변경합니다.

![create_project_13](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_13.png?raw=true)


## 색 칠하기

### 지형 입히기

- **Textures 가져오기**

1. Textures 폴더와 Materials 폴더를 생성해주세요.
2. Textures 폴더에서 `Import` 버튼을 눌러 Texture들을 가져옵니다.

![create_project_14](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_14.png?raw=true)

- **Material 생성**

1. Materials 폴더에서 `M_Jungle`이라는 Material을 생성해주세요.
2. Texture를 불러와 세팅을 해줍니다.

![create_project_15](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_15.png?raw=true)

> 이쪽은 잘 모르겠네요 ㅠㅠ 그냥 따라했습니다.

- **Material 적용하기**

1. 생성한 Material을 Landscape 속성 중 `Landscape Material`로 드래그 하여 적용시켜줍니다.
2. Paint 메뉴에서 Layers에 Material들이 추가된 것을 확인 할 수 있습니다.
3. Layer 우측에 + 버튼을 눌러서 가중치 정보를 저장하는 `Layer Info`를 생성합니다. (Nomal로 생성)
4. `Layer Info`가 생성되면서 자동으로 폴더가 만들어집니다.

![create_project_16](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_16.png?raw=true)

### 바다 색 입히기

- **Material 생성**

1. Materials 폴더에서 `M_Ocean`이라는 Material을 생성합니다.
2. `M_Ocean`의 `Blend Mode`를 `Translucent`로, `Lighting Mode`를 `Surface TranslucencyVolume`으로 변경하고, `Screen Space Reflections` 옵션을 켜줍니다.
3. Depth Fade를 사용해 Opacity를 사용해 투명도를 조절해줍니다.
4. 물결 무늬를 UV 애니메이션을 통해 물결치는 애니메이션을 구현합니다.
5. 1.333의 굴절율을 줘서 바다 속에 굴절을 줍니다.
6. `M_Ocean` X, Y Scale을 100000, 100000 으로 변경합니다.

![create_project_17](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_17.png?raw=true)

![create_project_18](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_18.png?raw=true)

- **Fog 생성**

1. Exponation Hight를 추가하고, Location 값을 수정합니다.
2. `Fog Density` 를 0.01, `Fog Height Falloff`를 0.08, `Start Distance`를 20000으로 수정합니다.
3. `M_Ocean` 속성 중 `Apply Fog Per Pixel` 옵션을 선택합니다.

![create_project_19](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_19.png?raw=true)

> 중간에 지형이 좀 이상한거 같아서 수정했습니다.

## 하늘 디테일 수정

1. `Cloud opacity`를 1.2로 수정합니다.
2. `Cloud speed`를 2로 수정해주세요.

![create_project_20](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_20.png?raw=true)


## 결과물

- 게임 실행 모습
![create_project_21](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_21.png?raw=true)

- 전체 맵
![create_project_22](https://github.com/wkddnjset/wkddnjset.github.io/blob/master/_posts/images/2019-01/01-08_battle_22.png?raw=true)