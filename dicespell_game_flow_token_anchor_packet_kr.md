# DiceSpell `Run Table` Token & QA Anchor Packet

## 3줄 요약
- 이 문서는 `Run Table` UI 패스 다음 단계에서 가장 먼저 닫아야 하는 공백인 **핵심 시각 토큰 + DOM/QA anchor 계약**을 한 장으로 고정한다.
- 읽는 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이다. 새 메카닉 제안이 아니라 `route node / stakes plaque / oath banner` 3축을 어디까지 같은 언어로 잠글지에 집중한다.
- 방향 문서는 `docs/dicespell_game_flow_surface_handoff_packet_kr.md`, 빠른 브라우징용 companion은 `docs/dicespell_game_flow_token_anchor_summary_kr.md`, 시각 참조는 `docs/prototypes/dicespell_game_flow_redesign_board.html`이다.

## 한 줄 목표
`Run Atlas`, `Battle Table`, `Overrun Oath`가 다시 비슷한 카드 꾸밈으로 평준화되지 않도록, **토큰 이름·DOM hook·검수 질문·아트 납품 단위**를 동일한 source-of-truth로 고정한다.

## 왜 이 문서가 필요한가
`docs/dicespell_game_flow_surface_handoff_packet_kr.md`가 5개 surface family의 목적과 경계를 잘 정리했지만, 실제 다음 refinement 직전에는 아직 아래 공백이 남아 있다.

- 프로그래머는 `어디에 data hook을 꽂아야 smoke와 수동 QA가 같은 surface를 보게 되는지`를 다시 정해야 한다.
- UI 오너는 `route board / stakes / oath`의 강조 차이를 말로는 알지만, 어떤 토큰 묶음이 같은 family이고 무엇이 다른 family인지`를 다시 풀어 써야 한다.
- 아트 오너는 `우선 자산화하라`는 방향은 받았지만, **지금 필요한 것이 정확히 어떤 작은 패키지인지**를 한 장에서 보기 어렵다.
- QA/PM은 `보기 좋아졌다`가 아니라 `surface 역할이 토큰 단위로 분리돼 있는가`를 어떤 관찰 포인트로 pass/fail 볼지`가 필요하다.

이번 문서는 그 공백을 줄이기 위해 **핵심 토큰 3종**, **권장 DOM/QA anchor**, **1440p 읽힘 기준**, **role별 handoff 묶음**을 production 문장으로 다시 고정한다.

---

## 먼저 무엇을 읽어야 하는가

| 역할 | 먼저 볼 문서 | 그 다음 | 이 문서에서 특히 볼 섹션 |
| --- | --- | --- | --- |
| 프로그래머 | 이 문서 | `docs/dicespell_game_flow_surface_handoff_packet_kr.md` | 2, 4, 6, 8 |
| UI 오너 | `docs/dicespell_game_flow_token_anchor_summary_kr.md` | 이 문서 → prototype board | 3, 5, 7 |
| 아트 오너 | `docs/dicespell_game_flow_token_anchor_summary_kr.md` | 이 문서 → prototype board | 3, 5, 7 |
| QA / PM | summary | 이 문서 | 4, 6, 8, 9 |

---

## 1. 이번 문서의 범위 선언

### 이번 문서가 다루는 것
- `Run Atlas`의 route node token
- `Battle Table`의 stakes plaque token
- `Overrun Oath`의 banner / seal token
- 위 3축을 검수하기 위한 DOM/QA anchor와 density 질문
- role별 바로 전달 가능한 bounded handoff 묶음

### 이번 문서가 의도적으로 다루지 않는 것
- 새 phase 추가
- 새 reward/event/mechanic 정의
- `overrunEvent` / onboarding primer 문서 묶음 대체
- route board 전체 재설계
- 개별 몬스터/책 일러스트 리워크 계획

### 핵심 판단
지금 `Run Table` 패스의 제일 큰 리스크는 `디자인이 약하다`가 아니라 **표면상 다른 surface가 다시 같은 카드 family로 보이기 시작하는 것**이다. 따라서 다음 30분~1스프린트는 새 화면을 더 늘리기보다 **차이를 만드는 최소 토큰과 검수 anchor를 먼저 닫는 것**이 맞다.

---

## 2. 이번 슬롯의 우선 잠금 대상

