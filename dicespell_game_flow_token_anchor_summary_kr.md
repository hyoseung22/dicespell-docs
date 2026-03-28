# DiceSpell `Run Table` Token & QA Anchor Summary

## 3줄 요약
- 이번 후속 문서의 목적은 `Run Table` UI 패스가 다시 generic polishing으로 흐르지 않도록 핵심 토큰과 QA anchor를 먼저 잠그는 것이다.
- 우선 대상은 3개다: `Run Atlas`의 route node, `Battle Table`의 stakes plaque, `Overrun Oath`의 oath banner.
- 자세한 계약은 `docs/dicespell_game_flow_token_anchor_packet_kr.md`, 기존 surface 목적 설명은 `docs/dicespell_game_flow_surface_handoff_packet_kr.md`에 있다.

## 한 줄 목표
`route node / stakes plaque / oath banner`를 토큰 이름, DOM hook, 검수 질문, 아트 starter 패키지까지 같은 문장으로 고정해 surface 차이가 다시 흐려지지 않게 만든다.

## 왜 이 문서가 필요한가
surface family 방향은 이미 잡혔지만, 실제 다음 refinement 직전에는 `어떤 작은 토큰을 먼저 닫아야 drift가 안 나는가`가 아직 제일 큰 공백이다. 이 문서는 그 결론만 빠르게 읽게 하는 companion이다.

---

## 지금 먼저 잠글 3가지

### 1) `Run Atlas` = route node token
- 목표: `현재 / 다음 상점 / 다음 보스`가 텍스트 읽기 전에 바로 구분되게 만든다.
- 프로그래머 포인트: `renderRouteBoard()`에 surface/state hook을 붙일 준비를 한다.
- 아트 포인트: current/past/shop/boss 4상태 토큰부터 닫는다.

### 2) `Battle Table` = stakes plaque token
- 목표: 전투가 계산 HUD보다 `이번 판 stakes` 중심으로 먼저 읽히게 만든다.
- 프로그래머 포인트: `current / next / grimoire` 3슬롯을 markup과 QA 이름으로 고정한다.
- UI/아트 포인트: header extension처럼 보이되 action panel을 가리지 않아야 한다.

### 3) `Overrun Oath` = banner / seal token
- 목표: ending이 reward/shop card family의 큰 버전처럼 보이지 않게 만든다.
- 프로그래머 포인트: ending root와 `banner / result / carry` 역할을 hook으로 분리한다.
- 아트 포인트: main banner, seal pill, side accent 3종만 먼저 자산화한다.

---

## 오너별 바로 볼 포인트

| 역할 | 지금 봐야 할 것 | 특히 주의할 것 |
| --- | --- | --- |
| 프로그래머 | `data-run-surface`, state/slot/role hook 제안 | CSS class만으로 QA 기준을 때우지 않기 |
| UI 오너 | atlas/battle/oath의 first-glance 우선순위 | 모든 차이를 glow 색상 하나로 해결하지 않기 |
| 아트 오너 | Route Node / Stakes Plaque / Oath Banner starter 패키지 | 큰 배경보다 frame/badge/seal부터 닫기 |
| QA / PM | 텍스트 읽기 전 역할 구분 가능 여부 | `예뻐짐`이 아니라 `역할 분리`를 pass 기준으로 보기 |

---

## 가장 중요한 리스크 4개
1. route/stakes/oath 차이를 copy와 색상만으로 유지하면 다음 CSS 수정에서 다시 평준화될 수 있다.
2. DOM hook이 없으면 QA가 여전히 감상형 리뷰에 머물 수 있다.
3. 1440p 밀도 검토 없이 정리하면 right panel과 ending side column이 다시 과밀해질 수 있다.
4. 아트가 큰 배경부터 시작하면 핵심 surface 차이가 늦게 닫힌다.

---

## 바로 다음 액션
1. packet + summary를 기존 Run Table handoff 문서 옆에 포털 링크로 노출
2. `renderRouteBoard()` / `renderBattle()` / `renderEnding()`용 최소 hook 이름 확정
3. atlas / battle / oath 3면만 따로 1440p density review 1회
4. `Route Node Starter` / `Stakes Plaque Starter` / `Oath Banner Starter` 3묶음 우선 자산화
5. QA는 `텍스트를 읽기 전에도 surface 역할이 보이는가`를 토큰 기준으로 기록
