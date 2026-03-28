# DiceSpell `Run Table` Token Delivery Workboard

## 3줄 요약
- 이 문서는 `surface handoff packet`과 `token anchor packet` 다음 단계에서 남아 있던 마지막 실무 공백인 **누가 어느 파일/표면부터 건드려야 하는가**를 production workboard 형태로 고정한다.
- 읽는 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이다. 새 메카닉 제안이 아니라 `Run Atlas / Battle Table / Overrun Oath` 3면의 토큰 전달 순서와 제출 묶음을 정리하는 문서다.
- source-of-truth는 이 문서, 빠른 브라우징용 companion은 `docs/dicespell_game_flow_token_delivery_workboard_summary_kr.md`, 선행 계약은 `docs/dicespell_game_flow_surface_handoff_packet_kr.md`와 `docs/dicespell_game_flow_token_anchor_packet_kr.md`다.

## 한 줄 목표
`route node / stakes plaque / oath banner`를 **말로만 중요한 토큰**으로 남기지 않고, 실제 `app.js` / `styles.css` / QA evidence / starter asset 패키지 순서까지 같은 workboard 언어로 잠근다.

## 왜 이 문서가 필요한가
지금까지의 Run Table 문서 묶음은 방향과 토큰 이름을 충분히 고정했다. 하지만 실제 다음 refinement 직전에는 아직 아래 공백이 남아 있다.

- 프로그래머는 `좋아, hook을 붙이자`까지는 알지만 **어느 함수부터 어떤 순서로 건드려야 closing 범위가 안 커지는지**를 다시 조합해야 한다.
- UI 오너는 `atlas / battle / oath가 달라 보여야 한다`는 결론은 받았지만, **1440p 기준으로 어느 밀도 리뷰를 먼저 돌려야 하는지**가 한 장에 모여 있지 않다.
- 아트 오너는 starter asset 패키지 이름은 받았지만, **이번 턴에 어떤 패키지만 제출하면 충분한지**가 아직 뚜렷하지 않다.
- QA/PM은 `토큰이 살아 있다`를 확인해야 하지만, 실제로는 **어떤 DOM hook / 어떤 캡처 / 어떤 한 줄 증거**가 있으면 pass인지가 필요하다.

즉 지금 남은 병목은 `무슨 토큰인가`가 아니라 **`누가 어디부터 착수해도 bounded하게 끝낼 수 있는가`**다. 이 문서는 그 실행 순서를 고정한다.

---

## 먼저 무엇을 읽어야 하는가

| 역할 | 먼저 볼 문서 | 그 다음 | 이 문서에서 특히 볼 섹션 |
| --- | --- | --- | --- |
| 프로그래머 | `docs/dicespell_game_flow_token_anchor_packet_kr.md` | 이 문서 | 2, 3, 5, 8 |
| UI 오너 | `docs/dicespell_game_flow_token_anchor_summary_kr.md` | 이 문서 → prototype board | 4, 6, 8 |
| 아트 오너 | summary | 이 문서 → prototype board | 4, 6, 7 |
| QA / PM | summary | 이 문서 | 3, 7, 8, 9 |

---

## 1. 범위 선언

### 이번 문서가 다루는 것
- `Run Atlas` route node token delivery 순서
- `Battle Table` stakes plaque delivery 순서
- `Overrun Oath` banner / seal delivery 순서
- `app.js` / `styles.css` 기준 첫 절개 anchor
- owner별 제출 묶음, review 질문, 금지 범위

### 이번 문서가 의도적으로 다루지 않는 것
- 새 phase 추가
- `overrunEvent` / 온보딩 packet 대체
- reward/shop 추가 polish 전체
- 큰 배경 일러스트 발주 계획
- 개별 책/몬스터 카피 수정

### 이번 단계의 핵심 판단
다음 refinement의 성공 기준은 `더 화려해졌는가`가 아니다. **surface 차이를 만드는 최소 토큰이 실제 markup / CSS / evidence에 동시에 꽂혔는가**가 먼저다. 따라서 이번 범위는 꼭 3면(`atlas`, `battle`, `oath`)만 다루고, 나머지 surface는 의도적으로 건드리지 않는다.

---

## 2. 현재 live anchor와 이번 절개 지점

