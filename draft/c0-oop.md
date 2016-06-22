# 부록 C 속성 PHP 객체지향 프로그래밍 입문

객체지향 문법을 이해해야 라라벨 개발이 수월하다. 부록 C에서는 이 책을 읽고 라라벨 프로젝트를 수행하기 위해 필요한 PHP의 객체지향 문법을 소개한다. 이하 객체지향 프로그래밍(object-oriented programming)은 OOP라 부른다.

부록 C의 최종 소스코드는 `oop-intro` 디렉터리에서 찾을 수 있다.

## C.1. 클래스

클래스는 청사진 또는 뼈대 코드라 할 수 있다. 무엇을 위한 청사진일까?

```php
// 코드 c-1 Post.php

<?php

class Post
{
    public $title;
}

$post = new Post();
$post->title = 'foo title';
echo $post->title;
```

-   `class Post { ... }`

    클래스를 정의할 때는 `class` 키워드를 쓴다. `Post`는 클래스 이름이다. 명사를 사용하는 것이 일반적이다.
    
-   `public $title;`

    방금 선언한 `Post` 클래스는 `$title` 속성을 가지고 있다. 클래스에서 사용하는 변수다. OOP에서는 프로퍼티(property)라고 부른다. `public` 키워드는 일단 무시하라.
     
-   `$post = new Post();`, `$post->title = 'foo title';`
 
    새로운 인스턴스(또는 객체)를 만들고 `title` 프로퍼티에 값을 할당했다. 인스턴스의 프로퍼티에 접근할 때 화살표(`->`) 기호를 이용한다. 
    
    우리는 지금 제목이 'foo title'인 포스트 객체를 만든 것이다. 같은 방법으로 제목이 'bar title'인 포스트 객체도 만들 수 있다. 블로그를 생각해 보면 금방 이해할 수 있다. 블로그는 여러 개의 포스트로 구성되고(인스턴스), 각 포스트는 제목으로 구분된다. 그래서 `Post` 클래스는 개별 포스트를 만들기 위한 청사진이라 할 수 있다.

콘솔에서 확인해보자(웹 서버가 작동하고 있다면, `http:://호스트/경로/Post.php`로 확인해도 된다.) 

```sh
# 콘솔 c-1

$ php Post.php
# foo title
```

## C.2. 생성자

인스턴스를 만들 때 프로퍼티 값을 지정하기 위해 생성자(constructor)를 이용한다.

```php
// 코드 c-2 Post.php

class Post
{
    public $title;

    public function __construct($title)
    {
        $this->title = $title;
    }
}

$post = new Post('foo title');
echo $post->title;
```

-   `public function __construct($title) { ... }`, `$post = new Post('foo title');`

    `__construct()`는 인스턴스를 만들 때 자동으로 실행되는 특수한 메서드다. 생성자라 한다. 생성자는 인자를 받을 수 있다. 받은 인자를 `title` 프로퍼티 값으로 할당하는데, 코드 c-1의 클래스 밖에 있던 할당 구문이 그대로 생성자 메서드 안으로 들어왔다고 볼 수 있다.
    
    OOP에서는 함수를 메서드(method)라고 부른다. 그리고, 블로그 예로 다시 돌아가면, 포스트 목록을 `[new Post('foo title'), new Post('bar title')]`처럼 표현할 수 있다. 생성자와 프로퍼티 할당 구문이 있기 때문에 포스트 목록 만들기가 편해져서 언급해 봤다.

-   `$this->title = $title;`

    `$this`는 `Post` 클래스가 아니라, `$post` 인스턴스를 의미한다. `$post2` 인스턴스를 새로 만들었다고 생각하면 이해하기 쉽다. 이때 `$this->title`은 `$post->title`이 아니라, `$post2->title`을 지칭한다.
    
## C.3. 메서드

사용자가 포스트를 읽을 때마다 조회수를 하나씩 올리자. 방문자의 읽기 이벤트가 있을 때마다, 조회수를 증가하는 메서드를 한번씩 호출해 주면 될 것이다.

```php
// 코드 c-3 Post.php

class Post
{
    public $title;
    public $viewCount = 0;

    public function __construct($title) { ... }

    public function increaseViewCount()
    {
        $this->viewCount += 1;
    }
}

$post = new Post('foo title');
$post->increaseViewCount();
$post->increaseViewCount();
var_dump($post);
```

읽기 이벤트가 두 번 발생했으므로, 코드의 실행 결과는 2다.

## C.4. 게터와 세터

게터(getter)란 클래스 인스턴스의 프로퍼티 값을 읽는 메서드, 세터(setter)란 쓰는 메서드다.

