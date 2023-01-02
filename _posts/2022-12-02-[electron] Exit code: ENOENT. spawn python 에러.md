---
title: '[electron] Exit code: ENOENT. spawn python 에러'
date: 2022-12-02 23:36:55 +0900
categories: [DEV]
tags: [electron, python, node]     # TAG names should always be lowercase
---

> `macOS` `monterey`를 완전히 새로 설치했고, `python` 이 설치되어 있지 않아 발생한 문제이다.
{: .prompt-info }

## **발생**
---

`electron` 앱을 빌드하려고 할 때, 아래와 같은 에러가 발생했다.
```console
Exit code: ENOENT. spawn python
```



## **원인**
---
> python 이 설치되어 있지 않거나 python 바이너리 파일이 없다면 여러 라이브러리에서 비슷한 문제가 발생할 수 있다.
{: .prompt-tip }
`macOS` 버전을 업그레이드 해왔다면, 구버전 macOS 에 기본 설치되어 있는 `python 2.x` 버전이 여전히 설치되어 있을 수 있다.

따라서 앱을 빌드하는 다양한 경우에서 python 실행과 관련된 에러는 없었을지 모른다.

그러나 내 경우 mac을 초기화 하여 macOS monterey를 완전히 새로 설치했는데 여기에 python 2.x 버전이 설치되어 있지 않았다.
- [**구버전 macOS 다운로드 및 설치 방법**](https://support.apple.com/ko-kr/HT211683){:target='_blank'}

내 경우에는 아래 두개 라이브러리에서 python 경로를 찾는 이슈와 관련된 문제가 발생했다..

- node_modules/accessibility-developer-tools/lib/closure-library/closure/bin/scopify.py
```python
#!/usr/bin/python
```
{: file='scopify.py'}

- node_modules/dmg-builder/out/dmg.js
```python
await builder_util_1.exec(process.env.PYTHON_PATH || "/usr/bin/python", [path.join(dmgUtil_1.getDmgVendorPath(), "dmgbuild/core.py")], {
```
{: file='dmg.py'}


## **python 설치**
---

`macOS monterey` 에 기본적으로 `python3` 가 설치되어 있었지만 python 바이너리 파일은 없고 `python3` 바이너리 파일만 있었다.
```console
$which python   #command not found와 같은 메세지를 확인할 수 있을 것이다.
$which python3
```
python3 바이너리에 대해서 python 으로 심볼릭 링크를 생성할 수도 있겠지만, 미래에 사용할 라이브러리에서 `python 2.x` 버전이 필요할 경우 이슈가 생길지 몰라 python 2.x 버전을 설치하기로 했다.
> macOS monterey 에서 /usr/bin 폴더의 경우 read-only 이며, 이곳에 쓰기 작업을 할 수 없다. 이 경로에 파일을 생성하기 위한 방법을 찾아보다가 시간이 소요될 것 같아 방법을 바꾸기로 했다.
{: .prompt-tip }



- python@2 설치
```console
$brew install pyenv
$pyenv install 2.7.18
```

  - `pyenv` 로 python 2 버전을 설치한 이유는 혹시 나중에 발생할지 모를 python 3 과 충돌 문제에 대해서 신경쓰기 싫어서다.  pyenv 설치 python 2 버전을 설치하지 않아도 되며, 이 부분은 직접 찾아보시길 바란다.

## **해결**
---


pyenv 로 python 2 버전을 설치했다면, python 바이너리는 아마 `~/.pyenv/shims` 경로에 존재할 것이다.

즉, python 실행 혹은 경로를 찾는 소스코드내에 python 2 바이너리를 가리킬 수 있도록 아래와 같이 수정한다.

- node_modules/accessibility-developer-tools/lib/closure-library/closure/bin/scopify.py
```python
#!/usr/bin/python  -> 이 부분을 아래와 같이 수정
#!/users/stashzero/.pyenv/shims/python
```
{: file='scopify.py'}

- node_modules/dmg-builder/out/dmg.js
```python
#await builder_util_1.exec(process.env.PYTHON_PATH || "/usr/bin/python", [path.join(dmgUtil_1.getDmgVendorPath(), "dmgbuild/core.py")], {
await builder_util_1.exec(process.env.PYTHON_PATH || "/Users/stashzero/.pyenv/shims/python", [path.join(dmgUtil_1.getDmgVendorPath(), "dmgbuild/core.py")], {
```
{: file='dmg.py'}

## **결과**
---

다행히, 잘 빌드 된다.

또한, python2 에 대한 심볼릭 링크를 만들어둬도 좋을 것 같은 느낌이 든다.