# ddongski-rules.md — 반려견 도메인 규칙

## 데이터 모델 (응가뿡가 대비 변경사항)

### Dog (≈ Child)
| 필드 | 타입 | 설명 |
|---|---|---|
| id | string | 고유 식별자 |
| name | string | 반려견 이름 |
| breed | string | 견종 |
| weight | number | 체중 (kg) |
| birthDate | string | 생년월일 |
| gender | 'male' \| 'female' | 성별 |

### 변 상태 분류 (Bristol Scale 대체)
| 코드 | 설명 | 심각도 |
|---|---|---|
| normal | 단단하고 촉촉한 변 (이상적) | 정상 |
| soft | 무른 변 | 주의 |
| loose | 액체 섞인 무른 변 | 경고 |
| liquid | 완전한 액체 설사 | 즉시 경고 |

### 경고 기준
[TBD — 수의사 가이드라인 기반으로 정의 예정]
- 마지막 배변 N일 경과 → 주의/경고
- 연속 soft/loose → 주의/경고
- liquid 1회 → 즉시 경고
- 색깔 이상 (검정, 빨강, 흰색, 초록) → 즉시 경고
