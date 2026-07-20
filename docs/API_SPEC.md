# API 명세서

## 1. 기본 규칙

Native PHP Hybrid App의 API는 JSON 기반 REST API로 설계합니다. Android WebView와 웹 화면은 동일한 API를 호출할 수 있어야 합니다.

## 2. 기본 URL

개발 환경 기준 기본 URL은 다음과 같이 둡니다.

```text
http://localhost/api
```

운영 또는 배포 환경에서는 `.env`의 `APP_URL` 값에 맞춰 변경합니다.

## 3. 공통 Header

### 3.1 JSON 요청

```http
Content-Type: application/json
Accept: application/json
```

### 3.2 인증 요청

인증이 필요한 API는 JWT를 `Authorization` Header로 전달합니다.

```http
Authorization: Bearer {access_token}
```

### 3.3 파일 업로드 요청

파일 업로드 API는 `multipart/form-data`를 사용합니다.

```http
Content-Type: multipart/form-data
Authorization: Bearer {access_token}
```

## 4. 공통 응답 형식

### 4.1 성공 응답

```json
{
  "success": true,
  "message": "요청이 성공했습니다.",
  "data": {}
}
```

### 4.2 실패 응답

```json
{
  "success": false,
  "message": "오류 메시지",
  "errors": {}
}
```

## 5. 공통 HTTP Status Code

| Status | 의미 | 사용 예 |
| --- | --- | --- |
| 200 | OK | 조회, 수정, 삭제 성공 |
| 201 | Created | 회원가입, 게시글 등록, 파일 업로드 성공 |
| 400 | Bad Request | 필수 값 누락, 잘못된 요청 |
| 401 | Unauthorized | 로그인 실패, 토큰 누락, 토큰 만료 |
| 403 | Forbidden | 권한 없음 |
| 404 | Not Found | 리소스 없음 |
| 409 | Conflict | 이메일 중복 |
| 422 | Unprocessable Entity | 유효성 검증 실패 |
| 500 | Internal Server Error | 서버 내부 오류 |

## 6. 인증 API

### 6.1 회원가입

```http
POST /auth/register
```

Request:

```json
{
  "email": "user@example.com",
  "password": "password1234",
  "name": "홍길동"
}
```

Response:

```json
{
  "success": true,
  "message": "회원가입이 완료되었습니다.",
  "data": {
    "user": {
      "id": 1,
      "email": "user@example.com",
      "name": "홍길동"
    }
  }
}
```

검증 항목:

- `email`: 필수, 이메일 형식, 중복 불가
- `password`: 필수, 최소 8자
- `name`: 필수, 최대 50자

### 6.2 로그인

```http
POST /auth/login
```

Request:

```json
{
  "email": "user@example.com",
  "password": "password1234"
}
```

Response:

```json
{
  "success": true,
  "message": "로그인되었습니다.",
  "data": {
    "access_token": "jwt.token.value",
    "token_type": "Bearer",
    "expires_in": 3600,
    "user": {
      "id": 1,
      "email": "user@example.com",
      "name": "홍길동"
    }
  }
}
```

### 6.3 로그아웃

```http
POST /auth/logout
```

인증:

```http
Authorization: Bearer {access_token}
```

Response:

```json
{
  "success": true,
  "message": "로그아웃되었습니다.",
  "data": null
}
```

초기 구현에서는 클라이언트가 토큰을 삭제하는 방식으로 처리합니다. 서버 측 토큰 블랙리스트가 필요하면 별도 테이블을 추가합니다.

### 6.4 내 정보 조회

```http
GET /auth/me
```

인증:

```http
Authorization: Bearer {access_token}
```

Response:

```json
{
  "success": true,
  "message": "내 정보를 조회했습니다.",
  "data": {
    "user": {
      "id": 1,
      "email": "user@example.com",
      "name": "홍길동",
      "profile_image_url": "http://localhost/uploads/profile/profile.png"
    }
  }
}
```

## 7. 게시판 API

### 7.1 게시글 목록

```http
GET /boards?page=1&limit=10
```

Response:

```json
{
  "success": true,
  "message": "게시글 목록을 조회했습니다.",
  "data": {
    "items": [
      {
        "id": 1,
        "title": "첫 번째 게시글",
        "content": "내용 일부",
        "author": {
          "id": 1,
          "name": "홍길동"
        },
        "created_at": "2026-07-20 10:00:00"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 1,
      "total_pages": 1
    }
  }
}
```

