# 개발 가이드 (비개발자용)

> 이 가이드는 개발을 몰라도 AI 에이전트와 함께 서비스를 만들 수 있도록  
> 순서대로 무엇을 해야 하는지 안내합니다.

> **팀으로 작업하는 경우** → [`TEAM_GUIDE.md`](./TEAM_GUIDE.md) 를 먼저 읽으세요.

---

## 이 가이드 읽는 법

| 표시 | 의미 |
|---|---|
| 🧑 **직접 할 일** | 본인이 직접 수행하는 작업 |
| 🤖 **에이전트에게 시킬 일** | Cursor AI 에이전트에게 지시하는 작업 |
| 📋 **에이전트 지시 프롬프트** | 이 내용을 복사해서 에이전트 채팅창에 그대로 붙여넣으세요 |
| ☑ **체크박스** | 완료하면 체크하며 진행 |

---

## 전체 흐름 한눈에 보기

```
[준비] PRD 작성
  ↓
[P1] 화면(UI/UX) 목업 만들기  ←──────────────┐
  ↓                                           │
[P2] 데이터 구조 설계 + SQL 작성              │
  ↓                                           │
[P3] Supabase 연결 + 실제 서비스              │
  ↓                                           │
완료 또는 문제 발견 시 P1으로 돌아가 개선 ───┘
```

---

---

# 0단계 — 시작 전 준비

---

## Step 0-1. PRD(기획서) 작성

🧑 **직접 할 일**

- [ ] `docs/current/` 폴더를 만든다
- [ ] `docs/current/prd.md` 파일을 만들고 아래 내용을 채운다

**prd.md에 들어가야 할 내용:**

```
1. 서비스 이름
2. 이 서비스가 해결하는 문제 (한 문장)
3. 주요 사용자는 누구인가
4. 핵심 기능 목록 (꼭 있어야 하는 것)
5. 제외할 기능 (이번에 안 해도 되는 것)
6. 사용자가 서비스를 어떻게 사용하는지 (시나리오)
```

> **팁:** 구체적일수록 좋습니다. "사용자가 로그인한다" 보다  
> "이메일과 비밀번호로 로그인하고, 로그인 후 대시보드로 이동한다"처럼 쓰세요.

---

## Step 0-2. 에이전트에게 프로젝트 초기 셋업 요청

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
docs/current/ 폴더 안에 아래 파일을 생성해줘:

- VERSION.md (내용: Phase: P1)

그리고 git 브랜치가 없다면 아래 브랜치를 생성해줘:
- p1 (현재 작업 브랜치)
- p2
- p3

현재 브랜치는 p1으로 유지해줘.
```

- [ ] 완료 확인

---

---

# P1 단계 — 화면(UI/UX) 목업 만들기

> **목표:** 실제 서비스처럼 동작하는 화면을 먼저 만든다.  
> 데이터는 가짜(더미)를 사용하고, 서버 연결은 나중에 한다.

---

## Step 1-1. 프론트 프롬프트 문서 세트 생성 (T1)

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T1 — CREATE_FRONT_PROMPT_SET 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/create-front-prompt-set.md
- docs/current/prd.md
- docs/specs/front-mock-prompt-spec/ (전체)

PRD를 기반으로 docs/current/front-prompt/ 폴더에
아래 문서 세트를 생성해줘:
- PROJECT_META.md
- PRD_UI_SUMMARY.md
- GLOBAL_RULES.md
- ROUTER_MAP.md
- STORYBOARD.md
- ROUTES/ 폴더 안에 각 페이지별 md 파일
```

- [ ] `docs/current/front-prompt/` 폴더와 파일들이 생성되었는지 확인
- [ ] ROUTER_MAP.md를 열어서 페이지 구조가 맞는지 확인
- [ ] 빠진 화면이나 잘못된 내용이 있으면 직접 수정하거나 에이전트에게 수정 요청

---

## Step 1-2. 프론트 목업 코드 생성 (T2)

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T2 — UPDATE_FRONT_BY_ROUTE 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/update-front-by-route.md
- docs/current/front-prompt/ (전체)

React + Vite 프로젝트를 생성하고,
front-prompt 문서 세트를 기반으로
모든 페이지의 UI 목업을 구현해줘.

