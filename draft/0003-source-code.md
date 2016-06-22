
## III. 소스코드 활용

이 책의 전체 소스코드는 깃허브(github)[^i-5]에 공개되어 있다. 언제든 로컬 컴퓨터로 복제하고 사용할 수 있다.

'아는 것과 할 수 있는 것은 다르다.' 복제한 소스코드는 참고 및 작동 확인을 위해서만 사용하고, 직접 한 줄씩 따라 하며 라라벨을 학습할 것을 권장한다.

### III.1. 소스코드 복제

콘솔 iii-1 명령으로 이 책의 전체 소스코드를 로컬 컴퓨터에 내려 받을 수 있다. Windows 사용자도 깃배시에서 `git` 명령어를 이용할 수 있다.

```sh
# 콘솔 iii-1 소스코드 복제

# 소스코드를 myapp 디렉터리에 복제한다.
$ git clone git@github.com:appkr/l5code.git myapp

# 깃허브에 SSH 키가 등록되어 있지 않다면 다음 명령을 이용한다.
$ git clone https://github.com/appkr/l5code.git myapp
```

GUI 환경을 선호하는 독자는 깃허브 데스크톱[^i-6] 프로그램을 이용할 수 있다. 

#### 태그 이동

매번 실습이 끝날 때마다 깃 버전 관리 시스템으로 커밋(commit) 메시지와 태그(tag)를 부여할 것이다. 깃허브의 커밋 메뉴 또는 깃의 비교도구(diff)를 이용하면 직전 커밋 대비 소스코드의 변경사항을 쉽게 확인할 수 있다. 그리고 콘솔 iii-2 명령으로 원하는 위치로 이동할 수 있다.

```sh
# 콘솔 iii-2 1001-installation 태그로 이동하기

$ git checkout 1001      # 까지 입력한 후 'Tab' 키를 누르고 'Enter'를 친다.
$ composer dump-autoload # 오토로드 레지스트리 업데이트
```

[편주 - 박스 겹침 방지]

> **팁** 
> 
> 명령줄이나 코드 에디터에서 <kbd>Tab</kbd> 키는 전체 명령의 일부만 타이핑하고 나머지를 자동 완성할 때 사용한다. 경로, 명령, 변수, 함수 등을 입력할 때 편리하게 사용할 수 있다.

### III.2. 소스코드 구동하기

복제한 소스코드를 작동을 확인하려면 다음 설명을 따른다. 무슨 말인지 모른다면 따라 하지 말고 그냥 넘어간다. 1부를 다 읽으면, 이해할 수 있다.

#### 프로젝트의 의존성 설치

내려 받은 소스코드는 이 프로젝트가 의존하는 컴포넌트들을 포함하지 않고 있다. 컴포저(composer)로 이 프로젝트가 의존하는 컴포넌트를 설치한다(컴포저가 없다면 부록 A를 참고해서 설치한다).

```sh
# 콘솔 iii-3 의존성 설치

# 소스코드를 복제한 myapp 디렉터리로 이동한다.
$ cd myapp

# (태그로 이동했을 때를 대비해서) 마스터 가지로 이동한다.
$ git checkout master

# 이 프로젝트가 의존하는 컴포넌트를 설치한다.
$ composer install
```

#### 환경 설정

먼저 `.env.example` 파일을 복사하여 `.env` 파일을 만든다. 파일을 열어 자신의 환경에 맞게 적절히 수정하고 저장한다. 예를 들어 사용할 데이터베이스가 `foo`라면 `DB_DATABASE=foo`로 수정한다.

```sh
# 콘솔 iii-4 프로젝트 전역 환경 설정 파일 만들기

$ cd myapp
$ cp .env.example .env
```

암호화 키를 만든다.

```sh
# 콘솔 iii-5 암호화 키 만들기

$ cd myapp
$ php artisan key:generate
# 생성된 암호화 키는 .env 파일에 이미 기록되었다.
```

Mac 또는 Linux를 사용한다면, `storage`, `bootstrap/cache`, `public/files` 디렉터리의 권한을 변경한다.

```sh
# 콘솔 iii-6 디렉터리 권한 변경

$ chmod -R 777 storage bootstrap/cache public/files
```

#### 데이터베이스 마이그레이션 및 시딩

테이블을 만들고 더미 데이터를 심는다(`.env` 파일에서 선언한 데이터베이스에 접속할 수 있어야 한다).

```sh
# 콘솔 iii-7 데이터베이스 마이그레이션

$ php artisan migrate --seed --force
```

[^i-5]: 이 책의 소스코드_ https://github.com/appkr/l5code

[^i-6]: 깃허브 데스크톱_ https://desktop.github.com