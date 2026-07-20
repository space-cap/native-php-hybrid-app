# 데모 시나리오

## 1. 목적

본 문서는 Native PHP Hybrid App을 면접, 포트폴리오, 발표 상황에서 설명하고 시연하기 위한 흐름을 정리합니다.

데모의 핵심은 Native PHP REST API, JWT 인증, 게시판 CRUD, 파일 업로드, Android WebView 연동이 하나의 사용자 흐름으로 연결되어 있음을 보여주는 것입니다.

## 2. 데모 준비물

- 로컬 PHP API 서버
- MariaDB 데이터베이스
- Postman Collection
- Android Studio 또는 Android Emulator
- 테스트 계정
- 업로드 테스트용 이미지 파일

## 3. 사전 준비

1. Docker 또는 로컬 서버를 실행합니다.
2. MariaDB 스키마를 적용합니다.
3. Postman Environment의 `base_url`을 설정합니다.
4. Android Emulator를 실행합니다.
5. WebView 앱의 API 주소를 로컬 서버 주소에 맞춥니다.

Android Emulator에서 PC의 로컬 서버에 접근할 때는 일반적으로 다음 주소를 사용합니다.

```text
http://10.0.2.2
```

## 4. 데모 전체 흐름

```text
프로젝트 구조 설명
→ 서버 실행
→ 회원가입
→ 로그인 및 JWT 발급
→ 인증 API 호출
→ 게시글 등록
→ 게시글 목록/상세 조회
→ 게시글 수정/삭제
→ 이미지 업로드
→ Android WebView에서 동일 흐름 확인
→ 보안 및 확장 포인트 설명
```

## 5. 발표용 설명 순서

### 5.1 프로젝트 소개

설명 포인트:

- 이 프로젝트는 Native PHP 8로 REST API를 직접 구성한 하이브리드 앱 샘플입니다.
- Android 앱은 WebView를 통해 웹 화면과 API 흐름을 사용합니다.
- Laravel이나 Spring Boot 같은 프레임워크 없이도 계층 분리, 인증, DB 접근, 파일 업로드 구조를 설명할 수 있도록 설계했습니다.

확인 문서:

- `docs/PROJECT_OVERVIEW.md`
- `docs/TECH_STACK.md`
- `docs/PROJECT_STRUCTURE.md`

### 5.2 프로젝트 구조 설명

설명 포인트:

- `backend/app`: Controller, Service, Model, Middleware 등 핵심 PHP 코드
- `backend/api`: 기능별 API 엔드포인트
- `backend/public`: Apache Document Root와 진입점
- `database`: 스키마와 샘플 데이터
- `android`: Android WebView 앱
- `docs`: 프로젝트 설명 문서
- `postman`: API 검증 Collection

### 5.3 서버 실행 시연

시연 흐름:

1. Docker 컨테이너를 실행합니다.
2. PHP, Apache, MariaDB 컨테이너 상태를 확인합니다.
3. Health Check API를 호출합니다.

예상 설명:

```text
먼저 로컬 개발 환경을 Docker로 실행합니다. 서버가 정상적으로 올라오면 Health Check API를 호출해서 API 진입점과 공통 응답 형식을 확인합니다.
```

### 5.4 회원가입 시연

Postman 요청:

```http
POST /auth/register
```

설명 포인트:

- 이메일 중복 검증
- 비밀번호 해시 저장
- 사용자 생성 후 공통 JSON 응답 반환

예상 설명:

```text
회원가입에서는 이메일 중복 여부와 필수 입력값을 검증합니다. 비밀번호는 평문으로 저장하지 않고 PHP의 password_hash 함수를 사용해 해시값으로 저장합니다.
```

### 5.5 로그인 및 JWT 인증 시연

Postman 요청:

```http
POST /auth/login
GET /auth/me
```

설명 포인트:

- 로그인 성공 시 JWT 발급
- `Authorization: Bearer` Header 사용
- 인증 미들웨어에서 토큰 검증

예상 설명:

```text
로그인에 성공하면 서버는 JWT를 발급합니다. 이후 인증이 필요한 API는 Authorization Header의 Bearer 토큰을 검증한 뒤 요청을 처리합니다.
```

### 5.6 게시판 CRUD 시연

Postman 요청:

```http
POST /boards
GET /boards
GET /boards/{id}
PUT /boards/{id}
DELETE /boards/{id}
```

설명 포인트:

- 게시글 작성자는 JWT 인증 사용자 기준으로 저장
- 수정과 삭제는 작성자만 가능
- 목록 조회는 페이지네이션 구조 사용
- 삭제된 게시글은 조회 대상에서 제외

