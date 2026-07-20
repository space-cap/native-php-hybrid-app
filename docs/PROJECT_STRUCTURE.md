# 프로젝트 구조

아래 구조는 Native PHP REST API와 Android WebView 앱을 함께 관리하기 위한 기본 디렉터리 설계입니다.

```text
native-php-hybrid-app/
│
├── README.md
├── LICENSE
├── .gitignore
├── docker-compose.yml
│
├── backend/
│   ├── app/
│   │   ├── Config/
│   │   ├── Controllers/
│   │   ├── Models/
│   │   ├── Services/
│   │   ├── Middleware/
│   │   ├── Helpers/
│   │   └── Core/
│   │
│   ├── api/
│   │   ├── auth/
│   │   ├── board/
│   │   ├── upload/
│   │   └── user/
│   │
│   ├── public/
│   │   ├── assets/
│   │   ├── uploads/
│   │   ├── .htaccess
│   │   └── index.php
│   │
│   ├── storage/
│   │
│   ├── vendor/
│   ├── composer.json
│   └── composer.lock
│
├── android/
│   └── HybridLoginApp/
│
├── database/
│   ├── schema.sql
│   └── seed.sql
│
├── docs/
│   ├── PROJECT_OVERVIEW.md
│   ├── FEATURE_ROADMAP.md
│   ├── PROJECT_STRUCTURE.md
│   ├── TECH_STACK.md
│   ├── DESIGN_DIRECTION.md
│   └── images/
│
└── postman/
    └── NativePHPHybrid.postman_collection.json
```

## 디렉터리 역할

### `backend/`

PHP REST API 서버 코드가 위치합니다. 인증, 게시판, 파일 업로드, 사용자 API 등 백엔드 기능의 중심입니다.

### `backend/app/`

애플리케이션의 핵심 PHP 코드가 위치합니다.

- `Config/`: 데이터베이스, 환경변수, CORS 등 설정
- `Controllers/`: 요청을 받아 서비스 계층을 호출하고 응답을 반환
- `Models/`: 데이터베이스 테이블과 매핑되는 모델
- `Services/`: 비즈니스 로직
- `Middleware/`: JWT 인증, CORS, 요청 검증 등 공통 처리
- `Helpers/`: 응답 포맷, 파일 처리, 문자열 처리 등 유틸리티
- `Core/`: 라우터, 요청/응답, 베이스 컨트롤러 등 프레임워크 역할의 핵심 코드

### `backend/api/`

기능별 API 엔드포인트가 위치합니다.

- `auth/`: 로그인, 로그아웃, 회원가입
- `board/`: 게시판 CRUD
- `upload/`: 이미지 및 파일 업로드
- `user/`: 사용자 정보 및 프로필

### `backend/public/`

Apache에서 바라보는 웹 루트입니다. 정적 파일, 업로드 파일, `.htaccess`, 진입점인 `index.php`가 위치합니다.

### `android/`

Android Studio 프로젝트가 위치합니다. Kotlin 기반 WebView 앱을 구현합니다.

### `database/`

MariaDB 스키마와 초기 데이터를 관리합니다.

### `docs/`

프로젝트 설명, 설치 방법, API 명세, 아키텍처, 변경 이력, 이미지 자료를 관리합니다.

### `postman/`

API 검증을 위한 Postman Collection을 관리합니다.