조건:
- 백엔드 연결 없음
- 더미 JSON 데이터 사용
- 모든 페이지 라우팅 정상 동작
- 각 페이지는 loading / success / empty / error 상태 모두 구현
```

- [ ] 프로젝트가 실행되는지 확인 (`npm run dev`)
- [ ] 모든 페이지가 브라우저에서 열리는지 확인
- [ ] 버튼 클릭, 화면 이동 등 기본 동작 확인

---

## Step 1-3. 목업 검토 및 수정 반복

🧑 **직접 할 일**

- [ ] 브라우저에서 직접 서비스를 사용해보며 아래를 확인한다

**확인 항목:**
```
□ 모든 페이지가 열리는가?
□ 버튼을 누르면 반응이 있는가?
□ 사용자 흐름이 자연스러운가?
□ 빠진 기능이 없는가?
□ 화면이 이상하게 보이는 곳이 없는가?
```

**수정이 필요하면** → 에이전트에게 수정 요청

📋 **에이전트 지시 프롬프트 (수정 요청 예시)**

```
T2 — UPDATE_FRONT_BY_ROUTE 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/update-front-by-route.md
- docs/current/front-prompt/GLOBAL_RULES.md
- docs/current/front-prompt/ROUTER_MAP.md
- docs/current/front-prompt/ROUTES/{수정할 페이지}.md

수정 요청:
- [수정할 내용을 여기에 구체적으로 적어줘]
  예: "대시보드 페이지에서 검색창이 위에 있어야 해"
  예: "로그인 버튼을 누르면 바로 대시보드로 이동해야 해"
```

- [ ] 수정 완료 후 다시 브라우저에서 확인

> **언제까지 반복?** 서비스 화면이 마음에 들고, 사용 흐름이 자연스러울 때까지.  
> 화면이 어느 정도 완성되었다고 느껴지면 다음 단계로.

---

## Step 1-4. 프롬프트 문서 최신화 (T3)

🤖 **에이전트에게 시킬 일**

> 화면을 수정하면서 실제 코드와 문서가 달라질 수 있습니다.  
> 에이전트가 문서를 현재 코드 기준으로 맞게 업데이트합니다.

📋 **에이전트 지시 프롬프트**

```
T3 — UPGRADE_FRONT_PROMPT_SET 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/upgrade-prd.md
- docs/current/front-prompt/ (전체)
- 현재 프론트 코드

현재 구현된 프론트 코드를 기준으로
docs/current/front-prompt/ 문서 세트를 최신 상태로 업데이트해줘.
ROUTES md, STORYBOARD, ROUTER_MAP을 실제 코드와 일치시켜줘.
```

- [ ] 완료 확인

---

## Step 1-5. PRD 최신화 (T4)

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T4 — UPGRADE_PRD 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/upgrade-prd.md
- docs/current/front-prompt/ (전체)

front-prompt 문서 기준으로
docs/current/prd.md를 현재 상태에 맞게 업데이트해줘.
```

- [ ] 완료 확인

---

## Step 1-6. P1 완료 여부 판단 및 P2 전환 (T7)

🤖 **에이전트에게 시킬 일** → 🧑 **최종 결정은 직접**

📋 **에이전트 지시 프롬프트**

```
T7 — TRANSITION_1_TO_2 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/transition/1-to-2.md
- docs/current/front-prompt/ (전체)
- docs/current/prd.md
- docs/specs/front-mock-prompt-spec/GLOBAL_QA.md

현재 프론트 목업이 P2로 전환 가능한 수준인지 QA 기준으로 검증하고,
전환 가능 여부와 이유를 알려줘.

전환 가능하다면 브랜치 전환(p1 → p2)도 수행해줘.
```

**에이전트가 "전환 불가"라고 하면:**
- [ ] 이유를 확인하고 Step 1-3으로 돌아가 수정

**에이전트가 "전환 가능"이라고 하면:**
- [ ] 내용을 직접 읽어보고 동의하는지 판단
- [ ] 동의하면 에이전트에게 "전환 진행해줘"라고 지시
- [ ] 브랜치가 p2로 바뀌었는지 확인

---

---

# P2 단계 — 데이터 구조 설계

> **목표:** 화면에서 필요한 데이터를 분석해서 데이터베이스 구조를 설계한다.  
> 실제 코딩은 에이전트가 하고, 결과물을 검토하는 것이 주된 역할.

---