예상 설명:

```text
게시판은 REST 방식으로 등록, 조회, 수정, 삭제 API를 분리했습니다. 수정과 삭제는 작성자 권한을 확인해서 다른 사용자가 변경하지 못하도록 처리합니다.
```

### 5.7 이미지 업로드 시연

Postman 요청:

```http
POST /uploads/images
POST /users/me/profile-image
```

설명 포인트:

- `multipart/form-data` 사용
- 확장자, MIME Type, 파일 크기 검증
- 저장 파일명 충돌 방지
- DB에 파일 메타데이터 저장
- 업로드 URL을 클라이언트에 반환

예상 설명:

```text
이미지 업로드는 파일 자체를 서버 디렉터리에 저장하고, 원본 파일명과 저장 파일명, MIME Type, 접근 URL 같은 메타데이터는 DB에 저장합니다.
```

### 5.8 Android WebView 시연

시연 흐름:

1. Android 앱 실행
2. WebView 시작 화면 확인
3. 로그인
4. 게시글 목록 확인
5. 게시글 작성 또는 상세 조회
6. 이미지 업로드 확인
7. 네트워크 오류 처리 설명

설명 포인트:

- Android 앱은 WebView 컨테이너 역할을 합니다.
- 핵심 비즈니스 로직은 PHP API 서버에 있습니다.
- Android에서는 로그인 상태, 파일 선택, 네트워크 오류 처리를 담당합니다.

예상 설명:

```text
Android 앱에서는 WebView를 사용해 동일한 사용자 흐름을 실행합니다. 서버 API를 분리했기 때문에 웹 화면과 Android 앱이 같은 백엔드 기능을 공유할 수 있습니다.
```

## 6. 예상 질문과 답변

### Q1. 왜 Laravel을 사용하지 않고 Native PHP로 만들었나요?

Native PHP로 인증, 라우팅, DB 접근, 응답 처리 구조를 직접 구성해보기 위해서입니다. 프레임워크 내부에서 자동으로 처리되는 흐름을 직접 구현하면 PHP 백엔드의 기본 동작을 더 명확히 설명할 수 있습니다.

### Q2. SQL Injection은 어떻게 방어하나요?

PDO Prepared Statement를 사용해 사용자 입력값을 SQL 문자열에 직접 연결하지 않습니다. 모든 DB 접근은 Model 또는 Repository 계층에서 준비된 쿼리로 처리합니다.

### Q3. JWT는 어디에 저장하나요?

웹 화면에서는 초기 구현 기준으로 브라우저 저장소를 사용할 수 있고, Android에서는 앱 내부 저장소를 사용할 수 있습니다. 보안 수준을 높일 경우 저장 방식과 만료 정책을 별도로 강화합니다.

### Q4. 파일 업로드 보안은 어떻게 처리하나요?

확장자, MIME Type, 파일 크기를 모두 검증합니다. 저장 파일명은 서버에서 새로 생성하며, 업로드 디렉터리에서 PHP 파일이 실행되지 않도록 서버 설정을 제한합니다.

### Q5. Android 앱의 역할은 무엇인가요?

Android 앱은 WebView를 통해 웹 화면을 표시하고, 파일 선택, 네트워크 상태 처리, 새로고침 같은 모바일 환경의 UX를 담당합니다. 핵심 데이터 처리는 PHP API 서버가 담당합니다.

## 7. 데모 체크리스트

- [ ] 서버 실행 상태를 확인했습니다.
- [ ] DB 연결 상태를 확인했습니다.
- [ ] Postman Environment가 준비되었습니다.
- [ ] 회원가입 요청이 성공합니다.
- [ ] 로그인 요청이 성공하고 JWT가 발급됩니다.
- [ ] 인증 API가 JWT로 보호됩니다.
- [ ] 게시글 등록, 조회, 수정, 삭제가 동작합니다.
- [ ] 이미지 업로드가 동작합니다.
- [ ] Android WebView에서 주요 화면이 표시됩니다.
- [ ] 데모용 테스트 이미지가 준비되었습니다.

## 8. 마무리 설명 포인트

- 이 프로젝트는 단순 화면 예제가 아니라 백엔드 API, DB, 인증, 업로드, Android 연동까지 연결된 실무형 샘플입니다.
- Native PHP로 직접 구현했기 때문에 프레임워크의 동작 원리를 이해하고 있음을 설명할 수 있습니다.
- 향후 Laravel, Spring Boot, React, Vue, Flutter 등으로 확장하더라도 현재 구조를 기준으로 계층과 책임을 나눌 수 있습니다.
