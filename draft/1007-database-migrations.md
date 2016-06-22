
## 7장 데이터베이스 마이그레이션

### 7.1. 마이그레이션 만들기

```sh
# 콘솔 7-1 데이터베이스 정리

$ mysql -uhomestead -p

mysql> use myapp;
# Database changed

mysql> SET FOREIGN_KEY_CHECKS=0;
# Query OK, 0 rows affected (0.00 sec)

mysql> DROP TABLE posts;
# Query OK, 0 rows affected (0.02 sec)

mysql> DROP TABLE authors;
# Query OK, 0 rows affected (0.01 sec)

mysql> SET FOREIGN_KEY_CHECKS=1;
# Query OK, 0 rows affected (0.00 sec)
```

```sh
# 콘솔 7-2 마이그레이션 클래스 뼈대 코드 만들기

$ php artisan make:migration create_posts_table --create=posts
# Created Migration: TIMESTAMP_create_posts_table
$ php artisan make:migration create_authors_table --create=authors
# Created Migration: TIMESTAMP_create_authors_table
```

```php
// 코드 7-1 database/migrations/TIMESTAMP_create_posts_table.php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreatePostsTable extends Migration
{
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->increments('id');
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::drop('posts');
    }
}
```

```php
// 코드 7-2 database/migrations/TIMESTAMP_create_authors_table.php

class CreateAuthorsTable extends Migration
{
    public function up()
    {
        Schema::create('authors', function (Blueprint $table) {
            $table->increments('id');
            $table->string('email', 255);
            $table->string('password', 60);
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::drop('authors');
    }
}
```

### 7.2. 마이그레이션 실행

```sh
# 콘솔 7-3 마이그레이션 실행

$ php artisan migrate
# Migration table created successfully.
# Migrated: 2014_10_12_000000_create_users_table
# Migrated: 2014_10_12_100000_create_password_resets_table
# Migrated: TIMESTAMP_create_posts_table
# Migrated: TIMESTAMP_create_authors_table
```

```sh
# 콘솔 7-4 마이그레이션 실행 결과 확인

$ mysql -uhomestead -p

mysql> use myapp;
# Database changed

mysql> DESCRIBE posts;
# +------------+------------------+------+-----+---------+----------------+
# | Field      | Type             | Null | Key | Default | Extra          |
# +------------+------------------+------+-----+---------+----------------+
# | id         | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
# | title      | varchar(255)     | NO   |     | NULL    |                |
# | body       | text             | NO   |     | NULL    |                |
# | created_at | timestamp        | YES  |     | NULL    |                |
# | updated_at | timestamp        | YES  |     | NULL    |                |
# +------------+------------------+------+-----+---------+----------------+
# 5 rows in set (0.01 sec)

mysql> DESCRIBE authors;
# +------------+------------------+------+-----+---------+----------------+
# | Field      | Type             | Null | Key | Default | Extra          |
# +------------+------------------+------+-----+---------+----------------+
# | id         | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
# | email      | varchar(255)     | NO   |     | NULL    |                |
# | password   | varchar(60)      | NO   |     | NULL    |                |
# | created_at | timestamp        | YES  |     | NULL    |                |
# | updated_at | timestamp        | YES  |     | NULL    |                |
# +------------+------------------+------+-----+---------+----------------+
# 5 rows in set (0.00 sec)
```

### 7.3. 롤백

```sh
# 콘솔 7-5 롤백

$ php artisan migrate:rollback
# Rolled back: TIMESTAMP_create_authors_table
# Rolled back: TIMESTAMP_create_posts_table
# Rolled back: TIMESTAMP_create_password_resets_table
# Rolled back: TIMESTAMP_create_users_table
```

```sh
# 콘솔 7-6 마이그레이션 재실행

$ php artisan migrate
# 출력 결과 생략
```

### 7.4. 열 추가

```sh
# 콘솔 7-7 테이블 열 추가 마이그레이션 뼈대 코드 만들기

$ php artisan make:migration add_name_to_authors_table --table=authors
# Created Migration: TIMESTAMP_add_name_to_authors_table
```

```php
// 코드 7-3 database/migrations/TIMESTAMP_add_name_to_authors_table.php

class AddNameToAuthorsTable extends Migration
{
    public function up()
    {
        Schema::table('authors', function (Blueprint $table) {
            $table->string('name')->nullable();
        });
    }

    public function down()
    {
        Schema::table('authors', function (Blueprint $table) {
            $table->dropColumn('name');
        });
    }
}
```

```sh
# 콘솔 7-8 마이그레이션 실행

$ php artisan migrate
# Migrated: TIMESTAMP_add_name_to_authors_table
```

```sh
# 콘솔 7-9 마이그레이션 실행 결과 확인

$ mysql -uhomestead -p

mysql> use myapp;
# Database changed

mysql> describe authors;
# +-------+--------------+------+-----+---------+-------+
# | Field | Type         | Null | Key | Default | Extra |
# +-------+--------------+------+-----+---------+-------+
# ...
# | name  | varchar(255) | YES  |     | NULL    |       |
# +-------+--------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
```

### 7.5. 초기화 및 새로고침

```sh
# 콘솔 7-10 마이그레이션 새로고침(refresh)

$ php artisan migrate:refresh
# Rolled back: TIMESTAMP_add_name_to_authors_table
# Rolled back: TIMESTAMP_create_authors_table
# Rolled back: TIMESTAMP_create_posts_table
# Migrated: TIMESTAMP_create_posts_table
# Migrated: TIMESTAMP_create_authors_table
# Migrated: TIMESTAMP_add_name_to_authors_table
```
### 7.6. 마치며

```sh
$ git commit -m '데이터베이스 마이그레이션'
$ git tag 1007-database-migrations
```
