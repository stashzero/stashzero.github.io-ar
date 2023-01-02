---
title: '[electron] msvs_version not set from command line or npm config 에러'
date: 2022-12-06 22:43:55 +0900
categories: [DEV]
tags: [electron, visualstudio, node, windows]     # TAG names should always be lowercase
---

> **Windows OS**환경에서 **node** 라이브러리를 설치하거나 빌드 하는 경우 등에서 발생할 수 있는 문제이다.
{: .prompt-info }

## **발생**
---

`Windows OS 10` 환경에서 `electron` 앱을 빌드하려고 할 때, 아래와 같은 에러가 발생했다.
```console
...
npm ERR! gyp ERR! find VS
npm ERR! gyp ERR! find VS msvs_version not set from command line or npm config
npm ERR! gyp ERR! find VS VCINSTALLDIR not set, not running in VS Command Prompt
npm ERR! gyp ERR! find VS checking VS2022 (17.3.32901.215) found at:
...
```



## **원인**
---

앱 빌드시 [`windows-focus-assist`](https://github.com/bitdisaster/windows-focus-assist) 라이브러리를 포함하여 빌드가 진행되어야 하는 상황이었다.

`Visual Studio` 의 `c++ 데스크탑 워크로드` 및 관련 개발 도구 구성 요소 등이 필요했다.

발생한 원인은 npm 에서 인식할 수 있는 Visual Studio 의 버전이 없으니, 명시해달라는 것이었다.


## **환경변수 선언**
---

내가 설치한 버전은 Visual Studio 2022 버전이었으며, 환경변수 선언하는 방법은 아래와 같다.
```console
$npm config set msvs_version 2022 
```

## **결과**
---

다행이다. 해결이 된다.