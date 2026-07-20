# 설치 및 실행 가이드

## 1. 개요

본 문서는 Native PHP Hybrid App을 로컬 개발 환경에서 실행하기 위한 준비 절차를 정리합니다.

프로젝트는 PHP 8.3, Apache, MariaDB, Composer, Android Studio를 기준으로 합니다. Docker 환경을 우선 권장하며, Docker 없이 직접 설치하는 방식도 사용할 수 있습니다.

## 2. 필요 도구

| 도구 | 권장 버전 | 용도 |
| --- | --- | --- |
| PHP | 8.3 이상 | 백엔드 API 실행 |
| Apache | 2.4 이상 | 웹 서버 및 Rewrite |
| MariaDB | 10.6 이상 | 데이터베이스 |
| Composer | 2.x | PHP 의존성 관리 |
| Docker | 최신 안정 버전 | 로컬 개발 환경 |
| Postman | 최신 버전 | API 테스트 |
| Android Studio | 최신 안정 버전 | Android WebView 앱 개발 |
| Git | 최신 안정 버전 | 버전 관리 |

## 3. 저장소 준비

```bash
git clone {repository-url}
cd native-php-hybrid-app
```

이미 저장소를 받은 상태라면 프로젝트 루트에서 작업합니다.

```bash
cd native-php-hybrid-app
```

## 4. 환경 변수 설정

백엔드 디렉터리에 `.env` 파일을 생성합니다.

```bash
cp backend/.env.example backend/.env
```

예시:

```env
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost

DB_HOST=mariadb
DB_PORT=3306
DB_DATABASE=native_php_hybrid
DB_USERNAME=app_user
DB_PASSWORD=app_password

JWT_SECRET=change_this_secret_value
JWT_EXPIRES_IN=3600

UPLOAD_MAX_SIZE=5242880
UPLOAD_PATH=backend/public/uploads
CORS_ALLOWED_ORIGINS=http://localhost
```

주의:

- `JWT_SECRET`은 실제 배포 환경에서 반드시 긴 무작위 문자열로 변경합니다.
- `.env` 파일은 Git에 커밋하지 않습니다.

## 5. Docker 실행

`docker-compose.yml`이 준비된 경우 다음 명령으로 실행합니다.

```bash
docker compose up -d
```

컨테이너 상태를 확인합니다.

```bash
docker compose ps
```

로그 확인:

```bash
docker compose logs -f
```

종료:

```bash
docker compose down
```

## 6. Composer 설치

Docker 컨테이너 내부 또는 로컬 PHP 환경에서 Composer 의존성을 설치합니다.

```bash
cd backend
composer install
```

`vendor/` 디렉터리는 Git에 커밋하지 않습니다.

## 7. 데이터베이스 준비

데이터베이스를 생성합니다.

```sql
CREATE DATABASE native_php_hybrid
  DEFAULT CHARACTER SET utf8mb4
  DEFAULT COLLATE utf8mb4_unicode_ci;
```

스키마 파일이 준비된 경우 실행합니다.

```bash
mysql -u app_user -p native_php_hybrid < database/schema.sql
```

샘플 데이터가 필요한 경우 실행합니다.

```bash
mysql -u app_user -p native_php_hybrid < database/seed.sql
```

## 8. Apache Rewrite 설정

API 라우팅을 위해 Apache Rewrite Module이 필요합니다.

확인 항목:

- `mod_rewrite` 활성화
- `AllowOverride All` 설정
- `backend/public/.htaccess` 적용
- Document Root가 `backend/public`을 바라보도록 설정

`.htaccess` 예시:

```apache
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```

## 9. 백엔드 실행 확인

브라우저 또는 Postman에서 Health Check API를 호출합니다.

```http
GET http://localhost/api/health
```

예상 응답:

```json
{
  "success": true,
  "message": "OK",
  "data": {
    "status": "running"
  }
}
```

Health Check API가 아직 구현되지 않았다면 첫 작업으로 추가합니다.

## 10. Postman 테스트 준비

Postman Collection 파일 위치:

```text
postman/NativePHPHybrid.postman_collection.json
```

권장 Environment 변수:

| 변수 | 예시 |
| --- | --- |
| base_url | `http://localhost/api` |
| access_token | 로그인 API 응답 토큰 |
| board_id | 테스트 게시글 ID |

테스트 순서:

1. 회원가입
2. 로그인
3. 내 정보 조회
4. 게시글 등록
5. 게시글 목록 조회
6. 게시글 상세 조회
7. 게시글 수정
8. 이미지 업로드
9. 게시글 삭제

## 11. Android 실행 준비

Android Studio에서 다음 디렉터리를 엽니다.

```text
android/HybridLoginApp
```

확인 항목:

- Android SDK 설치
- Emulator 또는 실제 Android 기기 준비
- WebView 인터넷 권한 설정
- 로컬 서버 접근 주소 설정

Android Emulator에서 로컬 PC 서버에 접근할 때는 일반적으로 다음 주소를 사용합니다.

```text
http://10.0.2.2
```

실제 기기에서 테스트할 때는 PC와 기기가 같은 네트워크에 있어야 하며, PC의 내부 IP 주소를 사용합니다.

## 12. 문제 해결

### 12.1 API가 404를 반환하는 경우

- Apache Document Root가 `backend/public`인지 확인합니다.
- `.htaccess` 파일이 존재하는지 확인합니다.
- `mod_rewrite`가 활성화되어 있는지 확인합니다.

### 12.2 DB 연결이 실패하는 경우

- `.env`의 DB 접속 정보를 확인합니다.
- MariaDB 컨테이너 또는 로컬 서비스가 실행 중인지 확인합니다.
- 데이터베이스와 계정 권한이 생성되어 있는지 확인합니다.

### 12.3 JWT 인증이 실패하는 경우

- `Authorization: Bearer {token}` 형식인지 확인합니다.
- 토큰 만료 시간을 확인합니다.
- `JWT_SECRET` 값이 발급 시점과 검증 시점에 동일한지 확인합니다.

### 12.4 파일 업로드가 실패하는 경우

- 업로드 디렉터리 권한을 확인합니다.
- PHP의 `upload_max_filesize`, `post_max_size` 값을 확인합니다.
- 허용 확장자와 MIME Type인지 확인합니다.

### 12.5 Android에서 로컬 서버 접속이 안 되는 경우

- Emulator는 `localhost` 대신 `10.0.2.2`를 사용합니다.
- 실제 기기는 PC의 내부 IP 주소를 사용합니다.
- 방화벽에서 해당 포트 접근이 허용되어 있는지 확인합니다.
