# DiceSpell `Run Table` Token Closing Packet

## 3줄 요약
- 이 문서는 `surface handoff packet` → `token anchor packet` → `token delivery workboard` 다음 단계에서 남아 있던 마지막 실행 공백인 **언제 token delivery가 실제로 닫혔다고 말할 수 있는가**를 production handoff 언어로 고정한다.
- 읽는 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이다. 새 화면 제안이 아니라 `Run Atlas / Battle Table / Overrun Oath` 3면의 **hook lock / density lock / starter asset lock 종료선**을 정리하는 문서다.
- source-of-truth는 이 문서, 빠른 브라우징용 companion은 `docs/dicespell_game_flow_token_closing_summary_kr.md`, 선행 계약은 `docs/dicespell_game_flow_surface_handoff_packet_kr.md`, `docs/dicespell_game_flow_token_anchor_packet_kr.md`, `docs/dicespell_game_flow_token_delivery_workboard_kr.md`다.

## 한 줄 목표
`route node / stakes plaque / oath banner` 3축을 **착수 가능한 일감**으로만 남기지 않고, 실제로 무엇이 제출되고 어떤 문장으로 handoff되면 닫혔다고 말할 수 있는지까지 같은 문서로 잠근다.

## 왜 이 문서가 필요한가
지금까지 Run Table 문서 묶음은 다음을 이미 충분히 고정했다.

- 5개 surface family가 왜 다른가 (`surface handoff`)
- 어떤 핵심 token과 DOM/QA anchor를 먼저 잠가야 하는가 (`token anchor`)
- 누가 어느 함수/스타일 anchor부터 시작해야 하는가 (`token delivery workboard`)

하지만 실제 다음 refinement 직전에는 아직 아래 공백이 남아 있다.

- 프로그래머는 `hook을 붙이는 순서`는 알지만, **어느 시점에 `hook lock 완료`라고 기록해도 되는지**가 없다.
- UI 오너는 1440p review를 돌릴 수는 있지만, **어떤 리뷰 문장과 어떤 fail 기준이 closing evidence인지**가 흩어져 있다.
- 아트 오너는 starter asset 3묶음을 만들 수는 있지만, **이번 턴에서 무엇까지만 제출하면 충분한지**가 아직 closing 언어로 잠기지 않았다.
- QA/PM은 `좋아 보임`과 `token delivery closed`를 분리해야 하지만, **캡처 / hook 목록 / 리뷰 문장 / handoff 문장이 어떤 세트로 모여야 pass인지**를 한 장에서 보지 못한다.

즉 지금 남은 병목은 `무엇을 시작할까`가 아니라 **`무엇을 어디까지 제출하면 bounded하게 끝났다고 말할 수 있는가`**다. 이 문서는 그 종료선을 고정한다.

---

## 먼저 무엇을 읽어야 하는가

| 역할 | 먼저 볼 문서 | 그 다음 | 이 문서에서 특히 볼 섹션 |
| --- | --- | --- | --- |
| 프로그래머 | `docs/dicespell_game_flow_token_delivery_workboard_kr.md` | 이 문서 | 2, 4, 5, 8 |
| UI 오너 | `docs/dicespell_game_flow_token_delivery_workboard_summary_kr.md` | 이 문서 | 3, 5, 6, 8 |
| 아트 오너 | summary | 이 문서 | 3, 6, 7, 8 |
| QA / PM | summary | 이 문서 | 4, 6, 8, 9 |

---

## 1. 범위 선언

### 이번 문서가 다루는 것
- `Run Atlas` route node closing evidence
- `Battle Table` stakes plaque closing evidence
- `Overrun Oath` banner/seal closing evidence
- `hook lock -> density lock -> starter asset lock` 3단계 종료선
- role별 제출물, fail 조건, tracker/run log에 남길 handoff 문장

