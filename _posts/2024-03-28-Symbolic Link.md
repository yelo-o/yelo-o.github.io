---
layout: single
title:  "Symbolic Link"
categories: coding
tag: [windowOS, linux]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

## Symbolic Link란? 
링크를 연결하여 원본 파일을 직접 사용하는 것과 같은 효과를 내는 링크입니다. 
보통 리눅스에서 사용되는데, 윈도우에서도 사용이 가능합니다.

## 적용 방법
1. 관리자 모드로 명령프롬프트 창을 실행합니다.

2. 복사할 폴더로 이동합니다.
```shell
cd C:\test\html
```

3. mklink /d [[display될 폴더명]] [[복사해야 하는 폴더]]
```shell
mklink /d css C:\test\css
```

## 결과화면
![심볼릭링크결과](https://github.com/yelo-o/yelo-o.github.io/assets/64743180/ced55ce8-8525-4a3d-a409-ca8def14a031)

위의 화면처럼 html/ 폴더에 css 폴더가 링크된 것을 확인하실 수 있습니다.