## Step 2-1. 데이터 구조 설계 + SQL 생성 (T5)

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T5 — CREATE_STRUCTURE 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/create-structure.md
- docs/current/front-prompt/ (전체)
- docs/current/prd.md
- docs/specs/supabase-spec.md

프론트 화면을 기반으로 아래 두 가지를 생성해줘:

1. docs/current/structure.md
   - IA (정보 구조)
   - Entity 정의
   - ERD (관계)
   - Table Schema

2. docs/current/schema.sql
   - Supabase SQL Editor에서 바로 실행 가능한 SQL
   - CREATE TABLE, FK, RLS Policy 포함
```

- [ ] `docs/current/structure.md` 생성 확인
- [ ] `docs/current/schema.sql` 생성 확인

---

## Step 2-2. 구조 검토

🧑 **직접 할 일**

`docs/current/structure.md`를 열어서 아래를 확인한다.

```
□ 화면에 보이는 모든 데이터가 테이블로 정의되어 있는가?
□ 빠진 항목이 없는가?
□ 이해가 안 되는 부분이 있으면 에이전트에게 설명을 요청
```

**수정이 필요하면** 에이전트에게 요청:

📋 **에이전트 지시 프롬프트 (수정 요청 예시)**

```
structure.md와 schema.sql을 수정해줘.

수정 내용:
- [수정할 내용을 구체적으로 적어줘]
  예: "주문 테이블에 배송지 주소 필드가 빠졌어"
  예: "상품과 카테고리의 관계가 잘못된 것 같아"
```

- [ ] 검토 완료

---

## Step 2-3. P2 완료 여부 판단 및 P3 전환 (T8)

🤖 **에이전트에게 시킬 일** → 🧑 **최종 결정은 직접**

📋 **에이전트 지시 프롬프트**

```
T8 — TRANSITION_2_TO_3 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/transition/2-to-3.md
- docs/current/structure.md
- docs/current/prd.md

현재 데이터 구조가 P3로 전환 가능한 수준인지 검증하고,
전환 가능 여부와 이유를 알려줘.

전환 가능하다면 브랜치 전환(p2 → p3)도 수행해줘.
```

**에이전트가 "전환 불가"라고 하면:**
- [ ] 이유 확인 후 Step 2-1로 돌아가 수정

**에이전트가 "전환 가능"이라고 하면:**
- [ ] 내용 확인 후 동의하면 "전환 진행해줘"
- [ ] 브랜치가 p3로 바뀌었는지 확인

---

---

# P3 단계 — Supabase 연결 (실제 서비스)

> **목표:** 만들어둔 화면과 데이터베이스를 실제로 연결한다.

---

## Step 3-1. Supabase 프로젝트 생성

🧑 **직접 할 일**

- [ ] [supabase.com](https://supabase.com) 접속 후 로그인
- [ ] **New Project** 클릭
- [ ] 프로젝트 이름 입력, 데이터베이스 비밀번호 설정
- [ ] 리전(Region) 선택: **Northeast Asia (Seoul)** 권장
- [ ] 프로젝트 생성 완료까지 기다림 (약 1~2분)

---

## Step 3-2. 환경변수 설정

🧑 **직접 할 일**

- [ ] Supabase 대시보드 → **Settings** → **API** 클릭
- [ ] 아래 두 값을 복사해둔다
  - `Project URL`
  - `anon public` (API key)
- [ ] 프로젝트 루트에 `.env` 파일을 만들고 아래 형식으로 붙여넣는다

```
VITE_SUPABASE_URL=여기에_Project_URL_붙여넣기
VITE_SUPABASE_ANON_KEY=여기에_anon_key_붙여넣기
```

- [ ] `.env` 파일이 `.gitignore`에 포함되어 있는지 확인 (에이전트에게 확인 요청 가능)

---

## Step 3-3. 데이터베이스 테이블 생성

🧑 **직접 할 일**

- [ ] Supabase 대시보드 → **SQL Editor** 클릭
- [ ] **New query** 클릭
- [ ] `docs/current/schema.sql` 파일의 내용 전체를 복사해서 붙여넣기
- [ ] **Run** 버튼 클릭
- [ ] 오류 없이 완료되면 **Table Editor**에서 테이블이 생성되었는지 확인

**오류가 발생하면** 에이전트에게 오류 메시지를 그대로 전달:

📋 **에이전트 지시 프롬프트 (SQL 오류 시)**

```
Supabase SQL Editor에서 schema.sql을 실행했더니 아래 오류가 났어.