### 이번 문서가 의도적으로 다루지 않는 것
- 새 phase 추가
- reward/shop 전체 polish 확장
- onboarding / `overrunEvent` handoff 대체
- atlas/battle/oath 외 surface 확장
- 큰 배경 아트나 개별 일러스트 발주 계획

### 이번 단계의 핵심 판단
다음 refinement의 진짜 성공 기준은 `예쁘다`가 아니다. **세 개의 핵심 token family가 live anchor, 1440p 읽힘, starter asset, 기록 문장까지 같은 이름으로 잠겼는가**가 먼저다. 따라서 이번 closing packet은 3면(`atlas`, `battle`, `oath`)만 다루고, 나머지 UI는 의도적으로 비범위로 둔다.

---

## 2. Core structure: closing을 3단계로 나눠서 닫는 이유

| 단계 | 무엇을 잠그는가 | 왜 먼저/나중인가 | 닫혔다고 말할 수 있는 최소 증거 |
| --- | --- | --- | --- |
| Step A. Hook lock | root / state / slot / role hook | review와 CSS를 감상형 피드백에서 분리하기 위해 먼저 필요하다 | hook 목록 1개 + DOM 존재 확인 1개 |
| Step B. Density lock | 1440p first-glance hierarchy | hook 없이 리뷰하면 감상형으로 흐르고, 아트를 먼저 열면 범위가 커진다 | atlas/battle/oath 캡처 3장 + 짧은 리뷰 문장 |
| Step C. Starter asset lock | frame / badge / seal starter 패키지 | hierarchy가 잠긴 뒤에만 자산이 drift를 막는 역할을 한다 | starter asset 목록 1개 + 적용 전/후 비교 1개 |

### 왜 이 순서가 중요한가
- hook이 없으면 QA는 다시 screenshot 감상에 의존한다.
- density review가 먼저 닫히지 않으면 아트는 결국 큰 배경이나 장식부터 연다.
- starter asset이 먼저 열리면 token delivery가 아니라 art backlog로 커진다.

---

## 3. Surface별 closing 기준

### 3-1. `Run Atlas` / route node closing

#### closing goal
`current / past / shop / boss / upcoming`이 단순 class 조합이 아니라 **QA와 PM이 같은 단어로 기록할 수 있는 state token**으로 남는다.

#### live anchor
- `dice-spell-standalone/src/app.js:1512` `renderRouteBoard()`
- `dice-spell-standalone/src/app.js:2027` route board 호출부
- `dice-spell-standalone/src/styles.css:2396` `.run-route-board`
- `dice-spell-standalone/src/styles.css:2402` `.run-route-node`

#### Step A. Hook lock 완료선
- route board root에 `data-run-surface="atlas"`가 존재한다.
- 각 node는 `data-route-node-state="current|past|shop|boss|upcoming"` 또는 동등한 state 이름으로 읽힌다.
- next threshold 문장은 `data-route-threshold="shop|boss|none"` 또는 동등 hook으로 확인 가능하다.

#### Step B. Density lock 완료선
- 1440p 캡처에서 `current node -> 다음 shop/boss 문턱 -> route 흐름` 순서가 5초 안에 읽힌다.
- preview/log panel이 route board보다 먼저 튀지 않는다.
- review 문장은 반드시 `유지 / 약함 / 과함` 3등급 중 하나로 끝난다.

#### Step C. Starter asset lock 완료선
- `Route Node Starter`에 `current / past / shop / boss` 4상태 차이와 연결 인장이 포함된다.
- atlas backplate나 큰 배경 없이도 token 차이가 유지된다.
- reward/shop card frame을 축소 복붙한 흔적이 없다.

#### fail 조건
- state 이름이 여전히 `type-normal + is-current` 조합 해석에 묶여 있어 QA가 상태를 문장으로 기록하지 못한다.
- boss/shop 차이가 텍스트를 읽기 전에는 안 보인다.
- review 문장이 `좋아 보임`, `살짝 약함` 같은 감상형 문장으로만 끝난다.

---

### 3-2. `Battle Table` / stakes plaque closing

