# 데이터베이스 설계서

## 1. 개요

본 문서는 Native PHP Hybrid App에서 사용할 MariaDB 데이터베이스의 기본 테이블 구조와 관계를 정의합니다.

초기 v1.0 기준 핵심 도메인은 사용자, 게시글, 업로드 파일입니다.

## 2. 데이터베이스 기본 정책

- 문자셋은 `utf8mb4`를 사용합니다.
- 시간 컬럼은 `created_at`, `updated_at`, `deleted_at` 명칭을 사용합니다.
- 삭제 복구 가능성이 있는 주요 데이터는 Soft Delete를 고려합니다.
- 비밀번호는 평문 저장하지 않고 `password_hash()`로 해시 처리합니다.
- 외래키는 가능하면 명시하되, 초기 구현 난이도에 따라 애플리케이션 레벨 검증을 병행합니다.

## 3. ERD 개요

```text
users 1 ─── N boards
users 1 ─── N uploaded_files
boards 1 ── N uploaded_files
```

## 4. 테이블 목록

| 테이블 | 설명 |
| --- | --- |
| users | 사용자 계정 정보 |
| boards | 게시글 정보 |
| uploaded_files | 업로드 파일 메타데이터 |
| token_blacklist | 로그아웃 또는 폐기된 JWT 관리용 선택 테이블 |

## 5. users

사용자 계정과 프로필 정보를 저장합니다.

| 컬럼 | 타입 | 제약 | 설명 |
| --- | --- | --- | --- |
| id | BIGINT UNSIGNED | PK, AUTO_INCREMENT | 사용자 ID |
| email | VARCHAR(255) | UNIQUE, NOT NULL | 로그인 이메일 |
| password | VARCHAR(255) | NOT NULL | 해시된 비밀번호 |
| name | VARCHAR(50) | NOT NULL | 사용자 이름 |
| profile_image_url | VARCHAR(500) | NULL | 프로필 이미지 URL |
| created_at | DATETIME | NOT NULL | 생성일시 |
| updated_at | DATETIME | NULL | 수정일시 |
| deleted_at | DATETIME | NULL | 삭제일시 |

DDL 예시:

