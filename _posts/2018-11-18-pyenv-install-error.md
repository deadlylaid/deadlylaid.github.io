---
title: "[python] pyenv 의 python install이 안되는 문제"
layout: post
comments: true
excerpt: 특정 명령어가 갑자기 오늘 작동하지 않는 문제점, 단순히 해결만하지말고 문제의 원인을 정확하게 알고자한다.
tags: 
  - python
date: 2018-11-18
---

## Pyenv

python3.7 버전을 써보려고 pyenv에서 해당 버전을 인스톨하려고 했다.

~~~bash
$ pyenv install python3.7.0

python-build: use openssl from homebrew
python-build: use readline from homebrew
Downloading Python-3.7.0.tar.xz...
-> https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
Installing Python-3.7.0...
python-build: use readline from homebrew

BUILD FAILED (OS X 10.14 using python-build 20160303-724-g80414b1b)

Inspect or clean up the working tree at /var/folders/c5/d1_cb4j92f19h5q0_cbv7bpc0000gn/T/python-build.20181115234958.63383
Results logged to /var/folders/c5/d1_cb4j92f19h5q0_cbv7bpc0000gn/T/python-build.20181115234958.63383.log

[...]
See `config.log' for more details
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun

$
~~~

## xcode의 버전업그레이드(첫번째 원인)

macOS를 `mojave` 로 업데이트하면서 xcode를 업데이트 했어야했다.

```bash
$ xcode-select --install
$ pyenv install 3.7.0

[...]

zipimport.ZipImportError: can't decompress data; zlib not available
```

`zlib` 가 없다고해서 설치후 재실행했는데도 같은 오류가 발생했다. **xcode 버전에 있었다.**


## xcode를 업데이트 후 생긴 변화(두번째 원인)

`The Command Line Tools package`는 macOS에 `macOS system headers(헤더)`라는 것을 설치한다. 
기본적으로 `command line tool` 은 SDK에서 헤더를 검색하는데 가끔 어떤 소프트웨어는 SDK가 아니라 `/usr/include/`에 헤더가 있지 않으면 정상적으로 빌드되지 않는 문제가 있다. 하지만 `Xcode Command Line tools` 는 더 이상 `usr/include` 에 필요한 헤더를 설치해주지 않았고 이 문제를 해결하기 위해서 `system headers` 기본 시스템에 추가하는 일을 해야한다.[^footnote1]

[^footnote1]: [Xcode 10 Release Notes](https://developer.apple.com/documentation/xcode_release_notes/xcode_10_release_notes)

```bash
$ sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target 
$ xcode select -s /Library/Developer/CommandLineTools
$ brew reinstall zlib
// python2도 재설치하라고해서 재설치했다.
$ brew reinstall python@2
$
$ pyenv install 3.7.0
python-build: use openssl from homebrew
python-build: use readline from homebrew
Installing Python-3.7.0...
python-build: use readline from homebrew
Installed Python-3.7.0 to /Users/hanminsoo/.pyenv/versions/3.7.0
$
```

정상적으로 설치되었다.

문제의 원인은 첫째로 xcode가 업데이트되지 않아서였고, xcode가 업데이트 됨에 따라서 더 이상 `usr/include`에 필요한 헤더를 xcode가 설치해주지 않아서 발생하는 문제였다.