오류 메시지:
[오류 내용을 여기에 붙여넣기]

schema.sql을 수정해줘.
```

- [ ] 모든 테이블 정상 생성 확인

---

## Step 3-4. Supabase 연결 코드 작성 (T6)

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T6 — UPDATE_INTEGRATION 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/update-integration.md
- docs/current/structure.md
- docs/current/schema.sql
- docs/specs/supabase-spec.md

현재 프론트 코드의 더미 JSON 데이터를
실제 Supabase 데이터로 교체하는 작업을 수행해줘.

- 단순 CRUD는 Supabase Client로 직접 연결
- 복잡한 로직이 필요한 경우 Edge Function 생성
- 인증이 필요한 기능에 Supabase Auth 연결
- 완료 후 docs/current/integration.md 업데이트
```

- [ ] 실행 후 실제 데이터가 화면에 표시되는지 확인
- [ ] 데이터 생성/수정/삭제가 실제로 DB에 반영되는지 확인

---

## Step 3-5. 실제 서비스 테스트

🧑 **직접 할 일**

- [ ] 처음부터 끝까지 실제 사용자처럼 서비스를 사용해본다
- [ ] 아래 항목을 확인한다

```
□ 회원가입 / 로그인이 되는가?
□ 데이터가 실제로 저장되는가?
□ 저장한 데이터가 다시 불러와지는가?
□ 수정/삭제가 정상 동작하는가?
□ 다른 사용자의 데이터가 보이지 않는가? (권한 확인)
□ 오류 발생 시 사용자에게 안내가 나오는가?
```

---

## Step 3-6. 문제 없으면 버전 확정

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
P3 작업이 완료되었어. 버전을 확정해줘.

아래 작업을 순서대로 수행해줘:
1. git tag v1-p3-done
2. main 브랜치에 merge
3. git tag v1
4. docs/current/ 내용을 docs/versions/v1/ 으로 복사
```

- [ ] 버전 1 완료 🎉

---

---

# 개선 사이클 — 문제 발견 시

> 실제 서비스를 사용하다가 UI/UX 문제가 생기면  
> P1으로 돌아가 수정한다.

---

## Step 4-1. 문제 분석 및 P1 복귀 (T9)

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T9 — TRANSITION_3_TO_1 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/transition/3-to-1.md
- docs/current/integration.md
- docs/current/prd.md

발견된 문제:
[여기에 발견한 문제를 구체적으로 적어줘]
예: "대시보드에서 필터를 쓰기가 너무 불편해"
예: "주문 완료 후 다음에 뭘 해야 할지 모르겠어"

문제를 분석하고 수정 대상 화면을 정의한 뒤,
p1 브랜치로 전환해줘.
```

- [ ] 전환 완료 후 **P1 Step 1-3**부터 다시 진행

---

---

# 자주 쓰는 에이전트 지시 모음

## 현재 상태 확인

```
현재 Phase와 진행 상태를 알려줘.
docs/current/VERSION.md와 현재 브랜치를 확인해줘.
```

## 특정 페이지만 수정

```
T2 — UPDATE_FRONT_BY_ROUTE 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/current/front-prompt/GLOBAL_RULES.md
- docs/current/front-prompt/ROUTER_MAP.md
- docs/current/front-prompt/ROUTES/[페이지명].md

수정 내용: [구체적으로 적기]
```

## 특정 기능만 Supabase 연결

```
T6 — UPDATE_INTEGRATION 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/current/structure.md
- docs/specs/supabase-spec.md

[특정 기능 이름]을 Supabase에 연결해줘.
```

---

---

# 주의사항

- **P1에서는 절대 백엔드 연결 코드를 만들지 않는다** — 더미 데이터만 사용
- **P2에서는 절대 실제 연결 코드를 만들지 않는다** — 구조 설계만
- **화면이 완성되지 않으면 P2로 넘어가지 않는다** — 나중에 더 어려워짐
- **DB 구조가 확정되지 않으면 P3로 넘어가지 않는다** — 나중에 더 어려워짐
- **문제가 생기면 에이전트에게 오류 메시지를 그대로 복사해서 전달한다**
