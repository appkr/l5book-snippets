
## 6장 데이터베이스와 모델

### 6.1. 데이터베이스 준비

```sh
# 콘솔 6-1 MySQL 접속 (관리자 계정)

$ mysql -uroot -p
# Enter password: secret

mysql>
```

```sh
# 콘솔 6-2 데이터베이스 만들기

mysql> CREATE DATABASE myapp;
# Query OK, 1 row affected (0.01 sec)

mysql> CREATE USER 'homestead' IDENTIFIED BY 'secret';
# Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALTER, CREATE, INSERT, SELECT, DELETE, REFERENCES, UPDATE, DROP, EXECUTE, LOCK TABLES, INDEX ON myapp.* TO 'homestead';
# Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH PRIVILEGES;
# Query OK, 0 rows affected (0.00 sec)

mysql> quit
# Bye

$ 
```

```sh
# 콘솔 6-3 MySQL 접속 (사용자 계정)

$ mysql -uhomestead -p
# Enter password: secret

mysql>
```

```sh
# 콘솔 6-4 테이블 만들기

mysql> use myapp;
# Database changed

mysql> CREATE TABLE posts(
id INT(11) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
title VARCHAR(255),
body TEXT
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
# Query OK, 0 rows affected (0.04 sec)

mysql> describe posts;
# +-------+------------------+------+-----+---------+----------------+
# | Field | Type             | Null | Key | Default | Extra          |
# +-------+------------------+------+-----+---------+----------------+
# | id    | int(11) unsigned | NO   | PRI | NULL    | auto_increment |
# | title | varchar(255)     | YES  |     | NULL    |                |
# | body  | text             | YES  |     | NULL    |                |
# +-------+------------------+------+-----+---------+----------------+
# 3 rows in set (0.01 sec)
```

### 6.2. REPL

```sh
# 콘솔 6-5 팅커(REPL) 콘솔

$ php artisan tinker
# Psy Shell v0.6.1 (PHP 7.0.3 — cli) by Justin Hileman

>>>
```

### 6.3. 데이터베이스 쿼리

```sh
# 콘솔 6-6 QueryException

$ php artisan tinker

>>> DB::select('SELECT * FROM posts');
# Illuminate\Database\QueryException with message 'SQLSTATE[42S02]: Base table or view not found: 1146 Table 'homestead.posts' doesn't exist (SQL: SELECT * FROM posts)'
```

```sh
# 코드 6-1 .env

DB_HOST=127.0.0.1
DB_DATABASE=myapp
DB_USERNAME=homestead
DB_PASSWORD=secret
```

```sh
# 콘솔 6-7 팅커 콘솔에서 데이터베이스 쿼리

$ php artisan tinker

>>> DB::select('SELECT * FROM posts');
# => []
```

#### 6.3.1. 데이터 삽입

```sh
# 콘솔 6-8 팅커 콘솔에서 데이터베이스 레코드 삽입 

>>> DB::insert('INSERT INTO posts(title, body) VALUES(?, ?)', ['Hello Database', 'Greetings from tinker']);
# => true

>>> DB::insert('INSERT INTO posts(title, body) VALUES(?, ?)', ['Ola Database', 'Saludos de tinker']);
# => true
```

#### 6.3.2. 데이터 조회

```sh
# 콘솔 6-9 컬렉션 쿼리

>>> $posts = DB::select('SELECT * FROM posts');
# => [
#      {#624
#        +"id": 1,
#        +"title": "Hello Database",
#        +"body": "Greetings from tinker",
#      },
#      {#626
#        +"id": 2,
#        +"title": "Ola Database",
#        +"body": "Saludos de tinker",
#      },
#    ]
```

```sh
# 콘솔 6-10 프로퍼티 접근

>>> $posts[0]->title;
# => "Hello Database"
```

```sh
# 콘솔 6-11 인스턴스 쿼리

>>> $post = DB::selectOne('SELECT * FROM posts WHERE id = ?', [1]);
# => {#633
#      +"id": 1,
#      +"title": "Hello Database",
#      +"body": "Greetings from tinker",
#    }
```

### 6.4. 쿼리 빌더

#### 6.4.1. 데이터 조회

```sh
# 콘솔 6-12 get() 메서드로 컬렉션 조회

>>> DB::table('posts')->get();
# => [
#      {#630
#        +"id": 1,
#        +"title": "Hello Database",
#        +"body": "Greetings from tinker",
#      },
#      {#616
#        +"id": 2,
#        +"title": "Ola Database",
#        +"body": "Saludos de tinker",
#      },
#    ]
# >>>
```