#### closing goal
stakes 3카드가 generic info card가 아니라 **current / next / grimoire 3역할 plaque**로 읽힌다.

#### live anchor
- `dice-spell-standalone/src/app.js:2140` `renderBattle(summary)`
- `dice-spell-standalone/src/app.js:2172`~`2182` stakes 3카드 마크업
- `dice-spell-standalone/src/styles.css:2336` `.battle-stakes-card`
- `dice-spell-standalone/src/styles.css:2536` `.battle-stakes-card.current`

#### Step A. Hook lock 완료선
- battle root에 `data-run-surface="battle-table"`가 존재한다.
- 각 stakes 카드는 `data-stakes-slot="current|next|grimoire"` 또는 동등 slot 이름으로 읽힌다.
- route hint copy는 battle action helper와 drift하지 않게 같은 hook/field 기준으로 확인 가능하다.

#### Step B. Density lock 완료선
- 1440p 캡처에서 `current stakes plaque`가 battle header extension으로 읽힌다.
- stakes 3카드가 action panel을 가리지 않는다.
- `current > next > grimoire`의 우선순위가 title/spacing/contrast로 보인다.

#### Step C. Starter asset lock 완료선
- `Stakes Plaque Starter`는 `current / next / grimoire` 3프레임만으로 충분하다.
- plaque frame이 library hero plate나 reward card frame과 구분된다.
- 몬스터 카드/주사위 영역 전체 리페인트 없이도 stakes 역할이 유지된다.

#### fail 조건
- 3개 card가 아무 정보 카드 3장처럼 보인다.
- current stakes가 subtitle처럼 약하거나, 반대로 action panel보다 과하게 튄다.
- grimoire slot이 battle 전략 맥락이 아니라 reward 예고 문구처럼 읽힌다.

---

### 3-3. `Overrun Oath` / banner & seal closing

#### closing goal
ending이 reward/shop의 큰 버전이 아니라 **banner / result / carry** 3층 구조로 읽힌다.

#### live anchor
- `dice-spell-standalone/src/app.js:2435` `renderEnding(summary)`
- `dice-spell-standalone/src/app.js:2473` `.ending-banner`
- `dice-spell-standalone/src/app.js:2479` `.ending-banner-pill`
- `dice-spell-standalone/src.styles.css:2564` `.ending-banner`
- `dice-spell-standalone/src/styles.css:2577` `.ending-banner-pill`
- `dice-spell-standalone/src/styles.css:2582` 이후 ending grid

#### Step A. Hook lock 완료선
- ending root에 `data-run-surface="overrun-oath"`가 존재한다.
- banner / result / carry block은 `data-oath-role="banner|result|carry"` 또는 동등 role 이름으로 읽힌다.
- overrun CTA는 `data-overrun-cta` 또는 동등 hook으로 banner/result와 구분된다.

#### Step B. Density lock 완료선
- 1440p 캡처에서 main banner가 side card보다 항상 먼저 읽힌다.
- carry-forward 정보는 banner 보조층으로 보이고 main banner와 경쟁하지 않는다.
- reward-grid나 side card가 main result처럼 보이지 않는다.

#### Step C. Starter asset lock 완료선
- `Oath Banner Starter`는 `main banner + seal pill + side accent`까지만 포함한다.
- ending helper family와 `overrunEvent` scene card family가 같은 frame으로 보이지 않는다.
- 큰 배경 아트 없이도 `문턱 장면` 읽힘이 유지된다.

#### fail 조건
- ending이 reward/shop summary의 큰 버전처럼 읽힌다.
- side card가 banner보다 강하게 튀어 main hierarchy를 잡아먹는다.
- seal/badge가 route node boss 배지와 거의 같은 역할로 재사용된다.

---

## 4. Role-specific handoff points

## 4-1. 프로그래머에게 넘길 것

