# 팀 개발 가이드

> 이 가이드는 **팀으로 작업할 때** 읽는 문서입니다.  
> 1인 작업 기본 흐름은 `README.md`를 먼저 읽으세요.

---

## 이 가이드 읽는 법

| 표시 | 의미 |
|---|---|
| 👑 **리드가 할 일** | 팀 리드(또는 프로젝트 관리자)가 수행 |
| 🧑 **직접 할 일** | 팀원 본인이 직접 수행 |
| 🤖 **에이전트에게 시킬 일** | Cursor AI 에이전트에게 지시 |
| 📋 **에이전트 지시 프롬프트** | 복사해서 에이전트 채팅창에 붙여넣기 |

---

## 전체 팀 작업 흐름

```
[리드] PRD 작성 + 라우터 배정
  ↓
[팀원] 본인 담당 라우터 개발 브랜치 생성
  ↓
[팀원] AI 에이전트로 프론트 목업 개발 + QA
  ↓
[팀원] 문서 업데이트 + _status.md 상태 변경
  ↓
[팀원] PR 제출
  ↓
[리드] 코드 리뷰 + merge
  ↓
모든 라우터 완료 시 → P2 전환
```

---

---

# 0단계 — 팀 프로젝트 셋업

---

## Step 0-1. 브랜치 구조 초기화

👑 **리드가 할 일**

README.md의 Step 0-2를 먼저 완료한 후, 추가로 팀 브랜치 규칙을 설정한다.

**브랜치 구조:**

```
main                      ← 버전 확정 시에만 merge
p1                        ← P1 팀 공용 베이스 브랜치
  └── p1/route/home       ← 홍길동 담당 라우터
  └── p1/route/dashboard  ← 김철수 담당 라우터
  └── p1/route/mypage     ← 이영희 담당 라우터
p2                        ← P2 팀 공용 베이스 브랜치
p3                        ← P3 팀 공용 베이스 브랜치
```

**규칙:**
- 팀원은 항상 `p1/route/{route_key}` 브랜치에서 작업한다
- PR은 `p1/route/{route_key}` → `p1` 으로 보낸다
- `p1`, `p2`, `p3`에 직접 push하지 않는다

---

## Step 0-2. 라우터 배정 + 상태 파일 초기화

👑 **리드가 할 일**

T1 작업으로 `docs/current/front-prompt/ROUTES/` 문서 세트가 생성된 후,  
아래 파일을 생성하여 라우터를 팀원에게 배정한다.

**생성 파일:** `docs/current/front-prompt/ROUTES/_status.md`

```markdown
# 라우터 개발 상태

| route_key | path | 담당자 | 상태 | 브랜치 | PR |
|---|---|---|---|---|---|
| home | / | 홍길동 | ⏳ 미착수 | p1/route/home | |
| dashboard | /dashboard | 김철수 | ⏳ 미착수 | p1/route/dashboard | |
| mypage | /mypage | 이영희 | ⏳ 미착수 | p1/route/mypage | |
```

**상태 값:**

| 상태 | 의미 |
|---|---|
| ⏳ 미착수 | 아직 시작 안 함 |
| 🔄 개발 중 | 현재 작업 중 |
| 🔍 자체 QA 중 | 개발 완료, QA 진행 중 |
| 📬 리뷰 요청 | PR 제출 완료 |
| ✅ 완료 | merge 완료 |

---

---

# P1 단계 — 팀원별 라우터 개발

---

## Step 1-1. 작업 브랜치 생성

🧑 **직접 할 일** (담당 라우터 개발 시작 전)

```bash
# p1 베이스에서 작업 브랜치 생성
git checkout p1
git pull origin p1
git checkout -b p1/route/{본인_route_key}

# 예시
git checkout -b p1/route/dashboard
```

그리고 `_status.md`에서 본인 라우터 상태를 변경한다:

```
⏳ 미착수  →  🔄 개발 중
```

---

## Step 1-2. 담당 라우터 프론트 목업 개발

🤖 **에이전트에게 시킬 일**

