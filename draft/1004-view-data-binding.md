
## 4장 뷰와 데이터 바인딩  

### 4.1. 뷰 반환

```php
// 코드 4-1 app/Http/routes.php

Route::get('/', function () {
    return view('errors.503');
});
```

### 4.2. 데이터 바인딩

#### 4.2.1. with() 메서드 이용하는 방법

```php
// 코드 4-2 app/Http/routes.php

Route::get('/', function () {
    return view('welcome')->with('name', 'Foo');
});
```

```html
<!-- // 코드 4-3 resources/views/welcome.blade.php -->

<h1>Hello <?= $name; ?> <?= isset($name) ? ", {$name}?" : ''; ?></h1>
```

```php
// 코드 4-4 app/Http/routes.php

Route::get('/', function () {
    return view('welcome')->with([
        'name' => 'Foo',
        'greeting' => '안녕하세요?',
    ]);
});
```

#### 4.2.2. view() 함수 이용하는 방법

```php
// 코드 4-5 app/Http/routes.php

Route::get('/', function () {
    return view('welcome', [
        'name' => 'Foo',
        'greeting' => '안녕하세요?',
    ]);
});
```

### 4.3. 마치며

```sh
$ git commit -m '뷰와 데이터 바인딩'
$ git tag 1004-view-data-binding
```