| 우선순위 | 토큰 family | 대상 surface | 지금 먼저 잠가야 하는 이유 |
| --- | --- | --- | --- |
| P1 | `route node token set` | `Run Atlas` | 현재/과거/상점/보스 차이가 copy만으로 읽히면 다음 CSS 수정에서 바로 약해진다 |
| P1 | `stakes plaque set` | `Battle Table` | 전투가 다시 단순 HUD처럼 보이지 않게 하는 최소 차별점이다 |
| P1 | `oath banner set` | `Overrun Oath` | ending이 reward/shop 카드 family로 평준화되는 것을 막는 마지막 표식이다 |
| P2 | `hero accent carry-through` | `Choose Your Tome` | library hero tone은 중요하지만 현재 blocker는 아니다 |
| P2 | `curation shell trim` | `Run Curation` | reward/shop은 이미 family가 잡혀 있어 2차 정리로 충분하다 |

### 이번 단계의 핵심 원칙
- **token은 자산 이름이자 검수 이름**이어야 한다.
- **DOM anchor는 완성 UI가 아니라도 먼저 심을 수 있어야 한다.**
- **1440p에서 첫 시선이 갈 위치**가 토큰 배치 기준보다 우선한다.

---

## 3. Core structure: 토큰 3종을 어떻게 다르게 읽히게 할 것인가

### 3-1. Route node token set (`Run Atlas`)

#### 존재 이유
플레이어가 오른쪽 패널을 보는 즉시 `현재 위치 / 다음 상점 / 다음 보스`를 list parsing 없이 읽게 만든다.

#### 토큰 구성
| 토큰 | 의미 | 최소 시각 차이 | 금지 |
| --- | --- | --- | --- |
| `route-node-current` | 지금 서 있는 페이지 | 가장 강한 윤곽 + 현재성 강조 | reward card 강조 패턴 재사용 금지 |
| `route-node-past` | 지난 페이지 | 감쇠된 plate / 저채도 | current와 같은 강조도 금지 |
| `route-node-shop` | 다음 경제 문턱 | 상점용 작은 배지 또는 인장 | curation card 전체를 축소 복붙 금지 |
| `route-node-boss` | 다음 큰 승부처 | 보스 전용 배지 + 강한 윤곽 | ending oath seal과 동일 시그널 금지 |
| `route-link-active` | 현재에서 다음으로 이어지는 경로 | 끊기지 않는 연결감 | 배경 장식처럼 묻히는 처리 금지 |

#### 현재 구현 anchor
- `renderRouteBoard(pagePlan, currentPage, options)`
- 관련 클래스: `run-route-board`, `run-route-node`

#### 권장 DOM/QA hook
| hook 제안 | 위치 | 목적 |
| --- | --- | --- |
| `data-run-surface="atlas"` | route board root | 수동/자동 QA에서 surface 기준점 고정 |
| `data-route-node-state="current|past|shop|boss|upcoming"` | route node | 상태 분리 확인 |
| `data-route-threshold="shop|boss|none"` | next threshold summary | 다음 문턱 문장 drift 방지 |

---

### 3-2. Stakes plaque set (`Battle Table`)

#### 존재 이유
전투 시작 직후 플레이어가 `지금 페이지 stakes`와 `다음 문턱`을 HUD parsing보다 먼저 읽게 만든다.

#### 토큰 구성
| 토큰 | 의미 | 최소 시각 차이 | 금지 |
| --- | --- | --- | --- |
| `stakes-plaque-current-page` | 현재 페이지 압력 | 가장 먼저 보이는 plaque | library hero title plate처럼 길게 설명하는 패턴 금지 |
| `stakes-plaque-next-threshold` | 다음 상점/보스 | 보조 plaque지만 즉시 구분 가능 | route node를 그대로 확대 복붙 금지 |
| `stakes-plaque-grimoire-axis` | 현재 책 운영 축 | 이번 런의 전략 맥락 | reward/shop sidebar 설명문처럼 약화 금지 |

#### 현재 구현 anchor
- `renderBattle(summary)`
- 관련 클래스: `battle-stakes-grid`, `battle-stakes-card`

#### 권장 DOM/QA hook
| hook 제안 | 위치 | 목적 |
| --- | --- | --- |
| `data-run-surface="battle-table"` | battle root | surface 기준점 고정 |
| `data-stakes-slot="current|next|grimoire"` | 3개 stakes card | card 순서/밀도 drift 방지 |
| `data-route-hint` | hint copy container | copy QA와 smoke 고정 포인트 |

---

### 3-3. Oath banner set (`Overrun Oath`)

#### 존재 이유
엔딩을 reward/shop 계열 카드의 연장선이 아니라 `이번 권을 봉인하고 다음 권 문턱을 마주하는 장면`으로 읽히게 만든다.

#### 토큰 구성
| 토큰 | 의미 | 최소 시각 차이 | 금지 |
| --- | --- | --- | --- |
| `oath-banner-main` | 이번 권 결과 배너 | 가장 큰 상단 표식 | market tray 톤 재사용 금지 |
| `oath-seal-pill` | carry-forward / status 인장 | 배너 보조 표식 | route node boss 배지와 동일 처리 금지 |
| `oath-side-card` | 다음 권 제안/보관함/보조 정보 | 메인 배너보다 낮은 위계 | reward choice card를 그대로 main tone으로 승격 금지 |

