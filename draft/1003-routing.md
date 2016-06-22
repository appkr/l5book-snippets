
## 3장 라우팅

### 3.1. URL

#### 3.1.1. URL의 구조

```
  http://www.example.com:80/articles?page=2&filter=no_comment#comment-21
└URL 스킴┴─────호스트─────┴포트┴──경로──┴─────────쿼리스트링─────────┴──URL 조각──┘
```

### 3.2. 라우팅 만들기

#### 3.2.1. 웰컴 뷰 수정

```html
<!-- //코드 3-1 resources/views/welcome.blade.php -->

<h1>Hello Laravel</h1>
```

```sh
# 콘솔 3-1 로컬 서버 구동

$ php artisan serve
```

![](images/3-1.png)

그림 3-1 웰컴 뷰 조작

#### 3.2.2. 라우팅이란 ?

```php
// 코드 3-2 app/Http/routes.php

Route::get('/', function () {
    return view('welcome');
});
```

#### 3.2.3. 라우팅과 HTTP 응답

```php
// 코드 3-3 app/Http/routes.php

Route::get('/', function () {
    return '<h1>Hello Foo</h1>';
});
```

### 3.3. URL 파라미터

```php
// 코드 3-4 app/Http/routes.php

Route::get('/{foo}', function ($foo) {
    return $foo;
});
```

```php
// 코드 3-5 app/Http/routes.php

Route::get('/{foo?}', function ($foo = 'bar') {
    return $foo;
});
```

#### 3.3.1. URL 파라미터 패턴

```php
// 코드 3-6 app/Http/routes.php

Route::pattern('foo', '[0-9a-zA-Z]{3}');

Route::get('/{foo?}', function ($foo = 'bar') {
    return $foo;
});
```

```php
// 코드 3-7 app/Http/routes.php

Route::get('/{foo?}', function ($foo = 'bar') {
    return $foo;
})->where('foo', '[0-9a-zA-Z]{3}');
```

### 3.4. 라우트 이름

```php
// 코드 3-8 app/Http/routes.php

Route::get('/', [
    'as' => 'home',
    function () {
        return '제 이름은 "home" 입니다.';
    }
]);

Route::get('/home', function () {
    return redirect(route('home'));
});
```

### 3.5. 마치며

```sh
$ git add .
$ git commit -m '라우팅'
$ git tag 1003-routing
```
