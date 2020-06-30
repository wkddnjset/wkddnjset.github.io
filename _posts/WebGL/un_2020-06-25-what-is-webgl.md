---
title: WebGL에 기본적인 컨셉
comments: true
description: OpenGL 2.0을 기반으로 만들어진 WebGL에 대한 기본적인 컨셉을 설명하는 해외 블로그의 글을 번역하면서 약간의 수정을 추가했습니다.
categories:
 - opengl, webgl
tags: 
---


### 목차

1. WebGL 이란?
2. WebGL Context 와 Framebuffer


# WebGL 이란?

WebGL은 low-level의 그래픽 프로그래밍을 할 수 있도록 도와주는 웹 API 이다. 여기서 말하는 `low-level`이란 WebGL을 통해 직접적으로 GPU 와 같은 그래픽 하드웨어에 접근할 수 있는 수준을 의미한다. 

1992년 OpenGL 1.0 버전이 릴리즈된 당시에는, 그 시대의 3D 그래픽 하드웨어의 특징에 초점을 맞춘 3D API 에 불과했다. 하지만 3D 그래픽 하드웨어의 발전으로 접근성의 향상으로 좋은 성능을 낼 수 있는 OpenGL 2.0이 탄생한 것이다. WebGL은 OpenGL 2.0을 기반으로 만들어 졌으며, 2D와 3D에 대한 개발을 넘어서 그래픽을 다루는 다양한 기능들을 제공한다. 


# WebGL Context 와 Framebuffer

WebGL은 HTML Canvas Element에 WebGL Context를 렌더링 한다는 것을 기본 컨셉으로 갖고 있다.

```javascript
var gl;
try {
  gl = canvas.getContext("experimental-webgl");
} catch(e) {}
```

위에서 선언된 gl Element를 통해 WebGL API 함수를 사용해 렌더링 할 수 있으며, WebGL은 단일 버퍼가 아닌 더블 버퍼링을 사용하고 있다. 버퍼에 대한 설명은 [[여기](https://huiyu.tistory.com/entry/%EC%BB%B4%ED%93%A8%ED%84%B0-%EA%B7%B8%EB%9E%98%ED%94%BD%EC%8A%A4-%EC%9D%B4%EB%A1%A0-%EC%A0%95%EB%A6%AC-%EB%8D%94%EB%B8%94%EB%B2%84%ED%8D%BC%EB%A7%81Double-Buffering)]에 자세하게 소개되어있다. 현재 디스플레이 되고 있는 객체를 `frontbuffer`라고 하며 다을 렌더링될 객체를  `WebGL Framebuffer` 또는 `backbuffer`라고 한다.

버퍼링 작업은 브라우저에서 자동으로 해주고 있으며, WebGL 엔지니어는 `frontbuffer`에 대한 접근 권한이 주어지지 않기에 깊게 알필요는 없다. 여기서 중요한 사실은 브라우저는 JavaScript 실행 중을 제외하고 언제든지 `backbuffer`를 `frontbuffer`에 복사할 수 있기 때문에 1개의 Javascript callback 함수로 전체 WebGL에 대한 렌더링을 진행해야 한다는 것이다. 그렇게 할 경우 브라우저에서 복잡한 다중 버퍼 작업을 통해 정확한 렌더링을 제공해준다. 뿐만아니라 WebGL callback 함수를 [`requetsAnimationFrame`](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)로 할 경우 브라우저에서 복잡한 애니메이션 스케줄링까지 제공해준다.


# 일반적인 low-level로 사용해보는 WebGL

### 삼각형이 기본이 되는 이유!

> 삼각형은 3개의 점만으로 평면상에 위치하여 정의할 수 있지만, 사각형의 경우 4개의 점만으로 평면상에 위치할 수 없기 때문에 WebGL은 삼각형 외에 다각형에 대해서 처리하지 않는다.

WebGL에서 삼격형을 랜더링하는 건 매우 일반적인 예시이다. callback 함수를 통해 `pixel shader` 또는 `fragment shader`를 불러오고, 불러온 `shader`를 통해 다시 한번 삼각형의 각 픽셀과 그려질 색상을 불러온다. 

여러분이 2D 게임을 코딩하고 있다고 가정해보자. WebGL은 삼각형만 그릴 수 있으므로 직사각형 이미지를 그리기 위해서 다음과 같이 두 개의 삼각형으로 분해해야 한다.

![webgl-rectangle](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/webgl-rectangle.png){: width="400px", margin="auto"}

그리고 `fragment shader`는 픽셀의 색을 결정하는 프로그램으로 간단하게 동작한다. 비트맵 이미지에서 하나의 픽셀을 읽고 현재 렌더링되고 있는 픽셀의 색으로 사용할 것이다.

