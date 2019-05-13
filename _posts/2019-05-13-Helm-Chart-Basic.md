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

[^footnote1]: [helm](https://helm.sh/)

Helm은 기본적으로 두 개의 영역으로 나뉜다.

- helm(client-side)
- triller(server-side)

설치 또한 다르게 진행되는데 client-side는 `brew`를 통해서 설치한다.

```
brew install kubernetes-helm
```

여기서 triller의 설치가 조금 특이한데, server-side 모듈인 만큼 기본적으로 쿠버네티스 클러스내 내부에 존재한다.

명령어로 `helm init`을 함과 동시에 triller 가 설치되고 설치된 triller는 kubectl로 연결되어있는 클러스터에 연결되려고 한다.(물론 개발환경에서는 이를 로컬로 바꿀 수 있다.)

## 표준

#### 차트 명명법

차트의 이름은 `lower case`로 작성되어야만 하며, 숫자를 포함할 수는 있지만 시작은 항상 문자로 시작해야한다.

하이픈(-)은 허용되지만 이는 helm template을 사용을 까다롭게 할 수 있다.
```
// 허용되는 명명법
helm
helm-chart
helm2-chart
```

또한 디렉토리의 이름은 반드시 디렉토리가 포함하고 있는 차트의 이름과 동일해야만 한다.

#### 버전 넘버

Helm은 가능한 SemVer2를 사용하여 버전정보를 나타낸다. SevVer 버전이 쿠버네티스 라벨이 붙을 때면 레이블은 `+` 기호를 허용하지 않으므로 `+` 를 `_`로 변경한다.

#### Helm, Tiller, Chart 사용법

Helm 과 helm, 그리고 Tiller 와 tiller 명시에는 작은 규칙이 존재한다.

- 프로젝트에서 Helm이라고 언급할 때는, umbrella term 으로써 이용된다.[^footnote2]
- helm이라고 언급할 때는, client-side 를 이야기한다.
- Tiller는 백엔드 고유의 이름이다.
- tiller는 백엔드에서 실행되는 바이너리의 이름이다.

#### 버전 제한

pip의 setup.py와 비슷하게 Chart.yaml 파일의 `tillerVersion` 항목에서 버전 제한을 설정할 수 있다.

```yaml
name: minsoochart
version: 0.1.0
tillerVersion: ">=2.4.0"
```

이 제한들은 Helm의 이전버전을 지원하지 않는 새기능들을 사용할 때 설정해주어야 한다. 물론 `==`를 통한 `sophisticated SemVer rules`을 적용할 수도 있지만 `>=`를 사용하는 것이 가장 조은 방법이다.


[^footnote2]: umbrella term은 하위의 카테고리를 포함하는 단어를 의미한다. 예를 들어 '동양인' 이라는 단어에는 '한국인', '일본인', '중국인' 이 세 종류의 하위 카테고리를 포함하기때문에 '동양인'이라는 단어는 umbrella term이라고 부를 수 있다.
