---
title: ionic 3 앱 개발 (Django API 만들기) - [여행플랜/백엔드]
comments: true
description: Django에서 설계한 모델을 ionic에서 연동할 수 있도록 RestFul API를 만들어 보도록 하겠습니다.
categories:
 - MiniProject/TourPlan
tags: django app develop backend api
---

## 이전 내용

- [[ionic 3 앱 개발 (사이드메뉴&탭 결합) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/04/ionic-3-%ED%95%98%EC%9D%B4%EB%B8%8C%EB%A6%AC%EB%93%9C-%EC%95%B1-%EA%B0%9C%EB%B0%9C%ED%95%98%EA%B8%B0-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (탭 아이콘 변경) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/05/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%ED%83%AD-%EC%95%84%EC%9D%B4%EC%BD%98-%EB%B3%80%EA%B2%BD)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (검색바 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/06/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EA%B2%80%EC%83%89%EB%B0%94-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (다양한 애니메이션) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/07/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EB%8B%A4%EC%96%91%ED%95%9C-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (드롭다운) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/09/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EB%93%9C%EB%A1%AD%EB%8B%A4%EC%9A%B4)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[ionic 3 앱 개발 (슬라이드) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/11/ionic-3-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%93%9C)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- - [[ionic 3 앱 개발 (Django 모델설계) 포스팅](https://wkddnjset.github.io/miniproject/tourplan/2018/02/12/Django-%EC%95%B1-%EA%B0%9C%EB%B0%9C-(Django-%EB%AA%A8%EB%8D%B8%EC%84%A4%EA%B3%84)-%EC%97%AC%ED%96%89%ED%94%8C%EB%9E%9C/)]
- [[Github 전체 소스코드](https://github.com/wkddnjset/MiniProject-TourPlan)]

저번 시간에 만든 모델과 Django Rest Framework를 이용해 API를 만들어보도록 하겠습니다.