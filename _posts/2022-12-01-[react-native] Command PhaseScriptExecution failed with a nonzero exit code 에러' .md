---
title: '[react-native] Command PhaseScriptExecution failed with a nonzero exit code 에러'
date: 2022-12-01 22:11:23 +0900
categories: [DEV]
tags: [iOS, react-native, xcode]     # TAG names should always be lowercase
---

> `react-native` 기반 iOS 앱을 빌드하려고 할 때 발생한 에러이다.
{: .prompt-info }

## **발생**
---
`xcode` 에서 `react-native` 앱을 빌드하려고 할 때 아래와 같은 에러가 발생했다.

```
Command PhaseScriptExecution failed with a nonzero exit code
```

관련 에러를 찾아보니, 아래와 같은 이슈들에 대해 검색이 되었다.
- `permission` 에러로 발생하는 이슈
- `nvm` 관련 `node` 실행 환경 이슈

하지만, 나에게 발생한 에러는 위의 이슈와 관련이 없었다. 

그렇게, 헤딩이 시작된다.

## **원인**
---
원인은 아마도 대부분 비슷할텐데 결국, 쉘 스크립트를 실행하지 못해서 발생하는 이슈였다.

내 문제도 쉘 스크립트를 실행 도중에 발생한 에러였다.

쉘 스크립트 파일을 하나하나 확인해보자.
```sh
set -o pipefail
set -e

RN_DIR=$(cd "${PODS_TARGET_SRCROOT}/../.." && pwd)

GENERATED_SRCS_DIR="${DERIVED_FILE_DIR}/generated/source/codegen"
GENERATED_SCHEMA_FILE="$GENERATED_SRCS_DIR/schema.json"
TEMP_OUTPUT_DIR="$GENERATED_SRCS_DIR/out"

LIBRARY_NAME="FBReactNativeSpec"
OUTPUT_DIR="${PODS_TARGET_SRCROOT}/../../React/FBReactNativeSpec/FBReactNativeSpec"

CODEGEN_REPO_PATH="$RN_DIR/packages/react-native-codegen"
CODEGEN_NPM_PATH="$RN_DIR/../react-native-codegen"
CODEGEN_CLI_PATH=""

# Determine path to react-native-codegen
if [ -d "$CODEGEN_REPO_PATH" ]; then
  CODEGEN_CLI_PATH=$(cd "$CODEGEN_REPO_PATH" && pwd)
elif [ -d "$CODEGEN_NPM_PATH" ]; then
  CODEGEN_CLI_PATH=$(cd "$CODEGEN_NPM_PATH" && pwd)
else
  echo "error: Could not determine react-native-codegen location. Try running 'yarn install' or 'npm install' in your project root." >> "${SCRIPT_OUTPUT_FILE_0}" 2>&1
  exit 1
fi

find_node () {
  source "$RN_DIR/scripts/find-node.sh"

  NODE_BINARY="${NODE_BINARY:-$(command -v node || true)}"
  if [ -z "$NODE_BINARY" ]; then
    echo "error: Could not find node. Make sure it is in bash PATH or set the NODE_BINARY environment variable." >> "${SCRIPT_OUTPUT_FILE_0}" 2>&1
    exit 1
  fi
}

```

아래 두가지 부분에서 좀 느낌이 왔다.

* `OUTPUT_DIR="${PODS_TARGET_SRCROOT}/../../React/FBReactNativeSpec/FBReactNativeSpec"`
* `echo "error: Could not find node. Make sure it is in bash PATH or set the NODE_BINARY environment variable." >> "$`

뭔가 output 이 나오는 디렉터리가 셋팅이 되는게 있고, node 를 찾을 수 없는 에러처리가 되어 있다라는 점

천하무적 `grep`
```console
$grep -r FBReactNativeSpec *
```

로그파일이 발견됐고, 아래와 같은 메시지가 있었다. 해당 폴더에 로그파일도 존재했다.
`/////Build/Intermediates.noindex/Pods.build/Debug-iphonesimulator/FBReactNativeSpec.build/DerivedSources/codegen-FBReactNativeSpec.log`

```
error: Could not find node. Make sure it is in bash PATH or set the NODE_BINARY environment variable.
```


> 마음이 급해서 기억이 뚜렷하지 않다. xcode내에 log 파일 경로가 있었는지, FBReactNativeSpec 관련 라이브러리를 찾아보다가 log파일을 발견했는지 확실하지 않다. 두 가지 경우가 모쪼록 도움이 되기를 바란다.
{: .prompt-warning }

## **해결**
---

`xcode` 가 인식하는 `node` 의 경로이다. 
```
/usr/local/bin/node
```

내가 설치한 node는 `homebrew` 로 설치된 node 였고 경로는 아래와 같다.
```
/usr/local/opt/node@18/bin/node
```

xcode 가 node 를 찾을 수 있도록 `심볼릭 링크`를 생성한다
```console
$ln -s /usr/local/opt/node@18/bin/node /usr/local/bin/node
```


## **결과**
---
다행히 잘 빌드 된다.