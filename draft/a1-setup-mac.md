
## A.2. Mac

라라벨이 동작하는데 문제가 없다면 이 책에서 제시하는 것과 다른 도구, 다른 버전을 사용해도 무방하다. 

### A.2.1. 개발도구 설치

#### 홈브루

홈브루(Homebrew)는 Mac용 패키지 관리 도구다. PHP, MySQL 등 실습에 필요한 다른 패키지를 홈브루를 이용해서 설치할 것이다.

```sh
# 콘솔 a-1 홈브루 설치 및 설치 확인

# 긴주소 타이핑이 번거롭다면 `homebrew install`로 구글링해서 복사해서 사용한다.
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

$ brew --version 
# Homebrew 0.9.5 (git revision 53d0; last commit 2016-02-22)
```

잘 설치되었나 확인하자. 주석으로 표시된 문자열이 표시되면 정상적으로 설치된 것이다.

> **팁** 
> 
> 버전이 출력되지 않으면 콘솔을 다시 시작하고 다시 해 본다. 설치 과정에 사용자의 콘솔 환경 설정에 필요한 경로를 추가했는데 추가된 설정을 적용하기 위함이다.
>
> 홈브루는 필요한 소스코드를 내려 받아 운영체제 환경에 맞게 직접 컴파일한다. 컴파일에 필요한 도구는 XCode로 설치하면 편리하다(`$ xcode-select --install`).

#### PHP

PHP 최신 버전인 7.0 을 설치한다. 홈브루로 설치가 가능한지 먼저 확인하자.

```sh
# 콘솔 a-2 PHP 탭 추가

$ brew tap homebrew/php
$ brew search php70
# homebrew/php/php70    homebrew/php/php70-hprose    ...
```

있다! `homebrew/php/php70` 를 설치하자. 설치 후에는 정상 설치되었는지 확인해야 한다.

```sh
# 콘솔 a-3 PHP 설치 및 설치 확인

$ brew install homebrew/php/php70

$ php --version 
# PHP 7.0.x ...
```

#### MySQL

홈브루로 MySQL을 설치하고, 루트 사용자의 비밀번호를 `secret`로 설정한다. 명령을 실행하면 나오는 첫 질문에 `N`으로 답해야 짧은 비밀번호를 쓸 수 있으니 `Y`로 답하지 않도록 주의한다. 이 비밀번호의 쓰임새를 알고 잘 기억할 수 있다면,`Y`를 눌러 다른 비밀번호를 넣어도 좋다. MySQL 시작과 종료를 편리하게 하기 위한 확장 기능도 추가하자.

```sh
# 콘솔 a-4 MySQL 설치 및 설치 확인

$ brew install mysql

$ mysql --version
# mysql  Ver 14.14 Distrib 5.7.11, ...

$ mysql_secure_installation
# ...
# Would you like to setup VALIDATE PASSWORD plugin?
# Press y|Y for Yes, any other key for No:          # N
# Please set the password for root here.
# New password:                                     # secret
# Re-enter new password:                            # secret
# ...
# All done!

$ brew tap homebrew/services
```

이제 MySQL 서버를 시작하고 종료할 때 아래 명령을 이용할 수 있다.

```sh
# 콘솔 a-5 MySQL 시작과 종료

$ brew services start mysql
# ==> Successfully started 'mysql' (label: homebrew.mxcl.mysql)

$ brew services stop mysql
# ==> Successfully stopped 'mysql' (label: homebrew.mxcl.mysql)
```

#### 컴포저

컴포저를 설치한다. 컴포저는 PHP의 표준 의존성 관리 도구다.

```sh
# 콘솔 a-6 컴포저 설치

$ brew install homebrew/php/composer
$ composer --version 
# Composer version 1.0.0 ...
```

### A.2.2. PHP 확장모듈

이 책을 따라 했다면 필요한 확장모듈은 이미 준비된 상태다. 아래 명령으로 한 번 더 확인하자. 빠진 확장모듈이 있다면 홈브루로 검색해서 설치한다. 

```sh
# 콘솔 a-7 PHP 확장모듈 확인

$ php -m | grep 'openssl\|pdo\|mbstring\|tokenizer'
# mbstring
# openssl
# pdo_mysql
# tokenizer
```