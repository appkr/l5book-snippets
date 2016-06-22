
## A.3. Ubuntu Desktop

Ubuntu Desktop에 개발 환경을 준비해 보자. 참고로 저자는 Mac 컴퓨터에 `box-cutter/ubuntu1404-desktop` 베이그런트(vagrant) 이미지를 버추얼박스(virtual box)에 올려서 사용했다. 라라벨이 동작하는데 문제가 없다면 이 책에서 제시하는 것과 다른 리눅스 배포판, 다른 도구, 다른 버전을 사용해도 무방하다. 

### A.3.1. 개발 도구 설치

Ubuntu의 패키지 관리 도구인 `apt-get`을 이용하여 필요한 패키지들을 설치한다. 먼저 로컬 컴퓨터에 설치된 패키지 캐시를 업데이트한다.

```sh
# 콘솔 a-8 패키지 업데이트

$ sudo apt-get update
```

Ubuntu에서 제공하는 기본 소프트웨어 저장소는 최신 PHP 버전을 제공하지 않는다. 그래서 새로운 저장소(PPA)를 추가한다.

```sh
# 콘솔 a-9 사설 패키지 저장소 추가

# UTF-8 문자 관련 오류를 피하기 위해 언어 팩을 설치한다.
$ sudo apt-get install -y language-pack-en-base

# PPA를 추가하려면 add-apt-repository 명령줄을 포함하고 있는 패키지가 필요하다.  
$ sudo apt-get install -y software-properties-common

# 온드르제이 수리(Odřej Surý)의 PPA를 추가한다.
$ sudo LC_ALL=en_US.UTF-8 add-apt-repository ppa:ondrej/php

# 로컬 컴퓨터의 패키지 캐시를 업데이트한다.
$ sudo apt-get update
```

#### PHP

PHP 최신 버전인 7.0과 필요한 PHP 확장모듈을 설치하고, 정상 설치를 확인한다.

```sh
# 콘솔 a-10 PHP 설치 및 확인

$ sudo apt-get install -y php php-mysql php-sqlite3 php-mbstring php-xml

$ php --version 
# PHP 7.0.4-6+deb.sury.org~trusty+5 ...
```

#### MySQL

MySQL 설치 중에 `root` 사용자의 비밀번호를 입력하는 창이 나타나면 `secret` 를 입력한다(이 비밀번호의 쓰임새를 알고 잘 기억할 수 있다면, 다른 비밀번호를 넣어도 좋다).

```sh
# 콘솔 a-11 PHP 설치 및 확인

$ sudo apt-get install mysql-server

$ mysql --version
# mysql  Ver 14.14 Distrib 5.5.47, ...
```

MySQL 서버를 실행한다.

```sh
# 콘솔 a-12 MySQL 시작

$ sudo service mysql start
# ﻿mysql start/running, process xxxxx
```

#### 컴포저

컴포저를 설치한다. 컴포저는 PHP의 표준 의존성 관리 도구다.

```sh
# 콘솔 a-13 컴포저 설치 및 확인

$ curl -sS https://getcomposer.org/installer | php
$ sudo mv composer.phar /usr/local/bin/composer
$ composer --version 
# Composer version 1.0.0 ...
```

### A.3.2. PHP 확장모듈

이 책을 따라 했다면 필요한 확장모듈은 이미 준비된 상태다. 아래 명령으로 한 번 더 확인하자. 빠진 확장모듈이 있다면 `apt-get`으로 설치한다.

```sh
# 콘솔 a-14 PHP 확장모듈 확인

$ php -m | grep 'openssl\|pdo\|mbstring\|tokenizer'
# mbstring
# openssl
# pdo_***
# tokenizer
```