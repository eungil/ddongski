# /design-brief

완성된 PRD를 읽어 Claude 디자인(Pencil)에 바로 전달할 수 있는 디자인 브리프를 생성합니다.

## 사용법

```
/design-brief [기능명 또는 PRD 파일 경로]
```

예시:
```
/design-brief 송금내역-필터
/design-brief traces/prd-송금내역-필터-20260427.md
```

기능명만 입력하면 `traces/`에서 가장 최근 PRD를 자동으로 찾습니다.

---

## 실행 절차

### Step 1: PRD 탐색

- 파일 경로가 주어진 경우 → 해당 파일 읽기
- 기능명만 주어진 경우 → `traces/prd-{기능명}*.md` 패턴으로 가장 최근 파일 탐색
- PRD를 찾지 못한 경우 → PM에게 경로 확인 요청 후 중단

### Step 2: 컨텍스트 수집

아래 파일을 순서대로 읽기 (있는 것만):

1. 탐색한 PRD 파일
2. `.claude/harness/templates/design-brief-template.md`
3. `.claude/harness/domain/brand.md` — 색상·타이포·톤 참조용
4. `.claude/harness/domain/strategy.md` — 타겟 유저·Phase 목표 보완용

### Step 3: 디자인 브리프 생성

PRD의 아래 항목을 template 각 섹션에 매핑:

| PRD 항목 | Design Brief 섹션 |
|---|---|
| 기능 목적 / 해결 문제 | 1. 한 줄 요약 |
| 타겟 유저 / 페르소나 | 2. 대상 사용자 |
| 화면 목록 / 진입점 | 3. 화면 목록 |
| 핵심 유저 플로우 | 4. 핵심 유저 플로우 |
| UI 컴포넌트 요구사항 | 5. 컴포넌트 목록 |
| 데이터 요구사항 / 상태 | 6. 데이터 · 상태 |
| brand.md 내용 | 7. 브랜드 가이드 |
| 기술 제약 / 해상도 | 8. 디자인 제약 |
| PRD 경로 + 관련 AC | 9. 참고 문서 |

**생성 규칙:**
- PRD에 명시되지 않은 항목은 `{작성 필요}`로 표시 (임의로 채우지 말 것)
- 브랜드 가이드는 brand.md가 있으면 실제 값으로, 없으면 `{brand.md 참고}` 표시
- 엣지 케이스(빈 상태·에러·권한 없음)는 PRD에 없어도 유저 플로우 기반으로 추론해서 제안

### Step 4: 저장 + 출력

저장 경로: `.claude/harness/traces/design-brief-{기능명-kebab}-{YYYYMMDD}.md`

저장 후 아래 형식으로 PM에게 보고:

```
✅ 디자인 브리프 생성 완료

파일: traces/design-brief-{기능명}-{YYYYMMDD}.md
참조 PRD: traces/prd-{기능명}-{YYYYMMDD}.md

채워야 할 항목: {`{작성 필요}` 개수}개
→ [항목 목록]

Pencil 전달 방법:
생성된 파일을 Claude 디자인 대화창에 첨부하거나,
내용을 복사해서 붙여넣고 "이 브리프로 디자인해줘"로 시작하세요.
```

---

## 산출물

- `.claude/harness/traces/design-brief-{기능명-kebab}-{YYYYMMDD}.md`
