# DiceSpell `Run Table` Token Closing Summary

## 3줄 요약
- `Run Table` 후속 문서의 남은 마지막 공백은 이제 `무슨 token인가`나 `누가 어디부터 시작하는가`가 아니라 `언제 닫혔다고 말할 수 있는가`다.
- 이번 문서는 `Run Atlas`, `Battle Table`, `Overrun Oath` 3면의 closing을 `hook lock -> density lock -> starter asset lock` 3단계로 고정한다.
- 자세한 계약은 `docs/dicespell_game_flow_token_closing_packet_kr.md`에 있다.

## 한 줄 목표
Run Table token delivery가 다시 `거의 완료` 상태로 흐르지 않도록, atlas/battle/oath 3면의 제출물과 pass/fail 문장을 production closing 언어로 잠근다.

## 왜 필요한가
`surface handoff`, `token anchor`, `token delivery workboard`까지로 시작 순서는 충분히 정리됐다. 하지만 실제 다음 refinement 직전에는 여전히 `무엇이 제출되면 닫혔다고 말할 수 있는가`가 한 장에 모여 있지 않았다. 이 summary는 그 결론만 빠르게 읽게 하는 companion이다.

---

## closing을 3단계로 잠글 것

### 1) Hook lock
- atlas/battle/oath에 root/state/slot/role hook을 먼저 붙인다.
- 목적: CSS 수정과 QA 기준을 분리한다.
- 최소 증거: hook 목록 1개 + DOM 존재 확인 1개.

### 2) Density lock
- 1440p 캡처로 `무엇이 먼저 보여야 하는가`를 확인한다.
- 목적: 감상형 피드백 대신 first-glance hierarchy를 잠근다.
- 최소 증거: atlas/battle/oath 캡처 3장 + `유지 / 약함 / 과함` 리뷰 1개.

### 3) Starter asset lock
- `Route Node Starter`, `Stakes Plaque Starter`, `Oath Banner Starter` 3묶음만 닫는다.
- 목적: 큰 배경보다 token 차이를 오래 버티는 frame/badge/seal부터 잠근다.
- 최소 증거: starter asset 목록 1개 + 적용 전/후 비교 1개.

---

## surface별 closing 한 줄

| surface | 닫혔다고 말할 수 있는 기준 |
| --- | --- |
| Run Atlas | `current / past / shop / boss / upcoming`이 state 이름과 1440p 캡처에서 같은 단어로 읽힌다 |
| Battle Table | `current / next / grimoire` stakes 3슬롯이 info card가 아니라 plaque 역할로 읽힌다 |
| Overrun Oath | ending이 reward/shop 변형이 아니라 `banner / result / carry` 3층으로 읽힌다 |

---

## 오너별 바로 남길 것

| 역할 | 이번 단계 최소 제출물 | 주의할 것 |
| --- | --- | --- |
| 프로그래머 | hook manifest + DOM diff note | 새 data truth나 대형 리팩터링으로 번지지 않기 |
| UI 오너 | 1440p 캡처 3장 + density review | `예쁨`보다 `무엇이 먼저 보이는가`를 기록하기 |
| 아트 오너 | starter asset 3묶음 | 큰 배경/일러스트부터 열지 않기 |
| QA / PM | closing note + evidence bundle + handoff 문장 | hook evidence 없는 캡처-only 제출을 pass로 보지 않기 |

---

## 가장 큰 fail 4개
1. hook 목록 없이 캡처만 남는다.
2. review 문장이 `좋아 보임` 같은 감상형으로 끝난다.
3. starter asset 범위가 커져 큰 배경/전체 리페인트까지 열린다.
4. tracker/run log에 `거의 완료`, `UI polish 진행` 같은 애매한 문장만 남는다.

---

## 바로 다음 액션
1. `surface handoff -> token anchor -> token delivery workboard -> token closing packet`을 읽기 스택으로 고정
2. atlas/battle/oath hook manifest 작성
3. 1440p 캡처 3장 + density review 기록
4. starter asset 3묶음만 제출
5. QA/PM은 `hook / density / starter asset` 어디까지 닫혔는지 한 줄 handoff 문장으로 기록