```php
// 코드 c-4 Post.php

class Post
{
    public $title;

    public function __construct($title)
    {
        $this->setTitle($title);
    }

    public function setTitle($title)
    {
        if (strlen($title) < 10) {
            throw new Exception('10 글자보다 긴 제목을 입력해 주세요');
        }

        $this->title = $title;
    }

    public function getTitle()
    {
        return ucfirst($this->title);
    }
}

$post = new Post('foo title');
echo $post->getTitle();
```

-   `$this->setTitle($title);`

    넘겨 받은 문자열을 바로 제목으로 할당하지 않고 `setTitle()` 세터 메서드에 처리를 위임했다. 클래스 밖에서 `setTitle()` 메서드를 호출하는 것이나, 생성자에서 호출하는 것이나 마찬가지다.

-   `public function setTitle($title) { ... }`

    인스턴스를 만들 때 생성자에 넘겨준 문자열이 10글자보다 짧으면 예외를 던지고 코드 실행을 멈춘다. 세터 메서드는 사용자 측에서 받은 값을 바로 할당할 때의 문제점을 해결할 수 있는 좋은 방법이다. `throw 예외클래스;` 형식으로 예외를 던진다는 것도 기억해 두자.
    
-   `public function getTitle()`

    게터 메서드다. 문장의 첫 글자를 대문자로 바꿨다.

세터 메서드로 문자열 길이를 필터링했지만, 지금 이 포스트의 제목은 안전하지 않다. `$post->title = 'short';`처럼 제목을 직접 지정할 수 있기 때문이다.

## C.5. 캡슐화

뭔지도 모른 채 `public`이란 키워드를 사용했다. 이 키워드는 프로퍼티나 메서드의 가시성(visibility)을 선언한다. 가시성이란 클래스 밖으로 노출할 정보/행동과 은닉할 것을 분류하는 방법이다. 

`public`으로 선언하면 클래스 밖에서도 프로퍼티의 값을 변경하거나, 메서드를 실행할 수 있다. 

프로퍼티나 메서드를 `protected`, `private`로 선언하면 클래스 밖에서는 접근할 수 없고 클래스 멤버만 사용할 수 있다. `private`는 클래스 자기 자신의 멤버만 사용할 수 있는 반면, `protected`는 자식 클래스의 멤버도 접근할 수 있다는 차이점이 있다.

```php
// 코드 c-5 Post.php

class Post
{
    protected $title;

    public function __construct($title)
    {
        $this->setTitle($title);
    }

    protected function setTitle($title) { ... }

    public function getTitle() { ... }
}

$post = new Post('foo title....');
$post->setTitle('short');
$post->title;
echo $post->getTitle();
```

이제 이 코드는 `Call to protected method Post::setTitle()` 오류를 일으킨다. `$post->title`처럼 제목에 직접 접근할 수도 없다.

자동차 내부의 모든 정보와 조작 장치를 운전자에게 공개할 필요는 없다. 만약 자동차 내부의 객체들까지 교환하는 데이터가 외부에 노출된다면, 자동차는 결코 안전한 이동수단이라 할 수 없을 것이다. 자동차는 핸들과 가속페달, 브레이크 등의 퍼블릭 메서드만 제공하면 된다.

## C.6. 상속

상속은 이해하기 쉽다. 실습을 위해 `Post` 클래스를 소비하는 클라이언트 코드는 주석 처리한다. 파일을 새로 만들었으니 콘솔에서 `$ php Article.php`로 테스트해야 한다.

```php
// 코드 c-6 Post.php

class Post { ... }

// $post = new Post('foo title....');
// $post->setTitle('short');
// $post->title;
// echo $post->getTitle();
```

```php
// 코드 c-7 Article.php

<?php

require 'Post.php';

class Article extends Post
{
}

echo (new Article('Lorem ipsum dolor sit amet'))->getTitle();
```

-   `require 'Post.php';`

    라라벨에서는 `require` 구문으로 다른 클래스를 임포트하지 않는다. 네임스페이스와 오토로딩을 이용하는데, 관련 내용은 책 본문에서 설명한다.

-   `class Article extends Post { ... }`

    `extends` 키워드로 부모 클래스를 상속받는다. 지금 클래스 본문에 아무것도 없지만, 코드 c-7에서 보는 바와 같이 `Post` 부모 클래스가 가진 모든 프로퍼티와 메서드는 그대로 쓸 수 있다.

