# DiceSpell 첫 런 온보딩 런타임 스택 요약

- 요약 1: 온보딩 문서는 이미 충분하지만, 실제 구현 턴에서는 `어느 surface를 어떤 커밋 단위로 닫을지`가 마지막 병목이었다.
- 요약 2: 이 문서는 B-track을 `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending`의 4커밋 스택으로 다시 압축한다.
- 요약 3: 핵심은 새 primer를 더 만드는 것이 아니라, 범위 팽창과 ending↔overrun 경계 재오염을 막는 것이다.

**한 줄 목표:** A-track green 이후 첫 런 온보딩을 `한 번에 다 붙이기`가 아니라 `surface별 bounded commit 4개`로만 실행하게 만든다.

## 왜 읽어야 하나

- 프로그래머: 이번 턴에 어느 surface까지만 닫는지 바로 알 수 있다.
- UI/아트: 언제부터 실제로 관여도가 올라가는지(B-3/B-4)가 보인다.
- QA/PM: tracker/run log에 어떤 완료 문장을 남겨야 하는지 surface별로 분리된다.

## 커밋 스택 한 장 요약

| 커밋 | 목표 | 절대 같이 하지 말 것 |
| --- | --- | --- |
| B-1 | library 첫 진입 hero-primer 고정 | battle/reward/shop/ending primer 동시 착수 |
| B-2 | battle entry/first-spell/auto-pass guidance 고정 | reward/shop/ending primer와 합치기 |
| B-3 | reward vs shop 목적 분리 고정 | 새 reward 규칙/상점 규칙 같이 수정 |
| B-4 | ending 결과/CTA 의미 primer 고정 | overrunEvent flavor 재서술 |

## 역할별 포인트

### 프로그래머
- B-1~B-4를 `surface 1개 = commit 1개`로만 연다.
- B-4에서는 `ending`과 `overrunEvent` 책임을 다시 섞지 않는다.

### UI 오너
- B-1/B-2는 검수 중심, B-3/B-4는 실질 owner 단계다.
- density보다 경계 유지가 우선이다.

### 아트 오너
- library/battle는 최소 token 세트, reward/shop/ending은 분리된 accent와 neutral 패키지가 중요하다.

### QA
- 각 커밋마다 acceptance/recovery evidence를 따로 남긴다.
- B-4는 defeat/victory/overrun CTA + boundary 증거가 필수다.

### PM/기획
- `온보딩 추가` 같은 뭉뚱그린 완료 문장을 금지한다.
- surface별 종료 문장을 그대로 재사용한다.

## 리스크

- B-track이 다시 `온보딩 전체 붙이기`로 부풀 수 있다.
- B-4에서 ending primer가 overrunEvent flavor를 먹을 수 있다.
- B-1~B-4가 같은 완료 문장으로 기록되면 실제 종료선이 사라진다.

## 바로 다음 액션

1. A-track green 전에는 B-track을 열지 않는다.
2. A-track green 후에는 이 문서 기준으로 B-1부터 순서대로만 실행한다.
3. 각 커밋은 surface별 evidence와 완료 문장을 따로 남긴다.