📋 **에이전트 지시 프롬프트**

```
T2 — UPDATE_FRONT_BY_ROUTE 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/tasks/update-front-by-route.md
- docs/current/front-prompt/GLOBAL_RULES.md
- docs/current/front-prompt/ROUTER_MAP.md
- docs/current/front-prompt/STORYBOARD.md
- docs/current/front-prompt/ROUTES/{담당_route_key}.md

담당 라우터: {route_key} ({path})

위 문서를 기반으로 해당 라우터의 프론트 목업을 구현해줘.

조건:
- 백엔드 연결 없음
- 더미 JSON 데이터 사용
- public/data/routes/{route_key}.json 기반 렌더링
- loading / success / empty / error 상태 모두 구현
- GLOBAL_RULES 규칙 준수
```

---

## Step 1-3. 자체 QA

🧑 **직접 할 일**

브라우저에서 본인 담당 페이지를 직접 확인한다.

```
□ 페이지가 정상적으로 열리는가?
□ loading 상태가 표시되는가?
□ 데이터가 정상적으로 렌더링되는가?
□ 데이터가 없을 때 empty 상태가 표시되는가?
□ 에러 상태와 재시도 버튼이 있는가?
□ 버튼 클릭 시 즉각적인 반응이 있는가?
□ 다른 페이지로 이동하는 링크/버튼이 정상 작동하는가?
□ GLOBAL_RULES의 레이아웃 구조를 따르는가?
□ 기존 다른 페이지가 내 작업으로 인해 깨지지 않았는가?
```

수정이 필요하면 Step 1-2를 반복한다.

`_status.md` 상태 변경:

```
🔄 개발 중  →  🔍 자체 QA 중
```

---

## Step 1-4. 라우터 문서 업데이트

🤖 **에이전트에게 시킬 일**

QA 완료 후 실제 구현 내용을 문서에 반영한다.

📋 **에이전트 지시 프롬프트**

```
T3 — UPGRADE_FRONT_PROMPT_SET 작업을 수행해줘.

아래 문서를 읽고 시작해:
- docs/system.md
- docs/task-definition.md
- docs/current/front-prompt/ROUTES/{담당_route_key}.md
- 현재 구현된 {route_key} 라우터 코드

현재 구현된 코드를 기준으로
docs/current/front-prompt/ROUTES/{route_key}.md 를 최신 상태로 업데이트해줘.

주의:
- 이 라우터 파일만 수정한다
- ROUTER_MAP.md, STORYBOARD.md, GLOBAL_RULES.md 는 수정하지 않는다
```

> ⚠️ **공유 문서(ROUTER_MAP, STORYBOARD, GLOBAL_RULES)는 절대 수정하지 않는다.**  
> 수정이 필요하다고 판단되면 리드에게 요청한다.

---

## Step 1-5. PR 제출

🧑 **직접 할 일**

```bash
# 변경사항 커밋
git add docs/current/front-prompt/ROUTES/{route_key}.md
git add docs/current/front-prompt/ROUTES/_status.md
git add src/pages/{PageComponent}.tsx
git add public/data/routes/{route_key}.json
git commit -m "feat({route_key}): 프론트 목업 개발 완료"

# 원격 브랜치에 push
git push origin p1/route/{route_key}
```

GitHub에서 PR 생성:

- base: `p1`
- compare: `p1/route/{route_key}`

**PR 제목 형식:**

```
[route_key] 페이지 목업 개발 완료
```

**PR 본문에 아래 체크리스트 포함:**

```
## 작업 내용
- route_key: {route_key}
- path: {path}
- 담당자: {이름}

## QA 체크리스트
□ 페이지 정상 진입 확인
□ loading / success / empty / error 상태 모두 구현
□ 버튼 클릭 반응 확인
□ 라우터 간 이동 정상 확인
□ GLOBAL_RULES 레이아웃 규칙 준수
□ 다른 페이지 영향 없음 확인
□ ROUTES/{route_key}.md 업데이트 완료
□ _status.md 상태 업데이트 완료

## 스크린샷
(선택) 주요 상태별 화면 캡처 첨부
```

