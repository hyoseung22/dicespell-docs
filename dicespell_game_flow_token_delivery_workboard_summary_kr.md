# DiceSpell `Run Table` Token Delivery Workboard Summary

## 3줄 요약
- `surface handoff`와 `token anchor` 다음 단계의 남은 공백은 이제 `무슨 토큰인가`가 아니라 `누가 어디부터 붙이는가`다.
- 이번 문서는 `Run Atlas`, `Battle Table`, `Overrun Oath` 3면만 대상으로 hook -> density review -> starter asset 순서를 고정한다.
- 자세한 계약은 `docs/dicespell_game_flow_token_delivery_workboard_kr.md`에 있다.

## 한 줄 목표
Run Table 후속 refinement가 generic polishing으로 번지지 않도록, atlas/battle/oath 3면의 실제 전달 순서를 owner workboard 형태로 잠근다.

## 왜 필요한가
토큰 이름과 QA anchor는 이미 정리됐지만, 실제 다음 턴에는 여전히 `프로그래머/UI/아트/QA가 어디부터 시작해야 하는가`가 한 장에 모여 있지 않았다. 이 summary는 그 결론만 빠르게 읽게 하는 companion이다.

---

## 지금 먼저 잠글 것

### 1) Hook lock
- 프로그래머는 `renderRouteBoard()` / `renderBattle()` / `renderEnding()`에 root/state/slot/role hook 범위를 먼저 고정한다.
- 목표: CSS와 QA 기준을 분리한다.

### 2) Density lock
- UI 오너는 atlas/battle/oath 3면만 1440p first-glance review를 돌린다.
- 목표: `무엇이 먼저 보여야 하는가`를 짧게 확인한다.

### 3) Starter asset lock
- 아트 오너는 `Route Node Starter` / `Stakes Plaque Starter` / `Oath Banner Starter` 3묶음만 닫는다.
- 목표: 큰 배경보다 surface 차이를 오래 버티는 frame/badge/seal부터 고정한다.

---

## 오너별 바로 볼 포인트

| 역할 | 지금 해야 할 일 | 특히 주의할 것 |
| --- | --- | --- |
| 프로그래머 | atlas/battle/oath hook 범위 선언 | 새 data truth나 대형 리팩터링으로 번지지 않기 |
| UI 오너 | 1440p density review 1회 | `예쁨`보다 `first glance`를 기록하기 |
| 아트 오너 | starter asset 3묶음만 정의 | 큰 배경/일러스트부터 열지 않기 |
| QA / PM | 캡처 3장 + hook 목록 1개 확보 | 감상형 피드백 대신 역할 분리 기준으로 pass/fail 보기 |

---

## 가장 큰 리스크 4개
1. 작업 순서가 없으면 atlas/battle/oath를 한 번에 넓게 건드리게 된다.
2. density review가 감상형으로 흐르면 토큰 차이가 다시 취향 토론이 된다.
3. 아트가 큰 배경부터 시작하면 핵심 surface 차이가 늦게 닫힌다.
4. QA가 hook evidence 없이 캡처만 보면 다음 drift를 빨리 못 잡는다.

---

## 바로 다음 액션
1. `surface handoff -> token anchor -> token delivery workboard`를 Run Table 읽기 스택으로 고정
2. atlas/battle/oath 3면의 hook 범위 선언
3. 1440p density review 1회
4. starter asset 3묶음 우선 자산화
5. 캡처 3장 + hook 목록 1개 + 짧은 리뷰 문장 남기기
