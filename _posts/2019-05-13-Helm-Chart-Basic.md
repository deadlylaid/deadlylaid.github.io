---
title: "[DevOps] Helm Chart - (1)"
layout: post
comments: true
excerpt: "Helm basic"
tags:
  - DevOps
date: 2019-05-13
---

쿠버네티스 공부가 한창인 요즘 우연히 재미있는 서비스를 찾았다.

$$Helm$$

복잡한 쿠버네티스를 조금이나마 관리하기 쉬우라는 목적으로 탄생했는데, 기본적인 설치와 정의, 버전 업그레이드 뿐 아니라 Chart 형식의 UI를 제공함으로써 관리의 용이성을 꾀한다고 적혀있다.[^footnote1]

Helm은 기본적으로 두 개의 영역으로 나뉜다.

- helm(client-side)
- triller(server-side)

설치 또한 다르게 진행되는데 client-side는 `brew`를 통해서 설치한다.

```
brew install kubernetes-helm
```

여기서 triller의 설치가 조금 특이한데, server-side 모듈인 만큼 기본적으로 쿠버네티스 클러스내 내부에 존재한다.

명령어로 `helm init`을 함과 동시에 triller 가 설치되고 설치된 triller는 kubectl로 연결되어있는 클러스터에 연결되려고 한다.(물론 개발환경에서는 이를 로컬로 바꿀 수 있다.)

[^footnote1]: [helm](https://helm.sh/)