`_status.md` 상태 변경:

```
🔍 자체 QA 중  →  📬 리뷰 요청 (PR #번호)
```

---

## Step 1-6. 리뷰 및 Merge

👑 **리드가 할 일**

PR을 검토하고 아래 항목을 확인한다:

```
□ QA 체크리스트 항목이 모두 체크되었는가?
□ ROUTES/{route_key}.md가 실제 코드와 일치하는가?
□ 공유 문서(ROUTER_MAP, STORYBOARD, GLOBAL_RULES)를 건드리지 않았는가?
□ 다른 라우터 코드에 영향이 없는가?
□ _status.md가 업데이트되었는가?
```

이상 없으면 `p1`에 merge한다.

merge 후 `_status.md` 상태 변경:

```
📬 리뷰 요청  →  ✅ 완료
```

---

---

# 공유 문서 수정 권한 규칙

> 이 규칙을 어기면 팀 전체 conflict의 원인이 된다.

| 문서 | 수정 가능자 | 사유 |
|---|---|---|
| `ROUTES/{본인_route_key}.md` | 담당자 본인 | 개인 담당 영역 |
| `ROUTES/_status.md` | 누구나 (본인 행만) | 상태 추적용 |
| `ROUTER_MAP.md` | 리드만 | 전체 구조 영향 |
| `STORYBOARD.md` | 리드만 | 전체 흐름 영향 |
| `GLOBAL_RULES.md` | 리드만 | 전체 UI 규칙 영향 |
| `PROJECT_META.md` | 리드만 | 서비스 방향 영향 |
| `PRD_UI_SUMMARY.md` | 리드만 | 서비스 방향 영향 |

---

# Conflict 해결 방법

---

## `_status.md` conflict 시

각자 본인 행만 수정하므로 conflict가 나도 해결이 단순하다.

```bash
# conflict 발생 시
git merge p1
# _status.md를 열어서 두 버전을 합친다 (본인 행 + 상대방 행 모두 유지)
git add docs/current/front-prompt/ROUTES/_status.md
git commit
```

---

## 공유 문서 conflict 시

공유 문서(ROUTER_MAP 등)에 conflict가 생겼다면,  
팀원이 잘못 수정한 것이다.

```bash
# 공유 문서는 p1 버전으로 되돌린다
git checkout p1 -- docs/current/front-prompt/ROUTER_MAP.md
git checkout p1 -- docs/current/front-prompt/STORYBOARD.md
```

변경이 필요하다면 리드에게 요청한다.

---

---

# 모든 라우터 완료 후 → P1 완료 처리

👑 **리드가 할 일**

`_status.md`의 모든 라우터가 `✅ 완료` 상태가 되면  
README.md의 **Step 1-4 (T3), Step 1-5 (T4), Step 1-6 (T7)** 을 순서대로 진행한다.

```
□ _status.md 전체 라우터 ✅ 완료 확인
□ T3 — 전체 front-prompt 문서 최신화
□ T4 — PRD 최신화
□ T7 — P2 전환 판단 및 브랜치 전환
```

---

---

# 자주 쓰는 명령어 모음

## 현재 작업 브랜치 확인

```bash
git branch
git status
```

## p1 최신 변경사항 내 브랜치에 반영

```bash
git checkout p1/route/{route_key}
git fetch origin
git rebase origin/p1
```

## 라우터 개발 진행 상황 확인

```
docs/current/front-prompt/ROUTES/_status.md 파일 열기
```

---

---

# 주의사항

- **절대로 `p1`, `p2`, `p3` 브랜치에 직접 push하지 않는다**
- **공유 문서는 리드 승인 없이 수정하지 않는다**
- **본인 담당 라우터 외의 코드는 건드리지 않는다**
- **PR 없이 merge하지 않는다**
- **conflict가 생기면 혼자 해결하려 하지 말고 리드에게 먼저 알린다**
