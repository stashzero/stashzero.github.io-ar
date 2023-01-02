---
title: '[jekyll] github.io 블로그 만들기'
date: 2022-10-08 22:31:25 +0900
categories: [BLOG]
tags: [github, jekyll, ruby]     # TAG names should always be lowercase
---

> **jekyll**기반 **github** 블로그 구성 요약
{: .prompt-info }

## **구성 하는 방법**
---

* `jekyll theme` 를 찾는다
* 해당 theme 의 소스코드를 복사한다.
    * `fork` or `zip download` 등 해당 theme 의 가이드가 있다면 참고한다.
* 나의 `github`에 `repository` 를 생성하는데 이름은 `[userid].github.io` 로 한다.

## **준비사항**
---

* `markdown` 작성법을 익히면 편하다.
* `ruby` 버전에 따라 theme 의 `bundle` 과정에서 의존성 에러가 발생하는 것 같다.
   * 나의 개발 환경 기준 [chirpy](https://github.com/cotes2020/jekyll-theme-chirpy){: target='_blank'} theme 가 ruby3.x 버전에서 의존성 오류가 발생했다.