### 이번 단계 제출물
| 제출물 | 최소 내용 | 끝났다고 말할 수 있는 기준 |
| --- | --- | --- |
| hook manifest | atlas/battle/oath root/state/slot/role hook 목록 | QA가 selector 없이 state/slot/role을 같은 단어로 기록할 수 있다 |
| DOM diff note | 어떤 함수에 어떤 hook을 붙였는지 3~5줄 | markup 범위가 atlas/battle/oath 밖으로 번지지 않았다 |
| sanity proof | 최소 DOM 캡처 또는 inspector 증거 1묶음 | CSS 전에도 hook 존재가 확인된다 |

### 프로그래머 DoD
- `renderRouteBoard()` / `renderBattle()` / `renderEnding()` 세 함수 안에서만 hook change가 설명 가능하다.
- `summary`, `pagePlan`, `ending` truth를 새 object tree로 재정의하지 않는다.
- atlas/battle/oath 외 surface는 건드리지 않는다.

## 4-2. UI 오너에게 넘길 것

### 이번 단계 제출물
| 제출물 | 최소 내용 | 끝났다고 말할 수 있는 기준 |
| --- | --- | --- |
| density review note | atlas/battle/oath 3면 각각 `유지 / 약함 / 과함` 1줄 | 감상형 문장 없이 first-glance 기준이 남는다 |
| 1440p captures | atlas/battle/oath 각 1장 | hierarchy가 한 장씩 바로 보인다 |
| adjust note | spacing/contrast/title 중 무엇을 만졌는지 1~3줄 | role drift가 token 단위로 설명된다 |

### UI DoD
- atlas는 `current -> next threshold`가 먼저 보인다.
- battle은 `current stakes`가 action panel을 가리지 않고 먼저 읽힌다.
- oath는 `banner -> carry` 2층 구조가 유지된다.

## 4-3. 아트 오너에게 넘길 것

### 이번 단계 제출물
| 패키지 | 최소 포함물 | 끝났다고 말할 수 있는 기준 |
| --- | --- | --- |
| `Route Node Starter` | 4상태 차이 + 연결 인장 | atlas가 generic sidebar card로 후퇴하지 않는다 |
| `Stakes Plaque Starter` | current/next/grimoire 3프레임 | stakes가 header extension처럼 읽힌다 |
| `Oath Banner Starter` | main banner / seal pill / side accent | ending이 reward/shop family로 역류하지 않는다 |

### 아트 DoD
- 큰 배경 없이도 token 차이가 유지된다.
- atlas/battle/oath 세 묶음이 서로 다른 family로 보인다.
- 이번 턴에는 개별 책/몬스터 일러스트까지 열지 않는다.

## 4-4. QA / PM에게 넘길 것

### 이번 단계 제출물
| 제출물 | 최소 내용 | 끝났다고 말할 수 있는 기준 |
| --- | --- | --- |
| closing note | `hook / density / starter asset` 단계별 pass/fail 1줄 | closing 상태가 과장 없이 기록된다 |
| evidence bundle | 캡처 3장 + hook 목록 1개 + review 문장 1개 | 다음 reviewer도 같은 파일로 판단 가능하다 |
| handoff sentence | tracker/run log용 한 줄 | `거의 됨` 같은 애매한 완료 문장을 피한다 |

### QA / PM DoD
- `예뻐짐`이 아니라 `surface 역할 분리`를 기준으로 pass/fail 본다.
- hook evidence 없는 캡처-only 제출은 closing으로 간주하지 않는다.
- atlas/battle/oath 외 surface 변경이 끼어 있으면 bounded delivery 실패로 본다.

---

## 5. Closing evidence bundle

### 최소 evidence 세트
1. atlas 1440p 캡처 1장
2. battle 1440p 캡처 1장
3. oath 1440p 캡처 1장
4. root/state/slot/role hook 목록 1개
5. `유지 / 약함 / 과함` 3등급 review note 1개
6. starter asset 목록 1개
7. tracker/run log용 handoff sentence 1개

