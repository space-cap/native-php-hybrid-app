# Native PHP Hybrid App

Android WebView 기반 하이브리드 앱과 Native PHP REST API를 이용한 샘플 프로젝트입니다.

이 저장소는 단순 로그인 예제가 아니라, 면접에서 시연 가능한 수준의 실무형 템플릿을 목표로 합니다. Native PHP 8 백엔드, MariaDB, JWT 인증, 파일 업로드, Android WebView 앱을 하나의 구조로 묶어 재사용 가능한 프로젝트 기반을 제공합니다.

## 핵심 기능

- Native PHP 8 기반 REST API
- JWT 인증 기반 로그인, 로그아웃, 회원가입
- 게시판 CRUD
- 이미지 업로드 및 프로필 사진
- Android WebView 연동
- Apache Rewrite 기반 라우팅
- Composer 의존성 관리
- MariaDB 연동
- PDO 기반 SQL Injection 방지
- XSS 방지 및 CORS 처리
- `.env` 기반 환경설정

## 기술 스택

| 영역 | 기술 |
| --- | --- |
| 백엔드 | PHP 8.3, Apache, MariaDB, Composer, JWT |
| 프론트엔드 | HTML5, CSS3, JavaScript ES6, Fetch API |
| 앱 | Android Studio, Kotlin, WebView |
| 개발 환경 | Docker, Git, GitHub, Postman |

## 문서

- [프로젝트 개요](docs/PROJECT_OVERVIEW.md)
- [기능 로드맵](docs/FEATURE_ROADMAP.md)
- [프로젝트 구조](docs/PROJECT_STRUCTURE.md)
- [기술 스택](docs/TECH_STACK.md)
- [설계 방향](docs/DESIGN_DIRECTION.md)