#### 현재 구현 anchor
- `renderEnding(summary)`
- 관련 클래스: `ending-banner`, `ending-banner-pill`, `ending-main-column`, `ending-side-column`

#### 권장 DOM/QA hook
| hook 제안 | 위치 | 목적 |
| --- | --- | --- |
| `data-run-surface="overrun-oath"` | ending root | surface 기준점 고정 |
| `data-oath-role="banner|result|carry"` | ending blocks | result vs carry-forward 위계 분리 확인 |
| `data-overrun-cta` | continue button area | CTA 문맥 drift 방지 |

---

## 4. 1440p readability contract

### 첫 시선 우선순위
| surface | 1순위 | 2순위 | 3순위 |
| --- | --- | --- | --- |
| Run Atlas | current / next threshold token | route 흐름 | nearby list / logs |
| Battle Table | current stakes plaque | next threshold plaque | battle action UI |
| Overrun Oath | main oath banner | carry-forward seal/card | 상세 정산/보조 설명 |

### 실패 예시
- `Run Atlas`에서 로그 블록이 route board보다 먼저 눈에 들어온다.
- `Battle Table`에서 stakes card가 주문/주사위 패널보다 더 작거나 약해서 그냥 부제처럼 보인다.
- `Overrun Oath`에서 보관함 카드가 메인 배너보다 더 강해 보여 reward/shop screen의 변형처럼 읽힌다.

### 간단 density 기준
- route node 8개가 보여도 `current`와 `next boss/shop` 구분이 멈춤 없이 읽혀야 한다.
- stakes 3개는 header extension처럼 보이되, 전투 메인 액션을 가려서는 안 된다.
- oath banner는 첫 화면에서 `결과`와 `다음 권 제안`을 2개 층으로 분리해 보여야 한다.

---

## 5. Role-specific handoff points

## 5-1. 프로그래머에게 넘길 것

### 지금 handoff-ready인 것
- route/stakes/oath 3축의 **권장 DOM hook 이름**
- helper/markup 변경이 필요한 anchor
  - `renderRouteBoard()`
  - `renderBattle()`
  - `renderEnding()`
- `data-run-surface`, `data-route-node-state`, `data-stakes-slot`, `data-oath-role` 계열 hook 도입 우선순위

### bounded refinement
1. visual class와 별개로 DOM hook을 붙여 CSS 수정과 QA 기준점을 분리한다.
2. `next threshold` 문구와 token state를 같은 helper 결과에서 뽑아 copy drift를 줄인다.
3. smoke 또는 최소 DOM snapshot에서 `atlas / battle-table / overrun-oath` surface root 존재를 확인할 준비를 만든다.

### 금지 범위
- DOM hook 도입을 핑계로 render 구조를 대형 리팩터링하지 않는다.
- data truth를 새 object tree로 재조합하지 않는다.
- `Run Curation`, onboarding, overrunEvent 경계까지 한 번에 흡수하지 않는다.

## 5-2. UI 오너에게 넘길 것

### 지금 handoff-ready인 것
- 3개 핵심 token family의 이름과 역할
- 1440p 읽힘 기준
- first-glance 우선순위

### bounded refinement
1. `route node = 지도 토큰`, `stakes plaque = 현재 판 압력`, `oath banner = 종료/문턱`의 레벨 차이를 typographic scale과 spacing으로 먼저 닫는다.
2. side panel/log/CTA가 핵심 토큰보다 앞에 튀지 않도록 hierarchy를 정리한다.
3. reward/shop 카드 family와 ending side card의 similarity를 의도적으로 낮춘다.

### 금지 범위
- 모든 차이를 border/glow 색상 하나로만 해결하지 않는다.
- `Overrun Oath`를 `Run Curation`의 큰 버전처럼 만들지 않는다.

## 5-3. 아트 오너에게 넘길 것

### 우선 자산 패키지
| 우선순위 | 패키지 | 포함물 | 왜 지금 필요한가 |
| --- | --- | --- | --- |
| P1 | Route Node Starter | current/past/shop/boss 상태 토큰 + 연결 인장 | atlas가 list UI로 후퇴하는 것을 막는다 |
| P1 | Stakes Plaque Starter | current/next/grimoire 3종 plaque frame | battle stakes를 계산 HUD 위에 올려 준다 |
| P1 | Oath Banner Starter | main banner + seal pill + side accent | ending을 reward/shop family에서 분리한다 |
| P2 | Atlas Accent Backplate | route board 바닥 표면 | token 차이를 더 오래 유지시킨다 |
| P2 | Curation Trim Pass | reward/shop 보조 shell 정리 | 2차 일관성 작업으로 충분하다 |

