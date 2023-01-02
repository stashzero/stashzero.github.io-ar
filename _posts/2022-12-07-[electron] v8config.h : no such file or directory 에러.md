---
title: '[electron] v8config.h : no such file or directory 에러'
date: 2022-12-07 23:16:25 +0900
categories: [DEV]
tags: [electron, node, windows]     # TAG names should always be lowercase
---

> **Windows OS 10**에서 **node** 개발환경을 구성하는 중에 발생한 에러이다. **node** 버전과 문제있었을 것으로 추정된다.
{: .prompt-info }

## **발생**
---

`electron` 앱을 빌드하려고 할 때, 아래와 같은 에러가 발생했다.
```console
npm ERR! robotjs.cc 
npm ERR! fatal error C1083: 파일을 열 수 없습니다. 'v8config.h': No such file or directory [C:\Users\money\Documents\GitHub\desktop-5.2\node_
modules\robotjs\build\robotjs.vcxproj] 
npm ERR! gyp info it worked if it ends with ok
```



## **원인**
---

원인은 명확하지 않다.

검색 결과 추정된 원인은 `dependency` 들의 버전 관련 이슈라고 잠정 결론을 내렸다.

## **해결**
---

`node` 버전을 `downgrade` 했다.

사실 처음 시도했던 방식은

`macOS` 기반으로 `node@18` 버전에서 잘 빌드 되었어서 

windows OS 10 에서도 node@18 기준으로 컴파일 시도한 것인데,

결국 `node@16`으로 해당 오류가 사라졌다.


## **결과**
---

다행히 잘 빌드 된다.

쉽게 쉽게 가면 참 좋겠다.