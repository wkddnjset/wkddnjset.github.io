---
title: Next & Mobx 
comments: true
description: Next.js, Mobx를 사용해 웹을 개발하기 위한 기본 세팅 작업
categories:
  - React
tags: react next mobx boilerplate
---


## 목차

1. **Next.js 란?**
2. Next.js 시작하기
3. Next.js 라이브러리 세팅
4. Mobx 세팅
5. Todo List 만들어보기


## Next.js 란?

기본적으로는 리액트를 기반으로 한 SPA를 빠르게 빌드할 수 있도록 도와주는 프레임워크 입니다. Next에서는 많은 기능을 제공해주지만 리액트에 대한 기본적인 지식이 있는 상황이라면 `client-side routing`, `page layout` 두가지만 배우면 빠르게 Next를 사용할 수 있습니다.

### SSR vs CSR

![ssrvscsr](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2020-07/ssrvscsr.png)

먼저 일반적인 React 렌더링 방식은 `render()` 함수가 먼저 실행되고, 그다음에 `ComponentDidMount()` 함수를 통해 데이터를 가져와서 다시 한 번 렌더링이 됩니다. 반면에 `Next`는 `getInitialProps()` 라는 함수를 사용하기 때문에 데이터를 먼저 가져와서 한 번에 렌더링을 해줍니다. 보시다시피 SSR은 한 번에 렌더링이 되기 때문에 초기로딩 속도는 빠르지만, page 이동 시에는 CSR보다 느립니다. 왜냐하면, page를 요청할 때마다 중복되는 파일을 내려받아야 하기 때문입니다. 하지만 CSR은 초기로딩 속도는 느리지만, 첫 페이지 로딩 때 모든 파일을 내려받기 때문에 페이지를 이동할 때마다 해당 페이지에서 필요한 데이터만 불러서 사용하면 됩니다.

### Next.js의 핵심기능

#### 코드 스플리팅

일반적인 리액트 싱글페이지에서는 초기 렌더링 때 모든 컴포넌트를 내려받습니다. 하지만 규모가 커지고, 용량이 커지면 로딩속도가 지연될 수 있는 문제점이 있습니다. Next는 이러한 문제점을 개선해서 필요에 따라 파일을 불러올 수 있게 여러 개의 파일을 분리하는 `코드 스플리팅`을 사용하였습니다. 폴더 구조를 보시면 `pages 폴더` 안에 각 page 즉, 라우트들이 들어가며, Components 폴더에는 React 컴포넌트들이 들어가게 되는데요. 예를 들어, 브라우저가 실행되고, 사용자가 접속을 하게 되면, 첫 페이지인 `index page`만 불러오게 되고, 그 이후에 다른 페이지로 넘어갔을 때는 해당 페이지만 불러오게 됩니다.

#### 간단한 클라이언트 사이드 라우팅 제공

- **/page/index.js**

```javascript
import Link from "next/link";
import Router from "next/router";

class Home extends Component {
    toDetail = () => {
        Router.push('/detail')
    }    
    render() {
        return (
            <div>

                // Link  
                <Link as={`/book/${this.props.book.id}`} href={`/book/${this.props.book.id}`}>
                    <div>Book</div>
                </Link>

                // Router
                <div onClick={this.toDatil}>Book</div> 

            </div>
        )
    }
}
```

사용방법은 `Router`와 `Link`를 모두 import 해서 사용할 수 있는데요. 먼저 `Link`에서는 href와 as props가 있는데 이 href는 해당 페이지로 이동해 주는 역할을 하고, as는 href의 URL을 조금 더 직관적으로 만들어주는 역할을 해줍니다. `Router`는 링크와 동일하게 해당 페이지로 이동해주는 역할을 하지만 개발자에게 조금 더 제어권을 넘겨줘서 쉽게 `Redirect`도 가능합니다.

#### 커스텀 API 서버 (as - 라우트 마스킹)

- **/server.js**

```javascript
const next = require("next");
const app = next({ dev: process.env.NODE_ENV === "production" });
const express = require("express");
const handle = app.getRequestHandeler();

app
.prepare()
.then(() => {
    const server = express();

    server.get("/book/:id", (req, res) => {
        const actualPage = "/book";
        const queryParams = { id:req.params.id };
        app.render(req, res, actualPage, queryParaams);
    })

    ...
})
```

만약에 `Link` 컴포넌트에서 as를 사용하게 되면 실제 페이지가 없는 곳에 요청하게 됩니다. 그래서 직접 커스텀 서버를 생성해서 as의 URL이 href를 바라볼 수 있게 처리를 따로 해줘야 새로 고침이나 뒤로 갔을 때도 렌더링이 가능해집니다.

#### getInitialProps()

Next의 핵심기능인 `getInitialProps` 함수를 통해 데이터를 가져올 수 있습니다. 밑에 코드를 보시면 React의 ComponentDidMount 는 렌더링이 두 번 되지만, Next에서는 데이터를 미리 갖고 오기 때문에 `한 번에 렌더링이 가능합니다`.

- **React Example**

```javascript
class Home extend Component {
    state = {
        books: []
    }
    // 렌더링이 된 다음 다시 state를 변경했기 때문에 렌더가 2번 됨
    async componentDidMount() {
        const res = await axios.get(`....url`);
        const books = res.data
        this.setState({ books })
    }
    ...
}
```

- **Next Example**

```javascript
class Home extend Component {
    static async getInitialProps () {
        const res = await axios.get(`....url`);
        const books = res.data

        return {
            books: books
        }
    }
    state = {
        books: this.props.books
    }
    ...
}
```


## 참고 사이트

- [Next.js 필요한 것만 빨리 배우기
](https://velog.io/@jakeseo_me/Next.js-%EB%B9%A8%EB%A6%AC-%EB%B0%B0%EC%9A%B0%EA%B8%B0-y0jz9oebn0)
- [Next.js 제대로 알고 쓰자
](https://medium.com/@msj9121/next-js-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90-8727f76614c9)