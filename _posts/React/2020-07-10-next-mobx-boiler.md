---
title: Next와 Mobx 정리 - [Next.js 라이브러리 세팅]
comments: true
description: Next.js에 대한 이해와 Mobx와 같은 라이브러리들을 사용해 웹을 개발하기 위한 기본 세팅 작업을 boilerplate로 만들어보는 과정에 대한 내용을 정리했습니다.
categories:
  - React
tags: react next mobx styled-component boilerplate
---


## 목차

1. [Next.js 란?](http://jangwon.io/react/2020/07/01/next-mobx-boiler/)
2. [Next.js 시작하기](http://jangwon.io/react/2020/07/05/next-mobx-boiler/)
3. **Next.js 라이브러리 세팅**
4. Mobx 세팅
5. Todo List 만들어보기


## Next.js 라이브러리 세팅

[[Github Project 링크](https://github.com/wkddnjset/next-mobx-boilerplate)]

### 프로젝트 생성

Next.js에서도 React에서 사용하는 `creat-react-app`과 같은 `creact-next-app`이라는 CLI를 제공하고 있으므로 꼭! 사용하는 것을 권장한다.

> 물론 메뉴얼 세팅 방법이 있지만, 다루지 않았으며, 하단에 `참고 사이트`를 참고 해주세요.

```bash
$ npx create-next-app
# or
$ yarn create next-app
```

이미 공식적으로 제공되는 `boilerplate` 들이존재하고, 프로젝트를 설치 할 때 자동 세팅을 지원하는 듯 하지만, `Default Mode`를 사용해서 설치하였습니다.

### 디렉토리 구조

```
next-mobx-boilerplate
├─ node_modules
    └─ ...
├─ pages
    └─ api
        └─ hello.js
    └─ index.js
├─ public
├─ node_modules
└─ pages
```

#### Page

Next.js에서 **page**는`pages`폴더에 리액트 컴포넌트 형태로 저장되어 있으며, 각 **page**들은 파일명을 기준으로 router와 연결되어 있습니다. 

> 예시로 만약 `pages/about.js` 파일을 생성하면, `/about`의 url 경로로 접속이 가능하게 됩니다. 

### 동적 라우팅

- `pages/post/[pid].js`

```javascript
import { useRouter } from 'next/router'

const Post = () => {
    const router = useRouter()
    const { pid } = router.query

    return <p>Post : {pid}</p>
}

export default Post
```

`/post/abc`

```javascript
{ "pid": "abc" }
```

`/post/abc?foo=bar`

```javascript
{ "foo": "bar", "pid": "abc" }
```

route 파라미터와 queryString이 같은 경우
`/post/abc?pid=123`

```javascript
{ "pid": "abc" }
```

`pages/post/[pid]/[comment].js`

```javascript
{ "pid": "abc", "comment": "a-comment" }
```

## 참고 사이트

- [Next.js 사이트](https://nextjs.org/docs/getting-started)
- [Next.js 필요한 것만 빨리 배우기
](https://velog.io/@jakeseo_me/Next.js-%EB%B9%A8%EB%A6%AC-%EB%B0%B0%EC%9A%B0%EA%B8%B0-y0jz9oebn0)
- [Next.js 제대로 알고 쓰자
](https://medium.com/@msj9121/next-js-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90-8727f76614c9)