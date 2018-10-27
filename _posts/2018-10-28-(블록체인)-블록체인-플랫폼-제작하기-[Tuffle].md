---
title: (블록체인) Django로 블록체인 플랫폼 개발하기 [Tuffle 세팅]
comments: true
description: Ethereum을 사용해서 웹 플랫폼을 구축하는 과정이다. 웹 사이드는 Django를 사용했다.
categories:
 - BlockChain
tags: BlockChain, Ethereum 
---

## Overview

Tuffle은 솔리디티 소스를 직접 컴파일하고, 배포까지 해주는 프레임워크이다.


## 목차

## Truffle 설치

```bash
$ npm install -g truffle
$ truffle version
Truffle v4.1.14 (core: 4.1.14)
Solidity v0.4.24 (solc-js)
```

## Truffle 초기 세팅

```bash
$ mkdir Truffle && cd Truffle
$ truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
```

초기 세팅이 끝나면 아래와 같은 디렉토리 구조가 됩니다.

```
|-- Truffle
|   |-- contracts
|   |   |-- Migrations.sol
|   |-- migrations
|   |   |-- 1_initial_migration.js
|   |-- test
|   |-- truggle-config.js
|   |-- truffle.js
```

### Truffle/contracts/TokenName.sol

```java
pragma solidity ^0.4.24;

contract TokenName {
    string TokenName = "Jangwon Coin";

    function getTokenName() constant public returns(string) {
        return TokenName;
    }

    function setTokenName(string newTokenName) public {
        TokenName = newTokenName;
    }
}
```

### Truffle/migrations/2_deploy_tokenname.sol

```java
var TokenName = artifacts.require("./TokenName.sol");

module.exports = function(deployer){
  deployer.deploy(TokenName);
}
```

## Truffle 개발 환경 세팅

### 개발 환경 생성

```bash
$ truffle develop
```

![truffle_error](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-10/truffle_error.png)

위와 같은 에러가 발생 할 경우, `Truffle/truffle.js` 파일을 삭제한 다음 재실행 합니다.

```bash
$ truffle develop
...
...
truffle(develop)>
```

정상 작동하면 위와 같이 뜹니다.

### 배포

```bash
truffle(develop)> migrate
...
```

배포가 성공적으로 완료되면, `Truffle/build` 폴더가 생성됩니다.


## 참고

- [Truffle 명령어 모음](https://github.com/wkddnjset/wkddnjset.github.io/wiki/Truffle-%EB%AA%85%EB%A0%B9%EC%96%B4-%EB%AA%A8%EC%9D%8C)