```sql
CREATE TABLE users (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  email VARCHAR(255) NOT NULL,
  password VARCHAR(255) NOT NULL,
  name VARCHAR(50) NOT NULL,
  profile_image_url VARCHAR(500) NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
  deleted_at DATETIME NULL,
  PRIMARY KEY (id),
  UNIQUE KEY uk_users_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## 6. boards

게시판의 게시글 정보를 저장합니다.

| 컬럼 | 타입 | 제약 | 설명 |
| --- | --- | --- | --- |
| id | BIGINT UNSIGNED | PK, AUTO_INCREMENT | 게시글 ID |
| user_id | BIGINT UNSIGNED | FK, NOT NULL | 작성자 ID |
| title | VARCHAR(200) | NOT NULL | 제목 |
| content | TEXT | NOT NULL | 본문 |
| view_count | INT UNSIGNED | NOT NULL, DEFAULT 0 | 조회수 |
| created_at | DATETIME | NOT NULL | 생성일시 |
| updated_at | DATETIME | NULL | 수정일시 |
| deleted_at | DATETIME | NULL | 삭제일시 |

DDL 예시:

```sql
CREATE TABLE boards (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  user_id BIGINT UNSIGNED NOT NULL,
  title VARCHAR(200) NOT NULL,
  content TEXT NOT NULL,
  view_count INT UNSIGNED NOT NULL DEFAULT 0,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
  deleted_at DATETIME NULL,
  PRIMARY KEY (id),
  KEY idx_boards_user_id (user_id),
  KEY idx_boards_created_at (created_at),
  CONSTRAINT fk_boards_user_id FOREIGN KEY (user_id) REFERENCES users (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## 7. uploaded_files

게시글 이미지, 프로필 이미지 등 업로드 파일의 메타데이터를 저장합니다.

| 컬럼 | 타입 | 제약 | 설명 |
| --- | --- | --- | --- |
| id | BIGINT UNSIGNED | PK, AUTO_INCREMENT | 파일 ID |
| user_id | BIGINT UNSIGNED | FK, NOT NULL | 업로드 사용자 ID |
| board_id | BIGINT UNSIGNED | FK, NULL | 연결된 게시글 ID |
| target_type | VARCHAR(30) | NOT NULL | `board`, `profile`, `temp` 등 |
| original_name | VARCHAR(255) | NOT NULL | 원본 파일명 |
| stored_name | VARCHAR(255) | NOT NULL | 저장 파일명 |
| path | VARCHAR(500) | NOT NULL | 서버 내부 저장 경로 |
| url | VARCHAR(500) | NOT NULL | 클라이언트 접근 URL |
| mime_type | VARCHAR(100) | NOT NULL | MIME Type |
| size | BIGINT UNSIGNED | NOT NULL | 파일 크기 |
| extension | VARCHAR(20) | NOT NULL | 파일 확장자 |
| created_at | DATETIME | NOT NULL | 생성일시 |
| deleted_at | DATETIME | NULL | 삭제일시 |

DDL 예시:

```sql
CREATE TABLE uploaded_files (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  user_id BIGINT UNSIGNED NOT NULL,
  board_id BIGINT UNSIGNED NULL,
  target_type VARCHAR(30) NOT NULL,
  original_name VARCHAR(255) NOT NULL,
  stored_name VARCHAR(255) NOT NULL,
  path VARCHAR(500) NOT NULL,
  url VARCHAR(500) NOT NULL,
  mime_type VARCHAR(100) NOT NULL,
  size BIGINT UNSIGNED NOT NULL,
  extension VARCHAR(20) NOT NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  deleted_at DATETIME NULL,
  PRIMARY KEY (id),
  KEY idx_uploaded_files_user_id (user_id),
  KEY idx_uploaded_files_board_id (board_id),
  KEY idx_uploaded_files_target_type (target_type),
  CONSTRAINT fk_uploaded_files_user_id FOREIGN KEY (user_id) REFERENCES users (id),
  CONSTRAINT fk_uploaded_files_board_id FOREIGN KEY (board_id) REFERENCES boards (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## 8. token_blacklist

초기 구현에서는 필수 테이블이 아닙니다. 서버 측 로그아웃 토큰 폐기 또는 강제 로그아웃 기능이 필요할 때 사용합니다.

| 컬럼 | 타입 | 제약 | 설명 |
| --- | --- | --- | --- |
| id | BIGINT UNSIGNED | PK, AUTO_INCREMENT | ID |
| token_hash | VARCHAR(255) | UNIQUE, NOT NULL | JWT 해시값 |
| expires_at | DATETIME | NOT NULL | 토큰 만료일시 |
| created_at | DATETIME | NOT NULL | 등록일시 |

DDL 예시:

```sql
CREATE TABLE token_blacklist (
  id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  token_hash VARCHAR(255) NOT NULL,
  expires_at DATETIME NOT NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  UNIQUE KEY uk_token_blacklist_token_hash (token_hash),
  KEY idx_token_blacklist_expires_at (expires_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## 9. 초기 데이터 예시

```sql
INSERT INTO users (email, password, name)
VALUES (
  'demo@example.com',
  '$2y$10$exampleHashedPasswordValue',
  '데모사용자'
);
```

실제 비밀번호 해시값은 PHP의 `password_hash()`로 생성해야 합니다.

## 10. 인덱스 기준

- `users.email`: 로그인 조회와 중복 검사를 위해 Unique Index를 사용합니다.
- `boards.user_id`: 사용자별 게시글 조회를 위해 Index를 사용합니다.
- `boards.created_at`: 최신순 목록 조회를 위해 Index를 사용합니다.
- `uploaded_files.user_id`: 사용자별 업로드 파일 조회를 위해 Index를 사용합니다.
- `uploaded_files.board_id`: 게시글 첨부 이미지 조회를 위해 Index를 사용합니다.

## 11. 구현 시 주의사항

- 게시글 삭제 시 첨부 파일 처리 정책을 함께 결정해야 합니다.
- 프로필 이미지를 교체할 때 기존 파일을 즉시 삭제할지 보관할지 결정해야 합니다.
- 파일의 실제 저장 여부와 DB 메타데이터 저장은 트랜잭션 관점에서 함께 검토해야 합니다.
- JWT 블랙리스트는 v1.0 필수 범위가 아니므로 필요 시 확장 기능으로 구현합니다.
