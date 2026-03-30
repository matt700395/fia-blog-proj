# Task Definition

────────────────────────────────────────────────────────
[목적]
────────────────────────────────────────────────────────

이 문서는 시스템에서 사용하는 Task Code(T1~T9)의 의미와 수행 범위를 정의한다.

모든 AI 작업은 반드시 Task Code를 기준으로 수행된다.

---

────────────────────────────────────────────────────────
[Task Code 구조]
────────────────────────────────────────────────────────

각 Task는 다음 요소로 정의된다:

- 코드 (T1 ~ T9)
- 이름
- 소속 Phase
- 목적
- 입력
- 출력
- 수행 범위
- 금지사항

---

────────────────────────────────────────────────────────
[T1 — CREATE_FRONT_PROMPT_SET]
────────────────────────────────────────────────────────

Phase: P1 — FRONT_MOCK

목적:
PRD를 기반으로 프론트엔드 목업 생성을 위한 프롬프트 문서 세트를 생성한다.

입력:
- docs/current/prd.md
- docs/specs/front-mock-prompt-spec/

출력:
- docs/current/front-prompt/

수행 범위:
- PROJECT_META 생성
- PRD_UI_SUMMARY 작성
- GLOBAL_RULES 정의
- ROUTER_MAP 작성
- STORYBOARD 작성
- ROUTES md 생성

금지:
- React 코드 생성
- DB 설계
- API 설계

---

────────────────────────────────────────────────────────
[T2 — UPDATE_FRONT_BY_ROUTE]
────────────────────────────────────────────────────────

Phase: P1 — FRONT_MOCK

목적:
특정 라우터의 UI/UX를 수정하거나 구현한다.

입력:
- docs/current/front-prompt/
- 기존 프론트 코드

출력:
- 수정된 프론트 코드
- 필요 시 ROUTES md 수정

수행 범위:
- UI 변경
- UX 흐름 개선
- 상태 전이 수정

금지:
- API 호출 추가
- DB 연결

---

────────────────────────────────────────────────────────
[T3 — UPGRADE_FRONT_PROMPT_SET]
────────────────────────────────────────────────────────

Phase: P1 — FRONT_MOCK

목적:
수정된 프론트 결과를 반영하여 front-prompt 문서 세트를 업데이트한다.

입력:
- 현재 프론트 코드
- docs/current/front-prompt/

출력:
- 업데이트된 front-prompt/

수행 범위:
- ROUTES md 업데이트
- STORYBOARD 업데이트
- ROUTER_MAP 수정 (필요 시)
- GLOBAL_RULES 수정 (필요 시)

금지:
- structure.md 수정
- integration.md 수정

---

────────────────────────────────────────────────────────
[T4 — UPGRADE_PRD]
────────────────────────────────────────────────────────

Phase: P1 — FRONT_MOCK

목적:
front-prompt 기준으로 PRD를 최신 상태로 업데이트한다.

입력:
- docs/current/front-prompt/

출력:
- docs/current/prd.md

수행 범위:
- 기능 정의 업데이트
- 사용자 흐름 반영
- 변경사항 반영

금지:
- DB/API 설계 포함

---

────────────────────────────────────────────────────────
[T5 — CREATE_STRUCTURE]
────────────────────────────────────────────────────────

Phase: P2 — STRUCTURE

목적:
프론트 설계를 기반으로 데이터 구조를 정의하고 Supabase용 SQL을 작성한다.

입력:
- docs/current/front-prompt/
- docs/current/prd.md
- docs/specs/supabase-spec.md

출력:
- docs/current/structure.md
- docs/current/schema.sql (Supabase SQL Editor 실행용)

수행 범위:
- IA 추출
- entity 정의
- ERD 구성
- 테이블 정의
- schema.sql 작성 (CREATE TABLE, FK, RLS, Index)

금지:
- UI 변경
- API 설계
- 실제 Supabase 연결 코드 작성

---

────────────────────────────────────────────────────────
[T6 — UPDATE_INTEGRATION]
────────────────────────────────────────────────────────

Phase: P3 — INTEGRATION

목적:
Supabase를 기반으로 API 연결 및 실제 데이터 연동을 구현하거나 수정한다.

입력:
- docs/current/structure.md
- docs/current/schema.sql
- docs/current/integration.md (기존 존재 시)
- docs/specs/supabase-spec.md

출력:
- docs/current/integration.md 업데이트
- Supabase Client 연결 코드
- Edge Function 코드 (필요 시)

수행 범위:
- Supabase Client 직접 CRUD 연결
- Edge Function 생성 및 배포 설정 (복잡 로직)
- Supabase Auth 연결
- RLS 정책 확인

금지:
- UI 변경
- DB 구조 변경 (schema.sql은 T5에서만 수정)
- fetch/axios로 Supabase 직접 호출

---

────────────────────────────────────────────────────────
[T7 — TRANSITION_1_TO_2]
────────────────────────────────────────────────────────

Phase: P2 — STRUCTURE

목적:
프론트 설계 단계에서 구조 설계 단계로 전환한다.

입력:
- docs/current/front-prompt/

출력:
- docs/current/structure.md

브랜치 컨텍스트:
- 작업 브랜치: p1
- 전환 후 브랜치: p2
- 전환 시 태그: v{N}-p1-done

---

────────────────────────────────────────────────────────
[T8 — TRANSITION_2_TO_3]
────────────────────────────────────────────────────────

Phase: P3 — INTEGRATION

목적:
구조 설계 단계에서 실제 연결 단계로 전환한다.

입력:
- docs/current/structure.md

출력:
- docs/current/integration.md

브랜치 컨텍스트:
- 작업 브랜치: p2
- 전환 후 브랜치: p3
- 전환 시 태그: v{N}-p2-done

---

────────────────────────────────────────────────────────
[T9 — TRANSITION_3_TO_1]
────────────────────────────────────────────────────────

Phase: P1 — FRONT_MOCK

목적:
연결 과정에서 발견된 문제를 프론트 설계 단계로 되돌린다.

입력:
- docs/current/integration.md
- docs/current/prd.md

출력:
- 수정 대상 정의
- docs/current/change-log.md

수행 범위:
- 문제 UI 식별
- 수정 대상 route 정의

금지:
- DB 구조 수정
- API 수정

브랜치 컨텍스트:
- 작업 브랜치: p3
- 전환 후 브랜치: p1
- 전환 시 태그: v{N}-p3-snapshot

---

────────────────────────────────────────────────────────
[절대 규칙]
────────────────────────────────────────────────────────

모든 작업은 반드시 아래 순서를 따른다:

1. Task Code 확인
2. Task Definition 참조
3. 수행 범위 내에서만 작업

Task 정의를 벗어난 작업은 금지한다.