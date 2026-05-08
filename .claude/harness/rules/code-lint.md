# Phase 1 Code Lint Rules

> reviewer 에이전트가 coder의 코드 검증에 사용하는 규칙 목록.
> 모든 Critical 항목 PASS + High 항목 80% 이상 PASS 시에만 reviewer 통과.

---

## L1. TypeScript 컴파일 (Critical)

| ID | 규칙 | PASS 조건 | 검증 도구 |
|---|---|---|---|
| L1-01 | 타입 에러 없음 | `npx tsc --noEmit` 통과 | TypeScript |
| L1-02 | strict 모드 | `tsconfig.json`의 `strict: true` 설정 | TypeScript |
| L1-03 | any 금지 | `: any`, `as any` 패턴 없음 | grep + ESLint |
| L1-04 | unknown 타입 가드 | `unknown` 사용 시 type narrowing 있음 | ESLint custom |

---

## L2. 코드 스타일 (High)

| ID | 규칙 | PASS 조건 | 검증 도구 |
|---|---|---|---|
| L2-01 | ESLint 통과 | `npx eslint [file]` 에러 0개 | ESLint |
| L2-02 | Prettier 포맷 | `npx prettier --check [file]` 통과 | Prettier |
| L2-03 | 사용 안 하는 import | `no-unused-vars` 통과 | ESLint |
| L2-04 | console.log 없음 | production 코드에 `console.log` 없음 | ESLint |

---

## L3. DDD 레이어 의존성 (Critical)

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L3-01 | Domain → Infra 금지 | domain/ 파일이 infrastructure/ 또는 @supabase/ import 안 함 |
| L3-02 | Domain → Presentation 금지 | domain/ 파일이 app/ 또는 components/ import 안 함 |
| L3-03 | Application → Infra 직접 금지 | application/ 이 infrastructure/ 구현체를 직접 import 안 함 (인터페이스만) |
| L3-04 | Application → Presentation 금지 | application/ 이 app/ import 안 함 |
| L3-05 | Infra → Presentation 금지 | infrastructure/ 가 app/ import 안 함 |
| L3-06 | @layer 메타데이터 | 파일 상단 `@layer` 주석이 실제 폴더와 일치 |

---

## L4. 파일 메타데이터 (Critical)

| ID | 규칙 | 우선순위 | PASS 조건 |
|---|---|---|---|
| L4-01 | @layer 필수 | Critical | 파일 상단에 `@layer [값]` 주석 존재 |
| L4-02 | @context 필수 | Critical | `@context [Bounded Context명]` 주석 존재 |
| L4-03 | @description 필수 | Critical | `@description [한 줄 설명]` 주석 존재 |
| L4-04 | @depends-on 정합성 | High | `@depends-on` 명시된 경우에만 실제 import와 대조. 미명시 시 스킵. |

---

## L5. Supabase 보안 (Critical, migration 파일만)

> 적용 기준: 파일 경로가 `*/supabase/migrations/*.sql` 인 경우

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L5-01 | RLS 활성화 | CREATE TABLE마다 `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` 존재 |
| L5-02 | Policy 최소 1개 | 테이블마다 `CREATE POLICY` 최소 1개 |
| L5-03 | RLS 예외 사유 | RLS 제외 시 주석으로 사유 명시 |
| L5-04 | auth.uid() 참조 | 유저 데이터 테이블의 policy에 `auth.uid()` 사용 |

---

## L6. 환경변수·비밀 정보 (Critical)

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L6-01 | Supabase URL 하드코딩 금지 | `https://*.supabase.co` 리터럴 없음 |
| L6-02 | JWT 토큰 하드코딩 금지 | `eyJ...` 패턴 없음 |
| L6-03 | API 키 하드코딩 금지 | `sk-*` 등 API 키 패턴 없음 |
| L6-04 | DB URL 하드코딩 금지 | `postgres://` 리터럴 없음 |
| L6-05 | process.env 사용 | 환경변수는 `process.env.변수명`으로만 접근 |
| L6-06 | NEXT_PUBLIC prefix | 클라이언트 노출 값은 `NEXT_PUBLIC_` prefix 확인 |

---

## L7. DDD 모델 규칙 (High, 레이어별)

