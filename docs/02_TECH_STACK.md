# 기술 스택

## 백엔드

| 기술 | 사용 목적 |
| --- | --- |
| PHP 8.3 | Native PHP 기반 REST API 구현 |
| Apache | 웹 서버 및 Rewrite 처리 |
| MariaDB | 관계형 데이터 저장소 |
| Composer | PHP 의존성 관리 및 오토로딩 |
| JWT | 토큰 기반 인증 |
| PDO | 데이터베이스 접근 및 Prepared Statement 처리 |

## 프론트엔드

| 기술 | 사용 목적 |
| --- | --- |
| HTML5 | WebView에서 표시할 화면 구조 |
| CSS3 | 화면 스타일링 |
| JavaScript ES6 | 클라이언트 로직 구현 |
| Fetch API | REST API 호출 |

## 앱

| 기술 | 사용 목적 |
| --- | --- |
| Android Studio | Android 앱 개발 환경 |
| Kotlin | Android 앱 구현 언어 |
| WebView | 웹 화면을 네이티브 앱 내부에서 표시 |

## 개발 환경

| 기술 | 사용 목적 |
| --- | --- |
| Docker | 로컬 개발 환경 표준화 |
| Git | 버전 관리 |
| GitHub | 원격 저장소 및 협업 |
| Postman | REST API 테스트 |

## 보안 및 운영 고려사항

- SQL Injection 방지를 위해 PDO Prepared Statement를 사용합니다.
- XSS 방지를 위해 출력 시 이스케이프 처리를 적용합니다.
- JWT는 Authorization Header를 통해 전달합니다.
- CORS 정책은 허용 도메인을 기준으로 명확히 관리합니다.
- 민감한 설정은 `.env` 파일로 분리하고 Git에 커밋하지 않습니다.
