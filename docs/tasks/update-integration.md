# Task: UPDATE_INTEGRATION

Task Code: T6  
Phase: P3 — INTEGRATION  

────────────────────────────────────────────────────────
[목적]
────────────────────────────────────────────────────────

구조 설계(structure.md)를 기반으로  
API 및 데이터 연결을 구현한다.

이 작업은:

"DB 구조 → 실제 동작 시스템 연결"

단계이다.

---

────────────────────────────────────────────────────────
[입력]
────────────────────────────────────────────────────────

필수 입력:

- docs/system.md
- docs/task-definition.md
- docs/current/structure.md
- docs/current/schema.sql
- docs/specs/supabase-spec.md
- docs/current/integration.md (기존 존재 시)
- docs/tasks/update-integration.md

선택 입력:

- docs/current/front-prompt/
- docs/current/prd.md

---

────────────────────────────────────────────────────────
[출력]
────────────────────────────────────────────────────────

- docs/current/integration.md (업데이트)
- Supabase Client 연결 코드 (src/ 내 해당 파일)
- Edge Function 코드 (supabase/functions/{name}/index.ts) — 필요 시
- API 명세 (integration.md 내 포함)

---

────────────────────────────────────────────────────────
[작업 절차]
────────────────────────────────────────────────────────

1. 연결 방식 판단 (supabase-spec.md 기준)

   각 기능별로 아래 중 선택:
   - Supabase Client 직접 CRUD (단순 조회/생성/수정/삭제)
   - Edge Function (데이터 가공 / 복잡 로직 / 외부 API 필요)

2. Supabase Client 연결 코드 작성

   - src/lib/supabase.ts 초기화 확인
   - 각 페이지/훅에서 supabase client 호출
   - 에러 처리 포함

3. Edge Function 생성 (필요한 경우)

   - 판단 기준 재확인 (supabase-spec.md)
   - supabase/functions/{function-name}/index.ts 생성
   - 비즈니스 로직 작성 (Deno)
   - 프론트에서 supabase.functions.invoke() 로 호출

4. 인증 처리 (필요 시)

   - Supabase Auth 연결
   - 세션 확인 로직 추가
   - 미인증 시 /auth redirect 처리

5. RLS 정책 확인

   - schema.sql의 RLS Policy와 실제 접근 패턴 일치 여부 확인
   - 권한 문제 발생 시 schema.sql 수정 요청 (T5 재수행)

6. integration.md 업데이트

   포함:
   - 연결된 기능 목록
   - 연결 방식 (Client / Edge Function)
   - 인증 처리 방식
   - 미완료 항목

---

────────────────────────────────────────────────────────
[중요 규칙]
────────────────────────────────────────────────────────

- 반드시 structure.md 기준으로 설계한다
- 프론트 요구사항을 충족해야 한다
- API는 최소한으로 설계한다

---

────────────────────────────────────────────────────────
[설계 기준]
────────────────────────────────────────────────────────

좋은 API:

- 명확한 목적
- 단순한 구조
- 재사용 가능

나쁜 API:

- 불필요한 endpoint
- 과도한 분리
- 프론트 요구 미반영

---

────────────────────────────────────────────────────────
[절대 금지]
────────────────────────────────────────────────────────

- DB 구조 변경 (T5에서만 수행)
- schema.sql 수정 (T5에서만 수행)
- UI 구조 변경 (T2에서만 수행)
- 프론트 흐름 변경
- fetch/axios로 Supabase API 직접 호출
- 환경변수를 코드에 하드코딩
- 단순 CRUD에 Edge Function 사용

---

────────────────────────────────────────────────────────
[완료 기준]
────────────────────────────────────────────────────────

□ 모든 주요 기능 Supabase 연결 완료  
□ 연결 방식 (Client / Edge Function) 판단 기준 준수  
□ 프론트와 데이터 흐름 정상 동작  
□ 에러 처리 존재  
□ 인증이 필요한 기능에 Auth 연결됨  
□ RLS 정책과 실제 접근 패턴 일치  

---

────────────────────────────────────────────────────────
[출력 형식 — 강제]
────────────────────────────────────────────────────────

[CURRENT STATE]

Phase: P3 — INTEGRATION  
Task: T6 — UPDATE_INTEGRATION  

Context:
- 구조 기반 API 및 연결 구현

Next Action:
- API spec 작성
- edge function 설계
- 프론트 연결

---

그 다음 작업 수행