### 7.2 게시글 상세

```http
GET /boards/{id}
```

Response:

```json
{
  "success": true,
  "message": "게시글을 조회했습니다.",
  "data": {
    "board": {
      "id": 1,
      "title": "첫 번째 게시글",
      "content": "게시글 내용입니다.",
      "author": {
        "id": 1,
        "name": "홍길동"
      },
      "images": [],
      "created_at": "2026-07-20 10:00:00",
      "updated_at": "2026-07-20 10:00:00"
    }
  }
}
```

### 7.3 게시글 등록

```http
POST /boards
```

인증:

```http
Authorization: Bearer {access_token}
```

Request:

```json
{
  "title": "첫 번째 게시글",
  "content": "게시글 내용입니다."
}
```

Response:

```json
{
  "success": true,
  "message": "게시글이 등록되었습니다.",
  "data": {
    "board": {
      "id": 1,
      "title": "첫 번째 게시글",
      "content": "게시글 내용입니다."
    }
  }
}
```

### 7.4 게시글 수정

```http
PUT /boards/{id}
```

인증:

```http
Authorization: Bearer {access_token}
```

Request:

```json
{
  "title": "수정된 제목",
  "content": "수정된 내용입니다."
}
```

Response:

```json
{
  "success": true,
  "message": "게시글이 수정되었습니다.",
  "data": {
    "board": {
      "id": 1,
      "title": "수정된 제목",
      "content": "수정된 내용입니다."
    }
  }
}
```

### 7.5 게시글 삭제

```http
DELETE /boards/{id}
```

인증:

```http
Authorization: Bearer {access_token}
```

Response:

```json
{
  "success": true,
  "message": "게시글이 삭제되었습니다.",
  "data": null
}
```

## 8. 업로드 API

### 8.1 이미지 업로드

```http
POST /uploads/images
```

인증:

```http
Authorization: Bearer {access_token}
```

Form Data:

| Key | Type | 필수 | 설명 |
| --- | --- | --- | --- |
| image | file | Y | 업로드할 이미지 |
| target_type | string | N | `board`, `profile` 등 |
| target_id | number | N | 연결할 게시글 또는 사용자 ID |

Response:

```json
{
  "success": true,
  "message": "이미지가 업로드되었습니다.",
  "data": {
    "file": {
      "id": 1,
      "original_name": "sample.png",
      "stored_name": "20260720100000_sample.png",
      "url": "http://localhost/uploads/images/20260720100000_sample.png",
      "size": 12345,
      "mime_type": "image/png"
    }
  }
}
```

제한:

- 허용 확장자: `jpg`, `jpeg`, `png`, `gif`, `webp`
- 최대 용량: 5MB
- 저장 위치: `backend/public/uploads`

### 8.2 프로필 사진 업로드

```http
POST /users/me/profile-image
```

인증:

```http
Authorization: Bearer {access_token}
```

Form Data:

| Key | Type | 필수 | 설명 |
| --- | --- | --- | --- |
| image | file | Y | 프로필 이미지 |

Response:

```json
{
  "success": true,
  "message": "프로필 사진이 변경되었습니다.",
  "data": {
    "profile_image_url": "http://localhost/uploads/profile/profile.png"
  }
}
```

## 9. 오류 응답 예시

### 9.1 유효성 검증 실패

```json
{
  "success": false,
  "message": "입력값을 확인해주세요.",
  "errors": {
    "email": ["이메일은 필수입니다."],
    "password": ["비밀번호는 최소 8자 이상이어야 합니다."]
  }
}
```

### 9.2 인증 실패

```json
{
  "success": false,
  "message": "인증이 필요합니다.",
  "errors": null
}
```

### 9.3 권한 없음

```json
{
  "success": false,
  "message": "권한이 없습니다.",
  "errors": null
}
```

## 10. 구현 시 확인 사항

- 모든 입력값은 Controller 또는 Request 단계에서 검증합니다.
- 데이터베이스 접근은 PDO Prepared Statement를 사용합니다.
- 인증이 필요한 API는 JWT Middleware를 통과해야 합니다.
- 파일 업로드는 확장자, MIME Type, 파일 크기를 모두 검증합니다.
- 응답 형식은 성공/실패 모두 공통 구조를 유지합니다.
