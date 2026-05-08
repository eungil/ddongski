# /new-feature

team-assemble 패턴으로 기능 PRD를 빠르게 생성합니다.
analyst (sonnet) → writer (opus) 2-멤버 순차 팀 실행.

## 사용법
```
/new-feature [기능 아이디어 또는 문제 정의]
```

## 팀 구성

| # | 역할 | 모델 | 담당 | 의존성 |
|---|------|------|------|--------|
| 1 | analyst | sonnet | Gate 체크 + Score + 핵심 가정 3개 → compact summary | - |
| 2 | writer | opus | PRD 작성 + phase1-lint 인라인 검증 + 저장 | #1 |

---

## 실행 절차

### Phase 1: 팀 생성

```
TeamCreate(team_name: "feature-team", description: "/new-feature: {기능 요약}")
TaskCreate(subject: "#1 analyst: Gate+Score+핵심가정", ...)
TaskCreate(subject: "#2 writer: PRD+lint", ...)
TaskUpdate(taskId: "#2", addBlockedBy: ["#1"])
```

---

### Phase 2: analyst 실행 (sonnet)

**컨텍스트 소스:** CLAUDE.md 전략 룰 퀵레퍼런스만 사용. 도메인 파일 읽기 생략.
- 프로젝트 특화 규칙이 관여되는 기능이면 `.claude/harness/domain/` 내 해당 파일 추가 읽기.

**analyst 출력 형식 (30줄 이내 — 별도 파일 저장 없음):**

```
## Analyst Summary — {기능명}

Gate: PASS | FLAG (N/N)
Score: N/20 (중요도 X, 만족도 Y) → 높음|중간|낮음

핵심 가정:
- Value: {가정} — High|Medium|Low
- Usability: {가정} — High|Medium|Low
- Feasibility: {가정} — High|Medium|Low

미결 질문: (없으면 생략)
- Q1: {질문}

권장: PASS | CONDITIONAL PASS | FLAG
```

**Gate 분기:**
- **FLAG** → TaskUpdate(완료) + TeamDelete 후 PM에게 FLAG 사유 보고. **종료.**
- **CONDITIONAL PASS** → analyst 결과 PM에게 공유 + 인터뷰 플랜 제안. PM 결정 대기.
- **PASS** → analyst 결과를 writer 프롬프트에 삽입해 Phase 3 진행.

---

### Phase 3: writer 실행 (opus)

**analyst 요약 + 아래 파일만 읽기:**
- `.claude/harness/templates/prd-template.md`
- `.claude/harness/rules/phase1-lint.md`
- `.claude/harness/domain/brand.md` — 카피 작성 시만 (파일 있으면)
- `.claude/harness/domain/` 내 프로젝트 특화 규칙 — 해당 기능 관련 시만

**작업 순서:**
1. analyst 요약의 Gate 결과·Score·핵심 가정을 PRD §2·§9에 반영
2. prd-template.md 기반으로 PRD 작성
3. phase1-lint.md 항목 자체 검증
4. FAIL 항목 수정 → 재검증
5. PASS 확인 후 저장: `.claude/harness/traces/prd-{기능명-kebab}-{YYYYMMDD}.md`
6. design-brief 생성 후 저장: `.claude/harness/traces/design-brief-{기능명-kebab}-{YYYYMMDD}.md`

---

### Phase 4: trace 저장 + 팀 정리

trace 파일 저장: `.claude/harness/traces/trace-new-feature-{기능명}-{YYYYMMDD}.md`

```markdown
# Trace: /new-feature — {기능명} ({YYYY-MM-DD})

## 실행 단계
- [x] analyst — Gate N/N, Score N/20
- [x] writer — lint PASS (N/N)

## 산출물
- PRD: `traces/prd-{기능명}-{YYYYMMDD}.md`
- Design Brief: `traces/design-brief-{기능명}-{YYYYMMDD}.md`
```

TaskUpdate(완료) → TeamDelete.

---

## Gate 규칙
- FLAG (기준 이상 No) → 중단, PM 검토 요청
- CONDITIONAL PASS (Score 5↓) → 인터뷰 플랜 제안
- lint FAIL → writer 수정 후 재검증, 통과 전 저장 금지

## 산출물
- `.claude/harness/traces/prd-{기능명}-{YYYYMMDD}.md`
- `.claude/harness/traces/design-brief-{기능명}-{YYYYMMDD}.md`
- `.claude/harness/traces/trace-new-feature-{기능명}-{YYYYMMDD}.md`
