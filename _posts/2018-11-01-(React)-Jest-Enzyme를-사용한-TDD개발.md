---
title: (React) Jest & Enzyme를 사용한 TDD 개발
comments: true
description: 테스트 주도 개발(TDD)를 통해 Navbar 컴포넌트 개발해보기
categories:
 - React
tags: React, TDD, Jest, Enzyme 
---

## Overview

테스트 프레임워크인 Jest와 React를 테스트 하기 위한 Enzyme 라이브러리를 사용해 Navbar 컴포넌트를 TDD 개발 방법을 이용해 개발해보려고 합니다.

TDD 개발은 기본적으로 아래 프로세스로 진행됩니다.

- 시나리오 작성 → 테스트 코드 작성 → 기능 구현 → 리팩토링

## Dependent

- [create-react-library](https://www.npmjs.com/package/create-react-library)
- [Enzyme](https://airbnb.io/enzyme/)

## Create Project

    $ npm install -g create-react-library
    $ create-react-library
    ? Package Name react-navbar
    ? Package Description TDD 개발 방식을 통한 Navbar 컴포넌트 개발
    ? Author's Github Handle Jangwon
    ? GitHub Repo Path Jangwon/react-navbar
    ? License MIT
    ? Package Manager yarn
    ? Template default
    ...

`create-react-library` 를 사용해서 프로젝트를 생성합니다. `create-react-app` 기반으로 `Jest`, `Babel` 등 다양한 라이브러리를 세팅해주는 CLI 라이브러리 입니다.

- Package Name → 패키지 이름을 설정하며, Github repo 이름으로 설정하면 될것 같습니다.
- Package Description → 패키지 상세 설명
- Author's Github → Github 아이디
- Github Repo Path → Author's Github/Package Name
- Package Manager → npm과 yarn 중 택 1
- Template → default, typescript, custom 중 택 1

### 디렉토리 구조

```
    react-navbar
    │
    ├── README.md
    ├── node_modules
    ├── example
    │   ├── README.md
    │   ├── node_modules
    │   └── src
    │   │   ├── App.js
    │   │   └── index.js
    │   └── package.json
    ├── rollup.config.js
    ├── src
    │   ├── Navbar
    │   │   ├── index.js
    │   │   ├── Navbar.js
    │   │   └── test.js
    │   ├── index.js
    │   ├── styles.css
    │   └── test.js
    └── yarn.lock
```

- react-navbar/src → 테스트 코드와 컴포넌트를 개발
- react-navbar/src/Navbar → Navbar 컴포넌트를 위한 폴더 생성
- react-navbar/example → 개발된 컴포넌트를 불러와서 사용
    - `import <ComponentName> from <ProjectName>` 다음과 같이 사용한다.
    - 예 : `import Component from 'react-navbar'`

### 테스트 및 실행 방법

- 루트 디렉토리(react-navbar/)에서 `yarn test` 또는 `npm test`를 하면 테스트 코드를 실행할 수 있다.
    - 특정 컴포넌트에 대한 test 코드만 실행 `Navbar를 test 코드를 실행하는 예제`
        - `yarn test src/Navbar` or `npm test src/Navbar`
- `react-navbar/example` 에서 `yarn start` 또는 `npm start`를 통해 실제 컴포넌트를 사용하는 예제를 실행할 수 있다.

## Navbar Component 시나리오

```
    Story. 사용자는 Navbar를 생성할 수 있다.
      senario
        given: 설정없이
        when: Navbar를 생성하면
        then: default 속성을 가진 Navbar를 생성한다.
      senario
        given: expand, position, bgColor, textColor를 설정하고
        when: Navbar를 생성하면
        then: 설정된 expand, position, bgColor, textColor를 가진 Navbar를 생성한다.
    Story. 사용자는 ToggleNav버튼으로 메뉴를 toggle 시킬 수 있다.
      senario
        given: state가 false인 상태에서
        when: ToggleNav를 클릭하면
        then: state가 true로 바뀌며, 메뉴가 보여진다.
      senario
        given: state가 true인 상태에서
        when: ToggleNav를 클릭하면
        then: state가 false로 바뀌며, 메뉴가 사라진다.
    ...
```

위에 정리한 시나리오 외에도 MouseOver 시, MouseClick 시 등.. 다양한 상황에 맞춰 시나리오를 짤 수 있습니다.

- 지금은 간단한 테스트를 위해 두가지 시나리오에 대한 테스트만 진행하도록 하겠습니다.

## TestCode  작성하기

### install enzyme

```bash
    $ yarn add enzyme enzyme-adapter-react-16
    or
    $ npm install enzyme enzyme-adapter-react-16
```

React Component에 접근하기 위해 `enzyme` 라이브러리를 설치합니다.

- `enzyme`를 사용하기 위해 `enzyme-adapter`도 함께 설치합니다. 저는 React 버전이 `v16.4.1` 이여서 16버전으로 설치했습니다.

### src/Navbar/test.js

```java
    import React from 'react';
    import Navbar from './Navbar'
    import { shallow, configure } from 'enzyme';
    import Adapter from 'enzyme-adapter-react-16';
    
    configure({ adapter: new Adapter() });
```

우선 테스트 코드를 작성에 필요한, enzyme 라이브러리를 세팅을 합니다. 그리고 테스트를 진행할 컴포넌트(Navbar)를 import 합니다. 

```javascript

    describe('Navbar 컴포넌트', () => {
      describe('사용자는 Navbar를 생성할 수 있다.', () => {
        it(`설정없이 Navbar를 생성하면,
            default 속성을 가진 Navbar를 생성한다.`, () => {
            // Test Code
          })
        it(`expand, position, bgColor, textColor를 설정하고 Navbar를 생성하면, 
            설정된 expand, position, bgColor, textColor를 가진 Navbar를 생성한다.`, () => {
            // Test Code
          })
      })
      describe('사용자는 ToggleNav버튼으로 메뉴를 toggle 시킬 수 있다.', () => {
        it(`state가 false인 상태에서 ToggleNav를 클릭하면,
            state가 true로 바뀌며, 메뉴가 보여진다.`, () => {
            // Test Code
          })
          it(`state가 true인 상태에서 ToggleNav를 클릭하면,
              state가 false로 바뀌며, 메뉴가 사라진다.`, () => {
            // Test Code
          })
      })
    })
```

앞서 작성한 시나리오 맞춰 `describe`을 작성한 다음 `Test Code`를 작성합니다.

## Result

### src/test.js

```javascript
    describe('Navbar 컴포넌트', () => {
      describe('사용자는 Navbar를 생성할 수 있다.', () => {
        it(`설정없이 Navbar를 생성하면,
            default 속성을 가진 Navbar를 생성한다.`, () => {
            // Test Code
            const navbar = shallow(<Navbar />)
            const expected = {
              expand:"md",
              position:null,
              bgColor:'rgb(240,240,240)',
              textColor:'rbg(100,100,100)',
              isOpen:false,
            }
            expect(navbar.state()).toEqual(expected)
          })
    ...
```

### src/Navbar.js

```javascript
    import React, { Component } from 'react'
    
    export default class Navbar extends Component {
      constructor(props) {
        super(props);
    
        this.state = {
          expand:"md",
          position:null,
          bgColor:'rgb(240,240,240)',
          textColor:'rbg(100,100,100)',
          isOpen:false,
        };
      }
    
      render() {
        return (
          <div id="Navbar">
          </div>
        )
      }
    }
```

## Reference

[Jest를 이용한 Unit Test 적용기 - jinseok.choi - Medium](https://medium.com/@jinseok.choi/jest%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-unit-test-%EC%A0%81%EC%9A%A9%EA%B8%B0-420049c16cc8)

[React로 TDD 쵸큼 맛보기](https://www.slideshare.net/jeokrang/react-tdd-76066004)