
## 10장 엘로퀀트 ORM

### 10.1. 일대다 관계

```sh
# 콘솔 10-1 마이그레이션 클래스 뼈대 코드 만들기

$ php artisan make:migration create_articles_table --create=articles
```

```php
// 코드 10-1 database/migrations/TIMESTAMP_create_articles_table.php

class CreateArticlesTable extends Migration
{
    public function up()
    {
        Schema::create('articles', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('user_id')->unsigned()->index();
            $table->string('title');
            $table->text('content');
            $table->timestamps();

            $table->foreign('user_id')->references('id')->on('users')
                  ->onUpdate('cascade')->onDelete('cascade');
        });
    }

    public function down()
    {
        Schema::drop('articles');
    }
}
```

#### 10.1.1. 관계 연결

```sh
# 콘솔 10-2 Article 모델 뼈대 코드 만들기

$ php artisan make:model Article
```

```php
// 코드 10-2 app/Article.php

class Article extends Model
{
    protected $fillable = ['title', 'content'];

    public function user() 
    {
        return $this->belongsTo(User::class);
    }
}
```

```php
// 코드 10-3 app/User.php

class User extends Authenticatable
{
    public function articles() 
    {
        return $this->hasMany(Article::class);
    }
}
```

#### 10.1.2. 관계 확인

```sh
# 콘솔 10-3 모델간 관계를 이용해 새로운 레코드 만들기

$ php artisan tinker
>>> App\User::find(1)->articles()->create([
... 'title' => 'First article',
... 'content' => 'First content',
... ]);
# 출력 결과 생략
```

```sh
# 콘솔 10-4 모델 간의 관계를 이용한 쿼리 

>>> App\User::find(1)->articles()->get();
# 출력 결과 생략
>>> App\Article::find(1)->user()->first();
# 출력 결과 생략
```

### 10.2. 다대다 관계 연결

```sh
# 콘솔 10-5 다대다 관계 실습을 위한 마이그레이션 클래스 만들기

$ php artisan make:migration create_tags_table --create=tags
$ php artisan make:migration create_article_tag_table --create=article_tag
```

```php
// 코드 10-4 database/migrations/TIMESTAMP_create_tags_table.php

class CreateTagsTable extends Migration
{
    public function up()
    {
        Schema::create('tags', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('slug')->index();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::drop('tags');
    }
}
```

```php
// 코드 10-5 database/migrations/TIMESTAMP_create_article_tag_table.php

class CreateArticleTagTable extends Migration
{
    public function up()
    {
        Schema::create('article_tag', function (Blueprint $table) {
            $table->increments('id');
            $table->integer('article_id')->unsigned();
            $table->integer('tag_id')->unsigned();

            $table->foreign('article_id')->references('id')->on('articles')->onDelete('cascade');
            $table->foreign('tag_id')->references('id')->on('tags')->onDelete('cascade');
        });
    }

    public function down()
    {
        Schema::drop('article_tag');
    }
}
```

#### 10.2.1. 관계 연결

```sh
# 콘솔 10-6 Tag 모델 뼈대 코드 만들기

$ php artisan make:model Tag
```

```php
// 코드 10-6 app/Tag.php

class Tag extends Model
{
    protected $fillable = ['name', 'slug'];

    public function articles()
    {
        return $this->belongsToMany(Article::class);
    }
}
```

```php
// 코드 10-7 app/Article.php

class Article extends Model
{
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}
```

#### 10.2.2. 관계 확인

```sh
# 콘솔 10-7 다대다 관계 테스트를 위한 데이터 만들기

$ php artisan tinker
>>> App\Tag::create([
... 'name' => 'Foo',
... 'slug' => 'foo',
... ]);

>>> App\Tag::create([
... 'name' => 'Bar',
... 'slug' => 'bar',
... ]);

>>> App\Tag::all();
# 출력 결과 생략

>>> App\User::find(2)->articles()->create([
... 'title' => 'Second article',
... 'content' => 'Second content',
... ]);

>>> App\Article::all();
# 출력 결과 생략
```

```sh
# 콘솔 10-8 다대다 관계 테스트

>>> $article = App\Article::find(1);
>>> $article->tags()->sync([1,2]);
>>> $article->tags->pluck('name', 'id');
# => Illuminate\Support\Collection {#658
#      all: [
#        1 => "Foo",
#        2 => "Bar",
#      ],
#    }

>>> $article = App\Article::find(2);
>>> $article->tags()->sync([1]);
>>> $article->tags->pluck('name', 'id');
# 출력 결과 생략

>>> App\Tag::find(1)->articles;
# 출력 결과 생략
```

#### 10.2.3. 다대다 관계 작동 원리

```sh
# 콘솔 10-9 article_tag 피벗 테이블

mysql> SELECT * FROM article_tag;
# +----+------------+--------+
# | id | article_id | tag_id |
# +----+------------+--------+
# |  1 |          1 |      1 |
# |  2 |          1 |      2 |
# |  3 |          2 |      1 |
# +----+------------+--------+
```

### 10.3. 마치며

```sh
$ git commit -m '엘로퀀트 ORM'
$ git tag 1010-eloquent
```
