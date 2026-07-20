# 문서 목록

## 1. 개요

본 문서는 Native PHP Hybrid App 프로젝트의 문서 목록과 각 문서의 역할을 정리합니다.

## 2. 핵심 문서

| 문서 | 역할 |
| --- | --- |
| `PROJECT_OVERVIEW.md` | 프로젝트 목적과 주요 특징 |
| `FEATURE_ROADMAP.md` | 기능 개발 단계와 v1.0 완료 기준 |
| `PROJECT_STRUCTURE.md` | 디렉터리 구조와 역할 |
| `TECH_STACK.md` | 사용 기술과 선택 이유 |
| `DESIGN_DIRECTION.md` | 설계 방향과 구현 원칙 |
| `WORK_PLAN.md` | 단계별 작업 계획과 산출물 |

## 3. 구현 기준 문서

| 문서 | 역할 |
| --- | --- |
| `API_SPEC.md` | API URL, Method, 요청, 응답, 오류 형식 |
| `DB_SCHEMA.md` | MariaDB 테이블 구조와 컬럼 정의 |
| `SETUP_GUIDE.md` | 로컬 설치, 실행, 환경 변수 설정 |
| `TEST_CHECKLIST.md` | 기능별 수동 검증 체크리스트 |
| `DEMO_SCENARIO.md` | 면접 및 포트폴리오 시연 흐름 |

## 4. 추천 읽기 순서

처음 프로젝트를 이해할 때:

1. `PROJECT_OVERVIEW.md`
2. `TECH_STACK.md`
3. `PROJECT_STRUCTURE.md`
4. `FEATURE_ROADMAP.md`
5. `WORK_PLAN.md`

구현을 시작할 때:

1. `DB_SCHEMA.md`
2. `API_SPEC.md`
3. `SETUP_GUIDE.md`
4. `TEST_CHECKLIST.md`

면접 또는 발표를 준비할 때:

1. `PROJECT_OVERVIEW.md`
2. `DESIGN_DIRECTION.md`
3. `DEMO_SCENARIO.md`
4. `API_SPEC.md`
5. `DB_SCHEMA.md`

## 5. 문서 관리 기준

- 기능을 추가하거나 변경하면 `API_SPEC.md`와 `TEST_CHECKLIST.md`를 함께 갱신합니다.
- 테이블 구조가 변경되면 `DB_SCHEMA.md`를 먼저 갱신한 뒤 SQL 파일을 맞춥니다.
- 실행 방법이 바뀌면 `SETUP_GUIDE.md`를 갱신합니다.
- 데모 흐름이 바뀌면 `DEMO_SCENARIO.md`를 갱신합니다.
- 작업 단계나 우선순위가 바뀌면 `WORK_PLAN.md`를 갱신합니다.

## 6. 추가 예정 산출물

문서 외에 다음 파일을 추가하면 프로젝트 재현성이 더 좋아집니다.

- `database/schema.sql`
- `database/seed.sql`
- `postman/NativePHPHybrid.postman_collection.json`
- `backend/.env.example`
- `docker-compose.yml`
