# DiceSpell 첫 런 온보딩 B-4 Ending Primer 요약

## 3줄 요약
- B-4는 첫 런 온보딩의 마지막 surface인 `ending`만 commit-level handoff로 좁힌 문서다.
- 핵심은 `ending`이 **결과/정산/버튼 의미**까지만 말하고, `themeTrophy` / `enemyTrophy` / `none` flavor와 다음 권 장면성은 끝까지 `overrunEvent`에 남기는 것이다.
- 구현 범위는 `ending.defeat` / `ending.victory` / `ending.overrun-cta` 3면, `seenDefeatPrimer` / `seenVictoryPrimer` / `seenOverrunCtaPrimer` 3필드, CTA 비차단, `오버런 전리품 보관함` 공존 검수선까지다.

**한 줄 목표:** 첫 결과 화면이 초보자에게 필요한 최소 의미는 남기되, overrun 장면을 미리 먹지 않게 만든다.

## 왜 이 문서가 필요한가
- B-1 library, B-2 battle, B-3 reward/shop는 이미 실제 첫 커밋 경계까지 닫혀 있다.
- 마지막 `ending` surface는 `오버런 전리품 보관함`과 `오버런 계속` CTA가 함께 있어 가장 쉽게 범위가 부푸는 구간이다.
- 그래서 `ending primer`만 따로 빼, 무엇을 일부러 하지 않을지까지 commit boundary로 고정할 필요가 있었다.

## 누가 무엇을 보면 되나
| 역할 | 먼저 읽을 부분 | 핵심 기억 문장 |
| --- | --- | --- |
| 프로그래머 | `renderEnding(summary)` anchor, 3개 content key, 3개 seen 필드 | ending은 flavor를 만들지 않는다 |
| UI | primer 위치와 위계 | badge/cta helper만 쓰고 hero-card 금지 |
| 아트 | accent/token 최소 패키지 | ending primer는 overrunEvent 카드처럼 보이면 안 된다 |
| QA | B4-A1~A7 acceptance, recovery 6종 | `결과 해석`과 `장면 flavor`를 분리해 본다 |

## core structure

### source handoff 범위
- `ending.defeat`: 패배해도 남는 기록/진척 설명
- `ending.victory`: 완주 보너스 후 다음 선택 설명
- `ending.overrun-cta`: `오버런 계속` 버튼 의미만 설명
- `seenDefeatPrimer` / `seenVictoryPrimer` / `seenOverrunCtaPrimer`

### 일부러 하지 않을 것
- `themeTrophy` / `enemyTrophy` / `none` flavor를 ending에 넣지 않기
- `오버런 계속` 설명을 큰 카드로 키우지 않기
- `오버런 전리품 보관함` 로직/정산 규칙 건드리지 않기

## role-specific handoff points

### 프로그래머
- `renderEnding(summary)`에 defeat/victory primer slot과 overrun CTA helper만 추가
- `meta.uiGuidance` 하위 3개 seen 플래그만 정규화
- CTA와 정산 로직은 그대로 유지

### UI
- defeat: muted ash badge strip
- victory: compact gold badge strip
- overrun CTA: 버튼 근처 violet helper line
- 결과 헤더/정산/CTA가 primer보다 우선

### 아트
- `primer-ending-defeat`, `primer-ending-victory`, `primer-ending-overrun` 최소 토큰 세트
- 대형 신규 일러스트 불필요

### QA
- 첫 패배 ending / 첫 승리 ending / 첫 오버런 CTA 3축 검증
- `오버런 전리품 보관함` 공존 시 겹침 없음 확인
- ending 카피에 overrun flavor 유입 금지 확인

## risks / follow-ups
- 가장 큰 리스크는 ending이 `overrunEvent`가 할 말을 미리 설명하는 것.
- 두 번째 리스크는 victory primer와 overrun CTA helper가 같은 의미를 두 번 말하는 것.
- 세 번째 리스크는 `오버런 전리품 보관함`과 primer가 같은 위계의 카드처럼 보이는 것.
- 후속 구현은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서를 지키고, B-4에서도 `docs/dicespell_ending_overrun_boundary_packet_kr.md`를 함께 본다.

## immediate next actions
1. 현재 슬롯에서는 B-4 문서를 source packet + readable summary 쌍으로 고정한다.
2. overrun A-track이 실제로 닫힌 뒤에만 온보딩 B-track 구현 순서를 `library -> battle -> reward/shop -> ending`으로 연다.
3. B-4 구현 시에는 ending이 `결과/정산/버튼 의미`까지만 말하는지 먼저 확인하고, flavor는 끝까지 `overrunEvent`에 남긴다.