| surface | 현재 live anchor | 현재 보이는 장점 | 아직 남은 실무 공백 |
| --- | --- | --- | --- |
| `Run Atlas` | `dice-spell-standalone/src/app.js:1512` `renderRouteBoard()` / `:1988` `renderRightPanel()` / `src/styles.css:2396` `.run-route-board`, `:2402` `.run-route-node` | route board 자체는 이미 들어가 있고 `current / shop / boss` 색 분기는 있다 | state가 class 조합에 묶여 있어 QA hook과 token state 이름이 아직 같은 payload에서 내려오지 않는다 |
| `Battle Table` | `src/app.js:2140` `renderBattle()` / `src/styles.css:2336` `.battle-stakes-card`, `:2530` `.battle-stakes-grid` | stakes 3카드가 battle header extension처럼 이미 붙어 있다 | `current / next / grimoire` 3슬롯이 display는 있으나 slot hook / density evidence / art starter 분리가 아직 없다 |
| `Overrun Oath` | `src/app.js:2435` `renderEnding()` / `src/styles.css:2564` `.ending-banner`, `:2235` `.ending-main-column`, `:2236` `.ending-side-column` | ending은 이미 banner + 2컬럼으로 갈라져 reward/shop과 다른 방향을 잡았다 | `banner / result / carry` 역할 분리가 DOM hook과 owner evidence로 잠기지 않아 다음 polish에서 side card family가 다시 섞일 수 있다 |

### 공통 해석
- 지금 상태는 `방향 없음`이 아니라 **half-locked** 상태다.
- 즉, 새로 큰 UI를 발명할 필요는 없고 **현재 구현 위에 역할 이름과 evidence 이름을 덧씌우는 작업**이 가장 값이 크다.

---

## 3. Core structure: 이번 delivery를 3컷으로 나누는 이유

### Cut A. Hook lock
먼저 DOM/QA hook을 붙여 CSS 변경과 검수 기준을 분리한다.

### Cut B. Density lock
그 다음 1440p first-glance 기준으로 `무엇이 먼저 보여야 하는가`만 확인한다.

### Cut C. Starter asset lock
마지막으로 frame/badge/seal starter 패키지만 닫아 surface 차이를 오래 유지한다.

### 왜 이 순서인가
- hook 없이 density review를 하면 결국 감상형 피드백으로 돌아간다.
- density 기준 없이 아트를 먼저 시작하면 큰 배경이나 장식부터 키우게 된다.
- starter asset을 먼저 크게 열면 bounded refinement가 아니라 art backlog가 된다.

---

## 4. Surface별 delivery order

### 4-1. `Run Atlas` / route node

#### 이번 슬롯의 목표
`current / past / shop / boss / upcoming`이 visual class만이 아니라 **QA가 찍을 수 있는 상태 이름**으로도 남게 한다.

#### delivery 순서
1. `renderRouteBoard()` root에 `data-run-surface="atlas"`를 붙인다.
2. 각 node에 `data-route-node-state`와 필요 시 `data-route-threshold`를 붙일 준비를 한다.
3. 1440p 캡처에서 `current -> next shop/boss -> route 흐름`이 실제 first glance 순서인지 review한다.
4. 아트는 `Route Node Starter`만 납품하고, atlas backplate는 후순위로 둔다.

#### 프로그래머 handoff
- `pageTypeMeta()`와 current/past 계산 결과를 markup state 이름으로 직결한다.
- state 이름은 `current|past|shop|boss|upcoming`처럼 QA 문장으로 읽히게 유지한다.
- `renderRightPanel()`의 next summary와 route node 상태가 다른 어휘를 쓰지 않게 한 helper 결과에서 파생한다.

#### UI handoff
- route node는 작은 카드 묶음이 아니라 `보드의 핀`처럼 보여야 한다.
- `preview list`와 `log panel`이 route board보다 먼저 눈에 들어오면 실패다.

#### 아트 handoff
- 제출 범위는 `current / past / shop / boss` 4상태 차이 + 연결 인장 정도면 충분하다.
- 이번 턴에 지도 일러스트나 상세 배경까지 열지 않는다.

---

### 4-2. `Battle Table` / stakes plaque

#### 이번 슬롯의 목표
stakes 3카드가 그냥 예쁜 정보 카드가 아니라 **current / next / grimoire 3역할 plaque**로 읽히게 한다.

#### delivery 순서
1. `renderBattle()` root에 `data-run-surface="battle-table"`을 붙일 준비를 한다.
2. 각 stakes 카드에 `data-stakes-slot="current|next|grimoire"`를 붙일 준비를 한다.
3. 1440p 캡처에서 stakes 3카드가 action panel을 가리지 않으면서도 battle 진입 첫 시선을 가져가는지 확인한다.
4. 아트는 `Stakes Plaque Starter` 3종 frame만 먼저 닫는다.

