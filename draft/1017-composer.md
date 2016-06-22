
## 17장 컴포저 

### 17.1. 컴포저란?

### 17.2. 로컬 컴포넌트 레지스트리 둘러보기

```javascript
"require": {
  "php": ">=5.5.9",
  "laravel/framework": "5.2.*",
  "guzzlehttp/guzzle": "~5.3|~6.0",
  "symfony/psr-http-message-bridge": "~0.2"
},
```

```javascript
"require-dev": {
  "fzaninotto/faker": "~1.4",
  "mockery/mockery": "0.9.*",
  "phpunit/phpunit": "~4.0",
  "symfony/css-selector": "2.8.*|3.0.*",
  "symfony/dom-crawler": "2.8.*|3.0.*"
},
```

```javascript
"autoload": { ... }
```

```javascript
  "scripts": { ... }
```

### 17.3. 컴포넌트 가져오기 실습 I

```sh
# 콘솔 17-1 마크다운 컴포넌트 설치

$ composer require "erusev/parsedown-extra: ~0.7.1"
# Loading composer repositories with package information
# Updating dependencies (including require-dev)
#   - Installing erusev/parsedown (1.6.0)
#     Downloading: 100%
# 
#   - Installing erusev/parsedown-extra (0.7.1)
#     Downloading: 100%
# 
# Writing lock file
# Generating autoload files
# > php artisan optimize
# Generating optimized class loader
```

#### 17.3.1. 컴포넌트 사용하기

```php
// 코드 17-1 app/Http/routes.php
    
Route::get('markdown', function () {
    $text =<<<EOT
# 마크다운 예제 1

이 문서는 [마크다운][1]으로 썼습니다. 화면에는 HTML로 변환되어 출력됩니다.

## 순서 없는 목록

- 첫 번째 항목
- 두 번째 항목[^1]

[1]: http://daringfireball.net/projects/markdown

[^1]: 두 번째 항목_ http://google.com
EOT;

    return app(ParsedownExtra::class)->text($text);
});
```

### 17.4. 컴포넌트 가져오기 실습 II

```sh
# 콘솔 17-2 라라벨 디버그 바 설치

$ composer require barryvdh/laravel-debugbar:2.2.*
```

`'provider'` 키의 맨 끝에 디버그 바의 서비스 프로바이더를 추가한다.

```php
// 코드 17-2 config/app.php

'providers' => [
    // ...
    Barryvdh\Debugbar\ServiceProvider::class,
],
```

```sh
$ php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

#### 17.4.1. 컴포넌트 사용

```php
// 코드 17-3 app/Http/Controllers/ArticlesController.php

public function show($id)
{
    $article = \App\Article::findOrFail($id);
    debug($article);
    return view('articles.show', compact('article'));
}
```

![](images/17-1.png)

그림 17-1 덤프 메시지를 표시하는 라라벨 디버그 바

#### 17.4.2. 코드 재구성

```php
// 코드 17-4 app/Providers/AppServiceProvider.php

class AppServiceProvider extends ServiceProvider
{
    public function boot() { ... }

    public function register()
    {
        if ($this->app->environment('local')) {
            $this->app->register(\Barryvdh\Debugbar\ServiceProvider::class);
        }
    }
}
```

### 17.5. install? update?

#### 17.5.1. install

#### 17.5.2. update

### 17.6. 문제 해결

### 17.7. 오토로드

```sh
public/index.php -> bootstrap/autoload.php -> vendor/autoload.php
```

### 17.8. 좋은 컴포넌트 찾기

### 17.9. 마치며

```sh
$ git commit -m '컴포저'
$ git tag 1017-composer
```