자식 클래스에서 부모에게 없는 프로퍼티나 메서드를 추가할 수 있다. 또는, 코드 c-8처럼 부모 클래스의 동작을 완전히 바꿀 수도 있다.

```php
// 코드 c-8 Article.php

class Article extends Post
{
    public function getTitle()
    {
        return str_replace(' ', '_', $this->title);
    }
}
```

## C.7. 추상 클래스

앞서 만든 포스트나 아티클 인스턴스의 프로퍼티 값을 어딘가에 저장한다고 가정해 보자. 앞서 상속을 배웠으므로, 부모 클래스에 `save()` 메서드를 추가하면 자식 클래스도 쓸 수 있다. 

그런데, 포스트나 아티클 클래스에서 저장하는 로직이나, 저장하는 내용이 다르다고 가정해 보자. 물론 자식인 아티클 클래스에서 `save()` 메서드를 오버라이드하면 된다. 그런데, 포스트 클래스를 상속 받는 자식 클래스는 전부 10개나 되고, 저장 로직은 제각각이라고 할 때, 메서드 오버라이드 보다 더 나은 방법이 있다.

```php
// 코드 c-9 Writing.php

<?php

abstract class Writing
{
    abstract public function save();
}
```

-   `abstract class Writing { ... }`

    `abstract` 키워드로 추상 클래스를 만든다. 추상 클래스는 다른 클래스의 부모 구실만 할 뿐, `new` 키워드로 인스턴스를 만들 수 없다.

-   `abstract public function save();`

    `abstract` 키워드로 메서드 본문이 없는 추상 `save()` 메서드를 만든다. 이 추상 메서드는 자식 클래스가 `save()` 메서드를 구현할 것을 강제한다.

```php
// 코드 c-10 Post.php

require 'Writing.php';

class Post extends Writing
{
    // 프로퍼티나 메서드는 Writing 클래스로 옮긴다(생성자 포함).

    public function save()
    {
        echo '저장합니다.';
    }
}

new Post('Lorem ipsum dolor sit amet')->save();
```

## C.8. 의존성 주입

코드 c-10에서 포스트를 저장할 때 의사코드(pseudo code)를 사용했는데, 이번에는 저장만 전담하는 클래스를 만들어서 실제로 저장해 본다.

```php
// 코드 c-11 ArrayStorage.php

<?php

class ArrayStorage
{
    protected $collection = [];

    public function put($item)
    {
        $this->collection[] = $item;
    }

    public function collection()
    {
        return $this->collection;
    }
}
```

-   `class ArrayStorage { ... }`

    저장소 클래스다. `put()`과 `collection()` 두 개의 퍼블릭 API를 가지고 있다. 다른 클래스가 이 저장소에 `put()` 메서드 호출로 데이터를 저장하라고 지시할 것이다. `collection()`은 그간 저장한 내용을 꺼내 볼 수 있는 게터 메서드다.

```php
// 코드 c-12 Writing.php

require 'ArrayStorage.php';

abstract class Writing
{
    protected $storage;
    // ...

    public function __construct($title, ArrayStorage $storage)
    {
        $this->setTitle($title);
        $this->storage = $storage;
    }
}
```

-   `public function __construct($title, ArrayStorage $storage) { ... }`

    생성자의 두 번째 인자로 저장소 인스턴스를 받는다. 변수명 앞에 쓴 `ArrayStorage`는 타입 힌트다. 꼭 저 클래스의 인스턴스만 받겠다는 의미다. 이제 클래스의 멤버 메서드에서 저장소 인스턴스의 `put()` 메서드를 호출할 수 있다. 저장소 인스턴스가 없으면 `Writing` 클래스는 작동하지 않으므로 `ArrayStorage`에 '의존한다'고 말한다. 
    
    우리는 생성자를 통해서 의존성(dependency)을 주입했다. 여러 가지 이점이 있다. 클래스의 멤버 메서드들이 `$storage` 프로퍼티를 참조하므로 `ArrayStorage`를 다른 저장소로 쉽게 바꿀 수 있다. 메서드 본문에서 직접 인스턴스를 만들어 썼다면, 두 클래스의 결합도가 강해서 테스트, 유지보수할 때 유연성이 떨어진다. 게다가 지금 우리 코드 구조에서는 이 클래스의 고객이 저장소를 선택할 수 있는 자유도도 제공한다.

```php
// 코드 c-13 Writing.php

class Post extends Writing
{
    public function save()
    {
        $this->storage->put([
            'model' => __CLASS__,
            'title' => $this->title
        ]);
    }
}

$storage = new ArrayStorage;
(new Post('Lorem ipsum dolor sit amet', $storage))->save();
(new Post('Duis dolor in reprehenderit', $storage))->save();
var_dump($storage->collection());
```