#### 프로그래머 handoff
- `routeHint`, `nextShop`, `nextBoss`, `summary.grimoireTitle` 계열이 서로 다른 helper에서 drift하지 않게 정규화한다.
- slot 이름은 visual class가 아니라 검수 anchor로 유지한다.
- battle 로직/판정 코드는 건드리지 않고 view layer 범위만 유지한다.

#### UI handoff
- stakes는 `header extension`이지 독립 미니 대시보드가 아니다.
- 전투 메인 인터랙션을 가리면 안 되며, 반대로 너무 약해져 subtitle처럼 보여도 실패다.

#### 아트 handoff
- current / next / grimoire plaque 3종만 있어도 충분하다.
- 몬스터 카드/주사위 영역 전체 리페인트는 이번 턴 범위가 아니다.

---

### 4-3. `Overrun Oath` / banner & seal

#### 이번 슬롯의 목표
ending이 reward/shop의 큰 버전이 아니라 **결과 배너 + carry-forward 인장 + side support** 3층으로 읽히게 한다.

#### delivery 순서
1. `renderEnding()` root에 `data-run-surface="overrun-oath"`를 붙일 준비를 한다.
2. `ending-banner`, 결과 블록, carry 영역에 `data-oath-role="banner|result|carry"`를 나눌 준비를 한다.
3. 1440p 캡처에서 main banner가 side card보다 항상 먼저 읽히는지 확인한다.
4. 아트는 `Oath Banner Starter`의 `main banner + seal pill + side accent`까지만 제출한다.

#### 프로그래머 handoff
- victory/failure와 carry-forward 문장을 같은 결과 payload에서 뽑더라도 역할 블록은 분리한다.
- side column 카드가 많아져도 `banner`의 위계를 떨어뜨리지 않게 markup을 유지한다.
- `overrunEvent`용 scene card family와 ending helper family를 섞지 않는다.

#### UI handoff
- 메인 배너가 결과를, side 영역이 다음 권 준비를 맡는 2층 구조를 유지한다.
- `reward-grid`류 카드가 배너보다 더 강하게 튀면 실패다.

#### 아트 handoff
- oath tone은 `seal / band / carry accent` 중심으로만 먼저 닫는다.
- 크게 드라마틱한 배경 일러스트보다 `문턱 장면` 느낌의 frame이 우선이다.

---

## 5. Role-specific handoff points

## 5-1. 프로그래머에게 넘길 것

### 지금 바로 해야 하는 일
| 순서 | 대상 | 해야 할 일 | 끝났다고 말할 수 있는 조건 |
| --- | --- | --- | --- |
| 1 | `renderRouteBoard()` / `renderRightPanel()` | atlas surface/state hook 도입 | route board root와 node state가 DOM에서 직접 읽힌다 |
| 2 | `renderBattle()` | stakes slot hook 도입 | current/next/grimoire 3슬롯을 QA가 selector 없이 눈으로 확인 가능하다 |
| 3 | `renderEnding()` | oath role hook 도입 | banner/result/carry 역할이 markup 층으로 분리된다 |
| 4 | 최소 smoke 또는 DOM snapshot | root / state / slot / role 존재 확인 | UI polish 전에도 reviewer가 drift를 잡을 수 있다 |

### 금지 범위
- helper 도입을 핑계로 `pagePlan`, `summary`, `ending` truth를 새 object tree로 재정의하지 않는다.
- atlas/battle/oath 범위를 넘어 reward/shop/primer까지 한 번에 묶지 않는다.
- CSS class rename 대규모 정리는 이번 workboard 범위가 아니다.

## 5-2. UI 오너에게 넘길 것

### 지금 바로 해야 하는 일
- 1440p 기준 first-glance review를 atlas/battle/oath 3면만 따로 돈다.
- 리뷰 질문은 `무엇이 예쁜가`가 아니라 `무엇이 먼저 보이는가`로 고정한다.
- review 결과는 `유지 / 약함 / 과함` 3등급으로 남기고 문장 길이를 줄인다.

### 권장 기록 형식
- `Atlas: current node는 보이나 next boss threshold가 preview list에 묻힘`
- `Battle: stakes는 보이지만 action panel 대비 current plaque contrast가 약함`
- `Oath: banner 우선순위는 살아 있으나 side accent가 reward card family와 너무 가까움`

## 5-3. 아트 오너에게 넘길 것

### 이번 턴 납품 단위
| 패키지 | 최소 포함물 | 이번 턴 DoD |
| --- | --- | --- |
| `Route Node Starter` | current/past/shop/boss 상태 차이, 연결 인장 | atlas가 generic sidebar card로 안 읽힌다 |
| `Stakes Plaque Starter` | current/next/grimoire 3프레임 | battle stakes가 header subtitle처럼 약해지지 않는다 |
| `Oath Banner Starter` | main banner, seal pill, side accent | ending이 reward/shop family로 역류하지 않는다 |

