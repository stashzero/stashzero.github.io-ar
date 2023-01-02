---
title: '[electron] config.gygi : no such file or directory 에러'
date: 2022-12-08 21:37:12 +0900
categories: [DEV]
tags: [electron, node, windows]     # TAG names should always be lowercase
---

> **Windows 10 OS**기반 **node** 라이브러리 빌드시 발생했다. 
{: .prompt-info }

## **발생**
---

`electron` 앱을 빌드하려고 할 때, 아래와 같은 에러가 발생했다.
```console
ENOENT: no such file or directory, open 'C:\Users\stashzero\.electron-gyp\21.2.0\include\node\config.gypi'
```



## **원인**
---

모른다.

아마, `node` 및 `node_modules`를 버전별로 설치 삭제를 반복하던 중에 관련 데이터 파일이 꼬인 것 같다.

## **해결**
---


폴더를 삭재했다. 

`/Users/stashzero/.electron-gyp` 삭제


> 그럴 때가 있다.
>
> 느낌적으로 이건 지워도 다시 설치되겠구나 하는 것들
{: .prompt-tip }



## **결과**
---

최종 빌드 성공