### Domain Layer

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L7D-01 | Entity 식별자 | Entity 클래스에 `id` 필드 존재 |
| L7D-02 | Value Object 불변 | VO의 모든 필드가 `readonly` |
| L7D-03 | Value Object 식별자 없음 | VO에 `id` 필드 없음 |
| L7D-04 | Repository 인터페이스 | Repository는 `I` prefix + interface |

### Application Layer

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L7A-01 | Use Case 단일 책임 | 하나의 `execute()` 메서드만 public |
| L7A-02 | DTO 사용 | 입력/출력이 DTO 타입 |
| L7A-03 | 의존성 주입 | Repository 구현체 직접 생성 금지 (생성자 주입) |

### Infrastructure Layer

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L7I-01 | 인터페이스 구현 | Repository 구현체가 Domain의 인터페이스 implements |
| L7I-02 | 타입 매핑 | Supabase row → Entity 변환 함수 존재 (`toDomain` 등) |

---

## L8. LunchCoin (Critical, 해당 파일만)

> 적용 기준: `@context ledger` 주석이 있거나 `LunchCoinTransaction` / `LunchCoinAmount` 키워드가 파일에 존재하는 경우

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L8-01 | 정수 연산 | 소수점 계산 없음, `Math.floor/round` 없음 |
| L8-02 | append-only | 기존 transaction 레코드 UPDATE 없음 |
| L8-03 | idempotency_key | 거래 생성 시 `idempotency_key` 필수 |
| L8-04 | balance 구분 | `earned_balance`와 `charged_balance` 혼용 금지 |
| L8-05 | 이중 기입 | 거래는 `from_account`, `to_account` 모두 명시 |

---

## L9. Next.js 규칙 (High, app/ 또는 components/ 파일)

> 적용 기준: `@layer presentation` 또는 파일 경로가 `app/` / `components/` 인 경우

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L9-01 | API Route 비즈니스 로직 금지 | API Route는 Use Case 호출만. DB 쿼리·도메인 로직 직접 작성 금지 |
| L9-02 | 동적 params await | Next.js 16+ 기준 `await params` 또는 `use(params)` 사용 |
| L9-03 | use client 선언 | `useState` / `useEffect` / 이벤트 핸들러 사용 시 파일 최상단 `"use client"` 필수 |
| L9-04 | service_role 클라이언트 차단 | `SUPABASE_SERVICE_ROLE_KEY`를 클라이언트 컴포넌트에서 접근 금지 |
| L9-05 | hex 색상 금지 | `style={{ color: '#...' }}` 등 인라인 hex 금지. Tailwind 클래스 사용 |
| L9-06 | HTTP 상태 코드 | 성공 200/201, 비즈니스 규칙 위반 400, 미인증 401, 권한 없음 403, 서버 오류 500 |

---

## L10. PRD Acceptance Criteria 역검증 (Critical)

| ID | 규칙 | PASS 조건 |
|---|---|---|
| L10-01 | AC 매핑 존재 | ADR의 AC Mapping에 이 파일이 등록됨 |
| L10-02 | AC 구현 | 등록된 AC에 해당하는 로직이 실제 파일에 존재 |
| L10-03 | Scope Creep 없음 | AC 외 추가 로직 없음 (있으면 PM 확인 플래그 후 보류) |

---

## 린트 결과 출력 형식

```
Code Lint 결과 — [파일 경로]

Critical (전부 통과 필수)
| ID      | 항목                   | 결과 | 비고                                      |
|---------|----------------------|------|-------------------------------------------|
| L1-01   | 타입 에러 없음         | PASS |                                           |
| L3-01   | Domain → Infra 금지   | FAIL | domain/User.ts가 @supabase/supabase-js import |
| ...     | ...                  | ...  | ...                                       |

High (80% 이상 통과 필요)
| ID      | 항목          | 결과 | 비고 |
|---------|-------------|------|------|
| L2-01   | ESLint 통과  | PASS |      |
| ...     | ...         | ...  | ...  |

요약
- Critical: N/N (통과/전체)
- High: N/N
- 최종: PASS / FAIL

실패 시 수정 방향
[구체적 수정안]
```

FAIL 시 coder에게 위 포맷 그대로 전달 → 재작업.
