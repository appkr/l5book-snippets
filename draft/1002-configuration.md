
## 2장 전역 환경 설정

### 2.1. dotenv 파일이 하는 일

```sh
# 코드 2-1 .gitignore

/vendor         # 타사 컴포넌트
/node_modules   # Node.js 컴포넌트
/public/storage # 라라벨 파일 저장소 (캐시, 로그 등)
# ...
.env            # '민감한' 전역 환경 변수
```

```sh
# 코드 2-2 .env

# ...
DB_HOST=127.0.0.1       # 데이터베이스 서버 주소
DB_DATABASE=homestead   # 이 프로젝트가 사용할 데이터베이스 이름
DB_USERNAME=homestead   # 데이터베이스에 접속하기 위한 사용자 이름
DB_PASSWORD=secret      # 사용자의 비밀번호
```

```php
// 코드 2-3 config/database.php

return [
    // ...
    
    'connections' => [
        'mysql' => [
            'driver'    => 'mysql',
            'host'      => env('DB_HOST', 'localhost'),
            'database'  => env('DB_DATABASE', 'forge'),
            'username'  => env('DB_USERNAME', 'forge'),
            'password'  => env('DB_PASSWORD', ''),
            // ...
        ],
];
```

### 2.2. APP 환경 설정

### 2.3. 마치며
