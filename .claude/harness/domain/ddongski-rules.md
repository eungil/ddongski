# ddongski-rules.md — 반려견 도메인 규칙

## 데이터 모델

### Dog (반려견 프로필)

| 필드 | 타입 | 설명 |
|---|---|---|
| id | string | 고유 식별자 |
| name | string | 반려견 이름 |
| breed | string | 견종 |
| birthDate | string | 생년월일 |
| weight | number | 체중 (kg) |
| size | 'small' \| 'medium' \| 'large' | 사이즈 |
| isNeutered | boolean | 중성화 여부 |
| baselineColor | string | 평소 색깔 (온보딩 입력) |
| baselineConsistency | 1~5 | 평소 형태 (온보딩 입력) |
| baselineFrequency | number | 평소 하루 횟수 (온보딩 입력) |
| createdAt | string | 등록일 |

### PoopRecord (배변 기록)

| 필드 | 타입 | 설명 |
|---|---|---|
| id | string | 고유 식별자 |
| dogId | string | 반려견 ID |
| date | string | 기록 날짜 |
| consistency | 1~5 \| null | 형태 (5단계) |
| color | string \| null | 색깔 |
| coating | 'none' \| 'clear_mucus' \| 'green_mucus' \| 'red_mucus' \| null | 코팅 |
| content | 'none' \| 'grass' \| 'worms' \| 'foreign' \| null | 내용물 |
| amount | 'small' \| 'medium' \| 'large' \| null | 양 |
| behavior | 'normal' \| 'straining' \| 'crying' \| 'accident_walk' \| 'accident_sleep' \| null | 배변 행동 |
| abnormalSmell | boolean | 냄새 이상 |
| note | string \| null | 메모 |
| photoUri | string \| null | 사진 경로 |
| createdAt | string | 생성일 |

---

## 형태 분류 (5단계)

| 단계 | 설명 |
|---|---|
| 1 | 딱딱한 펠릿형 |
| 2 | 단단한 통나무형 (이상적) |
| 3 | 부드럽고 찐득 |
| 4 | 묽고 쌓이는 형태 |
| 5 | 액체형 |

---

## 경고 시스템

### 즉시 경고 (병원 방문 권고)

| 조건 | 근거 |
|---|---|
| color: black / red | 소화기 출혈 의심 |
| color: white | 간·담관 문제, 기생충 의심 |
| color: pink_purple + 라즈베리잼 형태 | 출혈성 위장염(HGE) 의심 |
| coating: red_mucus | 감염·출혈 의심 |
| content: worms | 기생충(촌충) 감염 의심 |
| content: foreign | 장폐색 위험 |
| 강아지(~1세) 24시간 무배변 | 강아지는 변비도 응급 |
| 성견 48시간 무배변 + behavior: straining | 변비·장폐색 의심 |
| size: large + 식후 복부 팽창 + 무배변 | Bloat(위염전) 응급 |

### 주의 (48시간 경과 관찰)

| 조건 | 근거 |
|---|---|
| color: yellow_orange 지속 | 간·담낭·췌장 의심 |
| color: green 반복 | 담낭·기생충·풀 과다 |
| color: gray_oily | 지방 흡수 장애, 췌장 문제 |
| coating: green_mucus | 감염 가능성 |
| consistency 4~5 반복 | 설사 의심 |
| baselineFrequency 대비 +2회 이상 | 소화 이상·스트레스 |
| baselineFrequency 대비 -2회 이상 | 변비·식욕 저하 |
| behavior: straining / crying 반복 | 통증·폐색 가능성 |
| 노견(7세+) + behavior: accident_walk / accident_sleep | 신경계 이상·인지기능장애 |

### 품종별 추가 경고

| 견종 | 추가 경고 |
|---|---|
| German Shepherd | 복부 팽창 + 무배변 → 즉시 경고 강화 |
| Great Dane | 식후 30분 내 무배변 + 팽창 → Bloat 즉시 경고 |
| Boxer | coating: green_mucus 반복 → 주의 알림 |
| Miniature Schnauzer | color: gray_oily → 췌장염 경고 강화 |

---

## 라이프스테이지별 정상 배변 횟수

| 단계 | 나이 | 정상 횟수/일 |
|---|---|---|
| 강아지 초기 | ~12주 | 5~6회 |
| 강아지 | 12주~6개월 | 3~4회 |
| 강아지 후기 | 6개월~1세 | 2~3회 |
| 성견 | 1~7세 | 1~3회 |
| 노견 | 7세+ | 1~2회 |

---

## 베이스라인 경고 규칙

| 온보딩 입력 | 경고 조건 |
|---|---|
| baselineColor | 다른 색깔 기록 시 주의 판단 |
| baselineConsistency | ±2단계 이상 변화 시 주의 |
| baselineFrequency | ±2회 이상 증감 시 주의 |
