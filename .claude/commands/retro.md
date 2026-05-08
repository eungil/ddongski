# /retro

traces/ 폴더의 실행 로그를 분석해 반복 패턴을 찾고, lessons/에 학습 규칙으로 승격합니다.

## 사용법
```
/retro
/retro [특정 기간 또는 기능명]
```

## 실행 흐름

**orchestrator 에이전트**를 호출하여 다음 순서로 진행합니다:

1. `.claude/harness/traces/`에서 실행 로그 전체 수집
2. 반복 패턴 분석:
   - 3회 이상 반복된 Gate 실패 유형
   - 자주 등장하는 PRD 린트 실패 항목
   - CONDITIONAL PASS에서 멈춘 기능들의 공통점
   - PM이 자주 수정 요청한 항목
3. 패턴별 lessons 초안 작성
4. PM 승인 후 `.claude/harness/lessons/` 저장

## lessons 파일 형식

```markdown
# Lesson: [제목] (YYYY-MM-DD)

## 패턴
[어떤 상황에서 반복적으로 발생했는가]

## 원인
[왜 이 패턴이 생겼는가]

## 규칙
[다음부터 어떻게 해야 하는가 — 에이전트 행동 변경 사항]

## 출처
- [trace 파일명 1]
- [trace 파일명 2]
```

## 산출물
- `.claude/harness/lessons/lesson-[제목]-[YYYYMMDD].md` (PM 승인 후)
- `.claude/harness/traces/trace-retro-[YYYYMMDD].md` (분석 로그)
