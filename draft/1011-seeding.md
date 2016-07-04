
## 11장 데이터베이스 시딩

### 11.1. 시더 만들기

```sh
# 콘솔 11-1 시더 클래스 뼈대 코드 만들기 

$ php artisan make:seeder UsersTableSeeder
# Seeder created successfully.
```

```php
// 코드 11-1 database/seeds/UsersTableSeeder.php

class UsersTableSeeder extends Seeder
{
    public function run()
    {
        App\User::create([
            'name' => sprintf('%s %s', str_random(3), str_random(4)),
            'email' => str_random(10) . '@example.com',
            'password' => bcrypt('password'),
        ]);
    }
}
```

```sh
# 콘솔 11-2 데이터베이스 시더 실행 (시딩)

$ php artisan db:seed --class=UsersTableSeeder
```

### 11.2. 모델 팩터리

```sh
# 콘솔 11-3 모델 팩터리 맛보기

$ php artisan tinker
>>> factory(App\User::class)->make();
# => App\User {#669
#      name: "Brian Kessler",
#      email: "Carlie.Keeling@example.com",
#    }
>>> factory(App\User::class)->make(['name' => 'Foo']);
# => App\User {#663
#      name: "Foo",
#      email: "Vivianne76@example.org",
#    }
```

#### 11.2.1. 시더 수정 

```php
// 코드 11-2 database/seeds/UsersTableSeeder.php

class UsersTableSeeder extends Seeder
{
    public function run()
    {
        factory(App\User::class, 5)->create();
    }
}
```

#### 11.2.2. 모델 팩터리 추가

```php
// 코드 11-3 database/factories/ModelFactory.php

$factory->define(App\User::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->safeEmail,
        'password' => bcrypt('password'),
        'remember_token' => str_random(10),
    ];
});
```

```php
// 코드 11-4 database/factories/ModelFactory.php

$factory->define(App\Article::class, function (Faker\Generator $faker) {
    $date = $faker->dateTimeThisMonth;
    
    return [
        'title' => $faker->sentence(),
        'content' => $faker->paragraph(),
        'created_at' => $date,
        'updated_at' => $date,
    ];
});
```

```sh
# 콘솔 11-4 articles 테이블 시더 뼈대 코드 만들기

$ php artisan make:seeder ArticlesTableSeeder
```

```php
// 코드 11-5 database/seeds/ArticlesTableSeeder.php

class ArticlesTableSeeder extends Seeder
{
    public function run()
    {
        $users = App\User::all();

        $users->each(function ($user) {
            $user->articles()->save(
                factory(App\Article::class)->make()
            );
        });
    }
}
```

### 11.3. 마스터 시더

```php
// 코드 11-6 database/seeds/DatabaseSeeder.php

class DatabaseSeeder extends Seeder
{
    public function run()
    {
        if (config('database.default') !== 'sqlite') {
            DB::statement('SET FOREIGN_KEY_CHECKS=0');
        }

        App\User::truncate();
        $this->call(UsersTableSeeder::class);

        App\Article::truncate();
        $this->call(ArticlesTableSeeder::class);

        if (config('database.default') !== 'sqlite') {
            DB::statement('SET FOREIGN_KEY_CHECKS=1');
        }
    }
}
```

```sh
# 콘솔 11-5 데이터베이스 시딩

$ php artisan db:seed
# Seeded: UsersTableSeeder
# Seeded: ArticlesTableSeeder
```

### 11.4. 마이그레이션과 시딩

```sh
# 콘솔 11-6 마이그레이션과 시딩 한번에 실행하기

$ php artisan migrate:refresh --seed
```

### 11.5. 마치며

```sh
$ git commit -m '데이터베이스 시딩'
$ git tag 1011-seeding
```