### 권장 파일 묶음
| 파일 종류 | 권장 내용 |
| --- | --- |
| `captures/` | `atlas-1440.png`, `battle-1440.png`, `oath-1440.png` |
| `notes/` | `hook-manifest.md`, `density-review.md`, `handoff-line.md` |
| `assets/` | `route-node-starter.*`, `stakes-plaque-starter.*`, `oath-banner-starter.*` |

### 이 묶음이 부족한 경우
- hook 목록이 없으면 `Step A 미완료`
- 1440p 캡처가 없으면 `Step B 미완료`
- starter asset 목록이 없으면 `Step C 미완료`
- 한 줄 handoff 문장이 없으면 `closing 기록 미완료`

---

## 6. Acceptance / fail matrix

| 체크 | 기대값 | fail 예시 | 판정 |
| --- | --- | --- | --- |
| atlas root/state hook | atlas root와 node state가 직접 읽힌다 | class 조합 해석이 필요하다 | fail |
| battle slot hook | current/next/grimoire가 slot 이름으로 남는다 | 세 카드가 같은 family info card로만 보인다 | fail |
| oath role hook | banner/result/carry가 markup 층으로 분리된다 | side card가 result를 대신한다 | fail |
| atlas density | current/next threshold가 먼저 보인다 | preview/log가 먼저 튄다 | fail |
| battle density | current stakes가 action panel을 가리지 않고 먼저 읽힌다 | stakes가 너무 약하거나 너무 큼 | fail |
| oath density | banner가 side card보다 먼저 읽힌다 | reward-grid가 더 강하게 튄다 | fail |
| starter asset scope | 3 starter 묶음만 제출된다 | 큰 배경/전체 리페인트까지 열린다 | fail |
| closing sentence | bounded stage 문장으로 기록된다 | `UI polish 진행`, `거의 완료` | fail |

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 문서 기준 대응 |
| --- | --- | --- |
| delivery order는 있어도 closing language가 없으면 `거의 됨` 상태가 반복될 수 있다 | implementer와 reviewer가 서로 다른 완료선을 잡는다 | 단계별 `Hook/Density/Starter asset` 완료선과 handoff sentence를 먼저 고정한다 |
| review가 다시 감상형으로 흐르면 token 차이가 취향 문제처럼 남는다 | 다음 패스에서 generic polish로 역류한다 | review 문장을 `유지 / 약함 / 과함` 3등급으로 제한한다 |
| asset 제출 범위가 열리면 큰 배경/일러스트부터 시작할 수 있다 | bounded token delivery가 art backlog로 커진다 | starter 3묶음만 closing asset으로 인정한다 |
| 캡처는 있는데 hook evidence가 없으면 다음 drift를 빨리 못 잡는다 | 마크업 drift가 늦게 발견된다 | hook manifest 없는 캡처-only 제출은 pass로 보지 않는다 |

---

## 8. Immediate next actions

1. `surface handoff -> token anchor -> token delivery workboard -> token closing packet`을 Run Table 읽기 스택으로 고정한다.
2. 프로그래머는 atlas/battle/oath hook manifest를 먼저 만든다.
3. UI 오너는 1440p 캡처 3장과 `유지 / 약함 / 과함` review note를 남긴다.
4. 아트 오너는 starter asset 3묶음만 제출한다.
5. QA/PM은 `hook / density / starter asset` 세 단계 중 어디까지 닫혔는지 한 줄 handoff 문장으로 기록한다.

---

## 9. 한 장 결론
지금 남은 공백은 더 이상 `무슨 token을 써야 하지?`도 `누가 어디부터 시작하지?`도 아니다. 이제 남은 공백은 **`무엇이 제출되면 Run Table token delivery가 실제로 닫혔다고 말할 수 있는가`**다. 이 문서는 atlas/battle/oath 3면의 closing evidence, fail 기준, role별 제출물, 기록 문장을 같은 source-of-truth로 고정해 다음 refinement가 다시 애매한 `거의 완료` 상태로 남지 않게 만드는 bounded closing packet이다.
