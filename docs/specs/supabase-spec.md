# Supabase Specification

────────────────────────────────────────────────────────
[목적]
────────────────────────────────────────────────────────

이 문서는 이 시스템에서 Supabase를 사용하는 방식과 규칙을 정의한다.

모든 P2(STRUCTURE), P3(INTEGRATION) 작업은 이 문서를 기준으로 수행한다.

---

────────────────────────────────────────────────────────
[기술 스택 선언]
────────────────────────────────────────────────────────

백엔드: Supabase

구성요소:

- DB: Supabase PostgreSQL
- API: Supabase Client (supabase-js)
- 비즈니스 로직: Supabase Edge Function (Deno)
- DB 셋업: Supabase SQL Editor
- 인증: Supabase Auth
- 접근 제어: RLS (Row Level Security)

---

────────────────────────────────────────────────────────
[DB 셋업 절차]
────────────────────────────────────────────────────────

structure.md 기반으로 SQL DDL을 작성하고 Supabase에 적용한다.

실행 순서:

1. CREATE TABLE 작성
2. FK (Foreign Key) 설정
3. RLS Policy 설정
4. Index 추가 (필요 시)

실행 방법:

- Supabase 대시보드 → SQL Editor → 붙여넣기 → Run

출력 파일:

- docs/current/schema.sql
  (Supabase SQL Editor에서 바로 실행 가능한 형태)

---

────────────────────────────────────────────────────────
[API 연결 판단 기준]
────────────────────────────────────────────────────────

## Supabase Client 직접 사용 (단순 CRUD)

아래 조건을 모두 만족하면 Supabase Client를 직접 사용한다:

- 단순 조회 / 생성 / 수정 / 삭제
- 데이터 가공 없음
- 하나의 테이블 대상
- 검증 로직 없음

예:

```ts
const { data } = await supabase
  .from('items')
  .select('*')
  .eq('user_id', userId)
```

---

## Edge Function 사용 (복잡 로직)

아래 조건 중 하나라도 해당하면 Edge Function을 생성한다:

- 여러 테이블을 복합적으로 처리해야 하는 경우
- 데이터 가공 / 변환 로직이 필요한 경우
- 복잡한 유효성 검증이 필요한 경우
- 외부 API 호출이 필요한 경우
- 민감한 비즈니스 로직을 클라이언트에 노출하면 안 되는 경우

---

────────────────────────────────────────────────────────
[Supabase Client 사용 규칙]
────────────────────────────────────────────────────────

클라이언트 초기화:

```ts
// src/lib/supabase.ts
import { createClient } from '@supabase/supabase-js'

export const supabase = createClient(
  import.meta.env.VITE_SUPABASE_URL,
  import.meta.env.VITE_SUPABASE_ANON_KEY
)
```

환경변수:

- VITE_SUPABASE_URL
- VITE_SUPABASE_ANON_KEY

---

────────────────────────────────────────────────────────
[Edge Function 규칙]
────────────────────────────────────────────────────────

런타임: Deno

폴더 위치:

```
supabase/
  functions/
    {function-name}/
      index.ts
```

함수명 규칙:

- kebab-case 사용
- 동사-명사 형태
- 예: get-user-summary, create-order, process-payment

호출 방식 (프론트):

```ts
const { data, error } = await supabase.functions.invoke('function-name', {
  body: { ... }
})
```

배포:

```bash
supabase functions deploy {function-name}
```

---

────────────────────────────────────────────────────────
[인증 규칙]
────────────────────────────────────────────────────────

Supabase Auth를 사용한다.

로그인 방식은 프로젝트 PRD 기준으로 결정한다.

공통 규칙:

- 로그인 상태는 supabase.auth.getSession()으로 확인
- 로그아웃은 supabase.auth.signOut() 사용
- 인증이 필요한 페이지는 세션 없을 시 /auth로 redirect

---

────────────────────────────────────────────────────────
[RLS (Row Level Security) 규칙]
────────────────────────────────────────────────────────

모든 테이블에 RLS를 활성화한다.

기본 원칙:

- 사용자는 자신의 데이터만 접근 가능
- 공개 데이터는 SELECT만 허용
- 관리자 권한이 필요한 경우 별도 정의

schema.sql에 RLS Policy를 포함한다:

```sql
-- RLS 활성화
ALTER TABLE items ENABLE ROW LEVEL SECURITY;

-- 본인 데이터만 조회
CREATE POLICY "users can view own items"
  ON items FOR SELECT
  USING (auth.uid() = user_id);
```

---

────────────────────────────────────────────────────────
[schema.sql 작성 규칙]
────────────────────────────────────────────────────────

파일 위치: docs/current/schema.sql

포함 순서:

1. 확장(extension) 선언 (필요 시)
2. ENUM 타입 정의 (필요 시)
3. CREATE TABLE (의존성 순서대로)
4. FK 제약 조건
5. RLS 활성화 및 Policy
6. Index

형식 규칙:

- 모든 테이블은 id UUID PRIMARY KEY DEFAULT gen_random_uuid() 사용
- created_at TIMESTAMPTZ DEFAULT now() 포함
- updated_at TIMESTAMPTZ DEFAULT now() 포함 (수정 가능한 테이블)
- 컬럼명은 snake_case

예시:

```sql
CREATE TABLE items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  updated_at TIMESTAMPTZ DEFAULT now()
);

ALTER TABLE items ENABLE ROW LEVEL SECURITY;

CREATE POLICY "users can view own items"
  ON items FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "users can insert own items"
  ON items FOR INSERT
  WITH CHECK (auth.uid() = user_id);
```

---

────────────────────────────────────────────────────────
[절대 금지]
────────────────────────────────────────────────────────

- P1(FRONT_MOCK) 단계에서 Supabase 코드 작성
- P2(STRUCTURE) 단계에서 실제 연결 코드 작성
- Edge Function을 단순 CRUD에 사용 (오버엔지니어링)
- fetch/axios로 Supabase API를 직접 호출
- 환경변수를 코드에 하드코딩

---

────────────────────────────────────────────────────────
[AI 수행 규칙]
────────────────────────────────────────────────────────

- 모든 P3 작업은 이 문서를 참조한다
- API 연결 방식(Client vs Edge Function) 판단 시 이 문서 기준을 따른다
- schema.sql은 Supabase SQL Editor에서 바로 실행 가능해야 한다
- RLS가 없는 테이블을 만들지 않는다
