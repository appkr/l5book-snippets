
## 5장 블레이드

### 5.1. 변수를 이용한 문자열 보간

```html
<!-- // 코드 5-1 resources/views/welcome.blade.php -->

<h1>Hello {{ $name }} {{ $greeting or '' }}</h1>
```

### 5.2. 주석

```html
<!-- // 코드 5-2 resources/views/welcome.blade.php -->

<!--HTML 주석 안에서 {{ $name }}을(를) 출력합니다.-->
{{--블레이드 주석 안에서 {{ $name }}을(를) 출력합니다.--}}
<h1>Hello {{ $name }} {{ $greeting or '' }}</h1>
```

```html
<!-- // 코드 5-3 view-source:http://호스트:포트/-->

<!--HTML 주석 안에서 Foo을(를) 출력합니다.-->
<h1>Hello Foo 안녕하세요?</h1>
```

### 5.3. 제어 구조

#### 5.3.1. 조건문

```html
<!-- // 코드 5-4 resources/views/welcome.blade.php -->

@if($itemCount = count($items))
  <p>{{ $itemCount }} 종류의 과일이 있습니다.</p>
@else
  <p>엥~ 아무것도 없는데요!</p>
@endif
```

![](images/5-1.png)

그림 5-1 라라벨의 웁스(whoops) 페이지

```php
// 코드 5-5 app/Http/routes.php

Route::get('/', function () {
    $items = ['apple', 'banana', 'tomato'];

    return view('welcome', ['items' => $items]);
});
```

#### 5.3.2. 반복문

```html
<!-- // 코드 5-6 resources/views/welcome.blade.php -->

<ul>
  @foreach($items as $item)
    <li>{{ $item }}</li>
  @endforeach
</ul>
```

```html
<!-- // 코드 5-7 resources/views/welcome.blade.php -->

<ul>
  @forelse($items as $item)
    <li>{{ $item }}</li>
  @empty
    <li>엥~ 아무것도 없는데요!</li>
  @endforelse
</ul>
```

### 5.4. 템플릿 상속

```html
<!-- // 코드 5-8 resources/views/layouts/master.blade.php -->

<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>라라벨 입문</title>
</head>
<body>

  @yield('content')

</body>
</html>
```

```html
<!-- // 코드 5-9 resources/views/welcome.blade.php -->

@extends('layouts.master')

@section('content')
  <p>저는 자식 뷰의 'content' 섹션입니다.</p>
@endsection
```

```html
<!-- // 코드 5-10 resources/views/welcome.blade.php -->

@extends('layouts.master')

@section('style')
  <style>
    body {background: green; color: white;}
  </style>
@endsection

@section('content')
  <p>저는 자식 뷰의 'content' 섹션입니다.</p>
@endsection

@section('script')
  <script>
    alert("저는 자식 뷰의 'script' 섹션입니다.");
  </script>
@endsection
```

### 5.5. 조각 뷰 삽입

```html
<!-- // 코드 5-11 resources/views/partials/footer.blade.php -->

<footer>
  <p>저는 꼬리말입니다. 다른 뷰에서 저를 입양해 가요.</p>
</footer>
```

```html
<!-- // 코드 5-12 resources/views/welcome.blade.php -->

@extends('layouts.master')

@section('content')
  @include('partials.footer')
@endsection
```

#### 5.5.1. 섹션의 상속

```html
<!-- // 코드 5-13 resources/views/welcome.blade.php -->

@extends('layouts.master')

@section('content')
  @include('partials.footer')
@endsection

@section('script')
  <script>
    alert("저는 자식 뷰의 'script' 섹션입니다.");
  </script>
@endsection
```

```html
<!-- // 코드 5-14 resources/views/partials/footer.blade.php -->

@section('script')
  <script>
    alert("저는 조각 뷰의 'script' 섹션입니다.");
  </script>
@endsection
```

```html
<!-- // 코드 5-15 resources/views/partials/footer.blade.php -->

@section('script')
  @parent
  <script>
    alert("저는 조각 뷰의 'script' 섹션입니다.");
  </script>
@endsection
```

### 5.6. 마치며

```sh
$ git commit -m '블레이드'
$ git tag 1005-blade
```