### 아직 열지 않을 것
- 큰 배경 아트
- 책/몬스터 개별 일러스트 재작업
- reward/shop 전체 shell 재정리

## 5-4. QA / PM에게 넘길 것

### pass/fail 질문
1. 텍스트를 읽기 전에도 atlas/battle/oath의 역할이 토큰 차이로 보이는가?
2. DOM hook 이름과 실제 시각 역할이 서로 같은 단어를 가리키는가?
3. 1440p 캡처에서 side panel/side card가 핵심 토큰보다 먼저 튀지 않는가?
4. starter asset이 없어도 hook + hierarchy만으로 이미 surface 차이가 살아 있는가?

### 증거 묶음 최소 기준
- atlas 캡처 1장
- battle 캡처 1장
- oath 캡처 1장
- root/state/slot/role hook 목록 1개
- 리뷰 결론 3~5줄 1개

---

## 6. 1440p density review board

| surface | 먼저 봐야 하는 것 | 흔한 실패 | 이번 단계 권장 수정 |
| --- | --- | --- | --- |
| Atlas | current node, next threshold | preview/log가 board보다 강함 | node contrast와 threshold summary 위계 보강 |
| Battle | current stakes plaque | stakes가 부제처럼 약함 또는 action panel 가림 | card 크기보다 title/spacing/contrast 먼저 조정 |
| Oath | main banner | side card가 메인처럼 튐 | banner top hierarchy 우선 고정, side accent는 보조로 낮춤 |

### review를 짧게 유지하는 이유
이번 단계는 정교한 미감 토론이 아니라 **다음 구현자가 더 넓은 surface를 건드리지 않게 만드는 안전장치**다. 따라서 density review도 atlas/battle/oath 3면만 돌고 끝내는 것이 맞다.

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 문서 기준 대응 |
| --- | --- | --- |
| hook 이름은 생겼는데 실제 작업 순서가 없으면 implementer가 atlas/battle/oath를 한 번에 넓게 고칠 수 있다 | bounded refinement가 다시 generic polish나 미니 리팩터링으로 퍼진다 | 이 문서의 `Hook lock -> Density lock -> Starter asset lock` 순서를 범위 선언으로 먼저 고정한다 |
| UI review가 감상형으로 흐르면 토큰 차이가 다시 색상 취향 토론으로 흘러간다 | `역할 분리` 대신 `예뻐 보임`만 남는다 | review 문장을 first-glance 기준 3등급 기록으로 제한한다 |
| 아트가 큰 배경부터 시작하면 surface 차이를 오래 잠그는 데 실패한다 | 핵심 token family는 늦게 닫히고, 반복 수정 때 다시 흔들린다 | starter asset 3묶음만 먼저 제출하고 background/passive art는 후순위로 둔다 |
| QA 증거가 캡처만 있고 hook evidence가 없으면 다음 drift를 빨리 못 잡는다 | class rename이나 spacing 변경 후 역할 붕괴를 늦게 발견한다 | 캡처 3장과 root/state/slot/role hook 목록 1개를 항상 같이 남긴다 |

---

## 8. Immediate next actions

1. `surface handoff packet -> token anchor packet -> token delivery workboard` 3문서를 Run Table 기준 읽기 스택으로 고정한다.
2. 프로그래머는 `renderRouteBoard()` / `renderBattle()` / `renderEnding()`에 root/state/slot/role hook 도입 범위만 먼저 선언한다.
3. UI 오너는 atlas/battle/oath 3면만 1440p density review 1회를 돌린다.
4. 아트 오너는 `Route Node Starter` / `Stakes Plaque Starter` / `Oath Banner Starter` 3묶음만 납품 대상으로 잡는다.
5. QA/PM은 `예뻐졌는가` 대신 `텍스트 읽기 전 surface 역할이 보이는가`를 기록 문장으로 남긴다.

---

## 9. 한 장 결론
이번 공백은 더 이상 `무슨 토큰을 써야 하지?`가 아니다. 이제 남은 공백은 **`그 토큰을 누가 어디부터 실제 전달물로 잠그는가`**다. 이 문서는 Run Table 후속 작업을 atlas/battle/oath 3면의 hook, density, starter asset, evidence 묶음으로 다시 좁혀, 다음 refinement가 다시 generic polishing으로 번지지 않게 만드는 bounded workboard다.