-   `$this->storage->put([ ... ]);`

    저장소에 데이터를 저장한다. `__CLASS__`는 현재 클래스 이름을 담고 있는 PHP 내장 상수다.
    
-   `$storage = new ArrayStorage;`, `...`, `var_dump( ... )`

    앞서 만든 모든 클래스를 사용하는 클라이언트 코드다. 저장소 인스턴스를 준비해서, `Post` 클래스의 생성자에 넘겼다. `save()` 메서드 호출하고, `collection()` API를 호출해서 저장소의 현재 상태를 확인했다. 콘솔 c-2는 코드 실행 결과다.

```sh
# 콘솔 c-2

$ php Post.php
# array(2) {
#   [0] =>
#   array(2) {
#     'model' =>
#     string(4) "Post"
#     'title' =>
#     string(26) "Lorem ipsum dolor sit amet"
#   }
#   [1] =>
#   array(2) {
#     'model' =>
#     string(4) "Post"
#     'title' =>
#     string(27) "Duis dolor in reprehenderit"
#   }
# }
```

## C.9. 인터페이스

코드 c-13에서 클라이언트가 `Post::save()` 메서드를 호출했다. 요청을 받은 `Post` 클래스는 저장소의 `put()` 메서드를 호출한다. 저장소 선택권은 클라이언트가 가지고 있는데, `Post` 클래스는 클라이언트가 선택한 저장소에 `put()` 메서드가 있는지 어떻게 확신할 수 있을까? 이때 사용하는 것이 인터페이스다.

```php
// 코드 c-14 Writing.php

public function __construct($title, StorageInterface $storage) { ... }
```

-   `StorageInterface $storage`

    이제 `ArrayStorage` 인스턴스가 아니라, `StorageInterface`를 인자로 받는다. `StorageInterface`가 정한 규칙을 지키는 클래스 인스턴스는 어떤 것이든 클라이언트가 주입할 수 있다는 의미다.

```php
// 코드 c-15 StorageInterface.php

<?php

interface StorageInterface
{
    public function put($item);
    public function collection();
}
```

-   `interface StorageInterface { ... }`

    인터페이스는 `interface` 키워드로 정의한다. 인터페이스 본문에는 구현을 강제할 메서드를 쓴다. 추상 클래스의 추상 메서드처럼 이 메서드는 본문이 없다. 

```php
// 코드 c-16 Post.php

require 'FileStorage.php';

class Post extends Writing { ... }

$storage = new FileStorage;
// ...
```

-   `require 'FileStorage.php';`, `$storage = new FileStorage;`

    이제 `ArrayStorage` 대신 `FileStorage`를 저장소로 사용할 것이다. 저장소 인스턴스를 만드는 코드 외에 나머지 클라이언트 코드는 변경할 것이 없다.

```php
// 코드 c-17 FileStorage.php

<?php

require 'StorageInterface.php';

class FileStorage implements StorageInterface
{
    public function put($item)
    {
        file_put_contents('storage.txt', json_encode($item).PHP_EOL, FILE_APPEND);
    }

    public function collection()
    {
        $collection = [];
        $lines = explode(PHP_EOL, file_get_contents('storage.txt'));
        
        foreach($lines as $line) {
            if (! $line) continue;
            $collection[] = json_decode($line);
        }

        return $collection;
    }
}
```

-   `class FileStorage implements StorageInterface { ... }`

    인터페이스가 정한 약속을 지키겠다고 선언할 때는 `implements` 키워드를 쓴다. 코드 c-15의 인터페이스는 `put()`, `collection()` 메서드가 꼭 필요하다고 말하고 있고, 우리의 파일 저장소는 그 약속을 지키고 있다.
    
    이 코드의 실행 결과는 이전과 똑같다. 메모리를 사용하는 배열 저장소는 실행할 때마다 저장소의 내용이 초기화되는 반면, 파일 저장소는 이전 저장값을 계속 유지한다. 이제 클라이언트는 배열 저장소와 파일 저장소를 골라 쓸 수 있다.

## C.10. 마치며

트레이트, 네임스페이스, 오토로딩 등 PHP의 OOP에서 알아야 할 내용은 더 많다. 책 본문에서 설명한다. 그 외에도 SOLID 원칙, 디자인 패턴 등 OOP를 잘하기 위해 배워야 할 내용은 많다. 부록 C가 이 책을 이해하고, 더 나아가기 위한 주춧돌이 되었기를 바란다. 