```sh
# 콘솔 6-13 first(), find() 메서드로 인스턴스 조회

>>> DB::table('posts')->first();
# => {#627
#      +"id": 1,
#      +"title": "Hello Database",
#      +"body": "Greetings from tinker",
#    }
   
>>> DB::table('posts')->find(2);
# => {#628
#      +"id": 2,
#      +"title": "Ola Database",
#      +"body": "Saludos de tinker",
#    }
```

#### 6.4.2. 조건절

```sh
# 콘솔 6-14 조건절 이용

>>> DB::table('posts')->where('id', '=', 1)->get();
>>> DB::table('posts')->where('id', 1)->get();
>>> DB::table('posts')->whereId(1)->get();
>>> DB::table('posts')->where(function ($query) {$query->where('id', 1);})->get();
# => [
#      {#nnn
#        +"id": 1,
#        +"title": "Hello Database",
#        +"body": "Greetings from tinker",
#      },
#    ]
```

#### 6.4.3. 다른 메서드

### 6.5. 엘로퀀트 ORM

#### 6.5.1. 모델

```sh
# 콘솔 6-15 테스트 테이블 및 데이터 만들기

$ mysql -uhomestead -p

mysql> use myapp;
# Database changed

mysql> CREATE TABLE authors(
id INT(11) UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
email VARCHAR(255) NOT NULL,
password VARCHAR(60) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
# Query OK, 0 rows affected (0.06 sec)

mysql> show tables;
# +-----------------+
# | Tables_in_myapp |
# +-----------------+
# | authors         |
# | posts           |
# +-----------------+
# 2 rows in set (0.00 sec)

mysql> INSERT INTO authors(email, password) VALUES('john@example.com', 'password');
# Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM authors;
# +----+------------------+----------+
# | id | email            | password |
# +----+------------------+----------+
# |  1 | john@example.com | password |
# +----+------------------+----------+
# 1 row in set (0.01 sec)
```

```sh
# 콘솔 6-16 모델 만들기

$ php artisan make:model Post
# Model created successfully.

$ php artisan make:model Author
# Model created successfully.
```

```php
// 코드 6-2 app/Author.php

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Author extends Model
{
    //
}
```

#### 6.5.2. 모델 쿼리

```sh
# 콘솔 6-17 엘로퀀트 쿼리

>>> App\Author::get();
# => Illuminate\Database\Eloquent\Collection {#612
#      all: [
#        App\Author {#615
#          id: 1,
#          email: "john@example.com",
#          password: "password",
#        },
#      ],
#    }
```

```sh
# 콘솔 6-18 엘로퀀트로 새로운 레코드 만들기

>>> $author = new App\Author;
# => App\Author {#618}
>>> $author->email = 'foo@bar.com';
# => "foo@bar.com"
>>> $author->password = 'password';
# => "password"
>>> $author->save();
# Illuminate\Database\QueryException with message 'SQLSTATE[42S22]: Column not found: 1054 Unknown column 'updated_at' in 'field list' ...
```

#### 6.5.3. QueryException

```php
// 코드 6-3 app/Author.php

class Author extends Model
{
    public $timestamps = false;
}
```

```sh
# 콘솔 6-19 엘로퀀트로 새로운 레코드 만들기 (재시도)

# ...
>>> $author->save();
# => true
>>> App\Author::get();
# 출력 결과 생략
```

#### 6.5.4. 다른 메서드로 데이터 저장

```sh
# 콘솔 6-20 대량 할당

>>> App\Author::create([
'email' => 'bar@baz.com',
'password' => bcrypt('password'),
]);
# Illuminate\Database\Eloquent\MassAssignmentException with message 'email'
```

#### 6.5.5. MassAssignmentException

```php
// 코드 6-4 app/Author.php

class Author extends Model
{
    public $timestamps = false;
    protected $fillable = ['email', 'password'];
}
```

```php
// 코드 6-5 app/Post.php

class Post extends Model
{
    public $timestamps = false;
    protected $fillable = ['email', 'password'];
}
```

```sh
# 콘솔 6-21 대량 할당 (재시도)
>>> App\Author::create([
'email' => 'bar@baz.com',
'password' => bcrypt('password'),
]);
# => App\Author {#628
#      email: "bar@baz.com",
#      password: "$2y$10$my12WdNz2pYx9Kcki4bnm.GOPIrpbq9K99Th42Vp9H/OzUuCJg.T2",
#      id: 3,
#    }
>>> App\Author::get();
# 출력 결과 생략
```

### 6.6. 마치며

```sh
$ git commit -m '데이터베이스와 모델'
$ git tag 1006-database-model
```