### 금지 범위
- 개별 카드/몬스터/책 일러스트를 이번 P1 착수 전제처럼 두지 않는다.
- banner/pill/frame보다 큰 배경 일러스트부터 시작하지 않는다.

## 5-4. QA / PM에게 넘길 것

### pass 기준 질문
1. 우측 패널에서 `지금/다음`이 list 읽기 전에 토큰으로 먼저 보이는가?
2. 전투에서 `이번 판의 압력`이 action panel보다 늦게 읽히지 않는가?
3. ending이 reward/shop 계열 화면처럼 느껴지지 않고 결과/문턱으로 분리되는가?
4. 같은 카드 markup을 일부 재사용해도 surface 목적 차이가 토큰 레벨에서 유지되는가?

### 기록 문장 권장 형식
- `Run Table 후속 패스는 route node / stakes plaque / oath banner 3축에 대해 DOM hook과 visual token 이름을 고정해, 이후 UI 수정이 다시 generic card drift로 흘러가지 않게 만드는 데 목적이 있다.`

---

## 6. Acceptance / review checklist

| 체크 | 기대값 | 실패 예시 |
| --- | --- | --- |
| Atlas surface root | atlas root와 route state가 분리되어 식별 가능 | 우측 패널 전체가 generic sidebar로만 읽힘 |
| Route token contrast | current / boss / shop 차이가 즉시 보임 | 텍스트 읽기 전엔 구분이 안 됨 |
| Stakes slot order | current > next > grimoire 순이 유지됨 | 3개 card가 아무 카드 3장처럼 보임 |
| Battle density | stakes가 header 확장처럼 보이나 action을 가리지 않음 | stakes 때문에 실제 조작 영역이 눌림 |
| Oath hierarchy | banner와 carry-forward가 2층으로 분리됨 | reward/shop summary처럼 평평하게 섞임 |
| DOM anchor stability | hook 이름이 surface 목적과 대응됨 | CSS class 이름만으로 QA를 때움 |

---

## 7. Risks & follow-ups

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| route/stakes/oath 차이를 여전히 copy와 색만으로 버티면 다음 CSS touch에서 다시 평준화될 수 있음 | surface family 계약이 빠르게 약해짐 | token 이름 + DOM hook + 최소 자산 패키지를 같은 문서로 고정 |
| QA가 여전히 screenshot 감상 위주로 끝나면 drift를 조기 발견하지 못함 | `좋아 보이지만 역할은 흐린` 상태가 통과할 수 있음 | `data-run-surface`와 slot/state hook을 수동/자동 체크 기준으로 도입 |
| 1440p 밀도 검토 없이 작은 화면 감각만으로 정리하면 right panel과 ending side column이 다시 과밀해질 수 있음 | 첫 시선 위계가 무너짐 | atlas / battle / oath 3면만 따로 density review 1회 수행 |
| 아트가 큰 배경부터 시작하면 token 차이가 늦게 닫힘 | handoff-ready가 다시 mood board 단계로 후퇴 | P1 starter 자산을 frame / badge / seal 중심으로 좁힘 |

---

## 8. Immediate next actions

1. **문서 우선**: 이 packet + summary를 기존 `Run Table handoff packet` 옆에 source+companion 쌍으로 포털에 노출한다.
2. **프로그래머 우선**: `renderRouteBoard()` / `renderBattle()` / `renderEnding()`에 최소 DOM hook 설계를 맞춘다.
3. **UI 우선**: atlas / battle / oath 3면만 따로 1440p density review를 1회 수행한다.
4. **아트 우선**: `Route Node Starter`, `Stakes Plaque Starter`, `Oath Banner Starter` 3묶음만 먼저 닫는다.
5. **QA 우선**: 첫 수동 리뷰에서 `텍스트 읽기 전에도 역할이 구분되는가`를 토큰 기준으로 기록한다.

---

## 9. 이 문서가 하지 않는 일
- `Run Table` 전체 리디자인 방향서를 대체하지 않는다.
- `overrunEvent` / onboarding owner handoff를 대체하지 않는다.
- 새 asset backlog 전체를 설계하지 않는다.
- 현재 코드에 이미 없는 메카닉을 강제로 끼워 넣지 않는다.

이 문서는 오직 **Run Table 후속 정리가 다시 generic polishing으로 흐르지 않도록, 가장 먼저 잠가야 할 token/anchor 바닥을 production handoff 단위로 고정하는 source-of-truth**다.
