---
title: Modifiable Nested Octree 구조에 대한 정리
comments: true
description: Interactions with Gigantic Point Clouds 논문에서 소개된 Modifiable Nested Octree(MNO)에 대해 정리해 보았다.
categories:
 - WebGL
tags: octree vision webgl vision 
---


> Cloud Point를 렌더링하기 위해 연구된 수 많은 논문 중 [Interactions with Gigantic Point Clouds](https://www.cg.tuwien.ac.at/research/publications/2014/scheiblauer-thesis/)에서 소개된 Modifiable Nested Octree를 정리해 보았다. Potree에서도 동일한 공간 분할 알고리즘을 사용했다고 하여 찾아보게 됨.

# 아직 작성중 ..

### 목차
1. Modifiable Nested Octree(MNO) 란?
2. 점 추가하기

## Modifiable Nested Octree(MNO) 란?

`nested octree`는 외부 메모리(out-of-core)를 사용하여 점을 렌더링 하기 위한 효율적인 데이터 구조를 갖고 있지만 점을 수정하는 데에 있어 결함이 존재한다. 그 이유는 새로운 점이 추가되거나 삭제될때 마다 재구축되는 octree 가 갖고 있는 특성 때문이다. 또한, octree 내부의 점을 수정한 후에는 hierarchy는 정렬되어져야한다. octree 내부에서 상위 레벨에 저장된 점들은 렌더링하는 동안 일부 과다한 원형을 생성한다.


이러한 결함을 수용하기 위해, octree 내부를 일반적인 그리드 형태로 재배치하여 hierarchy가 지속적으로 재구축되는 일이 없게 만들수 있다.
일반 그리드를 사용하는 것은 octree 내부의 상위 레벨을 제거하고 `leaf nodes`만 유지하는 것과 동일하다. hierarchy에 저장된 점들의 변화를 처리하는 기능을 향상하기 위한 새로운 hierarchy는 Modifiable Nested Octree(MNO)라고 부를 것 이다. `Figure 1`에서 `nested octree`와 `MNO`가 각각 어떤 방식으로 점을 저장하는지 보여주고 있다. 

![mno-1](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-06/mno-1.png){: width="500px", margin="auto"}

> Figure 1: `nested octree`와 `modifiable nested octree(MNO)`의 차이는 점들을 저장하는 방식에 있다.(편의상 1차원으로 정리했다) `nested octree`는 octree 내부에 점을 저장하고(왼쪽), `MNO`는 점을 마지막 노드에 저장한다(오른쪽). 점을 추가하거나 삭제하는 것은 octree가 재구축 되어야하기 때문에, octree 내부에게 있어서는 비용이 많이 드는 작업이다. 또한 octree 내부에서는 VBO(Vertex Buffer Object)에 점을 매핑하기 위해 일정한 순서를 유지해야한다. 

`nested octree`에서 octree 내부를 배열로 변환하기 위해서는 점들의 재정렬이 필요하지만 `MNO`의 경우 점들에 대한 정보를 grid에서 갖고 있으며, gird에 저장된 점들의 위치들은 점의 3D 위치에서 가져온 값이기 때문에 각 점들이 배열로 저장되는 것에 문제가 없다.

이러한 변화들로 인해, LOD(Level of Detail) hierarchy는 Gobbetei와 Marton이 정의한? LOD hierarchy의 LPC(Layered Point Clouds) 와 유사하게 동작한다. They uniformly subsample an input point cloud at each node (using a kd-tree) to choose the points for this node. LPC와 MNO의 차이는 균일하게 서브 샘플링된 배열 대신 각 노드에 일반적인 그리드를 사용하는 구조적인 부분이다. MNO에서 샘플링된 배열을 필요로 하지 않은 시점 부터, 점을 추가하고 삭제하는 것이 더 간편해졌다. 그러므로 MNO가 hierarchy의 점을 편집하는 데에 더 적합하다.


> LOD hierarchy란, Static Mesh들의 클러스터 프록시를 생성하여
카메라로부터 멀리 떨어져있을 경우 합쳐진 Static Mesh로 바꿔치기해서 드로우콜을 줄이는 기법

> [LPC(Layered Point Clouds)](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.58.4315&rep=rep1&type=pdf)는 일반 GPU를 사용해 multiresolution algorithms 구현한 ?!??


물론 MNO에도 단점은 존재한다. 

