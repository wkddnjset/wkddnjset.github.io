---
title: Modifiable Nested Octree(MNO) 구조에 대한 정리
comments: true
description: [Interactions with Gigantic Point Clouds] 논문에서 소개된 Modifiable Nested Octree(MNO)에 대해 정리해 보았다.
categories:
 - WebGL
tags: octree vision webgl vision 
---


> 수억개의 Cloud Point를 렌더링하기 위해 연구된 수 많은 논문 중 [Interactions with Gigantic Point Clouds](https://www.cg.tuwien.ac.at/research/publications/2014/scheiblauer-thesis/)에서 소개된 Modifiable Nested Octree를 정리해 보았다. Potree에서도 동일한 공간 분할 알고리즘을 사용했다고 하여 찾아보게 됨.

# 목차
1. Modifiable Nested Octree[MNO] 란?
2. 점 추가하기

## Modifiable Nested Octree[MNO] 란?

`nested octree`는 외부 메모리(out-of-core)를 통한 point 렌더링을 하기 위한  효율적인 데이터 구조를 갖고 있지만 점을 수정하는 데에 있어 결함이 존재한다. 그 이유는 새로운 점이 추가되거나 삭제될때 마다 재구축되는 octree 가 갖고 있는 특성 때문이다. 또한, octree 내부의 점을 수정한 후에는 hierarchy는 정렬되어져야한다. 위에서 언급 한것 처럼, octree 내부에서 상위 레벨에 저장된 점들은 렌더링 되는 동안 과도한 객체를 생성한다.

이러한 결함을 수용하기 위해, octree 내부를 일반적인 그리드 형태로 재배치하여 hierarchy가 지속적으로 재구축되는 일이 없게 만들수 있다.
일반 그리드를 사용하는 것은 내부 octree의 상위 레벨을 제거하고 `leaf nodes`만 유지하는 것과 동일하다. hierarchy에 저장된 점들의 변화를 처리하는 기능을 향상하기 위한 새로운 hierarchy는 Modifiable Nested Octree(MNO)라고 부를 것 이다. `Figure 1`에서 `nested octree`와 `MNO`가 각각 어떤 방식으로 점을 저장하는지 보여주고 있다. 

![mno-1](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/mno-1.png){: width="500px", margin="auto"}

> Figure 1: `nested octree`와 `modifiable nested octree(MNO)`의 차이는 점들을 저장하는 방식에 있다.(편의상 1차원으로 정리했다) `nested octree`는 내부 octree에 점을 저장하고(왼쪽), `MNO`는 점을 마지막 노드에 저장한다(오른쪽). 점을 추가하거나 삭제하는 것은 octree가 재구축 되어야하기 때문에, 내부 octree에게 있어서는 비용이 많이 드는 작업이다. 또한 내부 octree에서는 VBO(Vertex Buffer Object)에 점을 매핑하기 위해 일정한 순서를 유지해야한다. 