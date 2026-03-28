# DiceSpell `Run Table` UI 리디자인 Surface Handoff Packet

## 3줄 요약
- 이 문서는 이미 시범 적용된 `Run Table` UI 패스를 실제 handoff 가능한 surface 계약으로 다시 고정한다.
- 읽는 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이다. 콘셉트 설명보다 `어느 surface가 무엇을 맡고 무엇은 아직 안 바뀌어야 하는가`에 집중한다.
- 첫 화면용 영감 문서는 `docs/dicespell_game_flow_redesign_packet_kr.md`, 빠른 브라우징용 companion은 `docs/dicespell_game_flow_surface_handoff_summary_kr.md`, 시각 보드 샘플은 `docs/prototypes/dicespell_game_flow_redesign_board.html`이다.

## 한 줄 목표
기존 규칙/세이브/phase를 유지한 채, 현재 빌드의 `도서관 -> 경로 -> 전투 -> 보상/상점 -> 엔딩`을 한 런의 보드처럼 읽히게 만드는 surface별 source-of-truth를 고정한다.

## 왜 이 문서가 필요한가
`docs/dicespell_game_flow_redesign_packet_kr.md`와 프로토타입 보드는 방향을 잘 설명하지만, 실제 구현/수정/리뷰 직전에는 아직 다음 질문이 남는다.

- 프로그래머는 `어떤 함수가 어떤 surface 문법을 책임지는가`를 다시 추론해야 한다.
- UI 오너는 `hero / board / curation / oath` 톤이 어디서 갈리고 어디서 재사용되는지`를 다시 정리해야 한다.
- 아트 오너는 `배경 mood`는 이해해도 실제로 어떤 panel family를 우선 자산화해야 하는지`를 다시 골라야 한다.
- QA/PM은 `예뻐졌는가`가 아니라 `런 보드형 읽힘이 실제로 살아났는가`를 어떤 기준으로 pass/fail 볼지`가 필요하다.

이번 문서는 그 공백을 메우기 위해 **현재 적용된 surface 변화**, **role별 handoff**, **금지 범위**, **다음 refinement 우선순위**를 한 장으로 다시 묶는다.

---

## 먼저 무엇을 읽어야 하는가

| 역할 | 먼저 볼 문서 | 그 다음 | 이 문서에서 특히 볼 섹션 |
| --- | --- | --- | --- |
| 프로그래머 | 이 문서 | `docs/dicespell_game_flow_redesign_packet_kr.md` → `docs/prototypes/dicespell_game_flow_redesign_board.html` | 4, 5, 6, 8 |
| UI 오너 | `docs/dicespell_game_flow_surface_handoff_summary_kr.md` | 이 문서 → 프로토타입 | 3, 5, 7 |
| 아트 오너 | `docs/dicespell_game_flow_surface_handoff_summary_kr.md` | 이 문서 → 프로토타입 | 3, 5, 7 |
| QA / PM | summary | 이 문서 | 2, 6, 9, 10 |

---

## 1. 현재 패스의 범위 선언

### 이번 패스에서 바뀐 것
- 도서관 상단을 `메뉴`가 아니라 `Choose Your Tome / Run Lobby`로 읽히게 하는 hero shell을 추가했다.
- 우측 패널을 `페이지 목록` 중심에서 `Run Atlas / Route Board` 중심으로 재배치했다.
- 전투 중앙 surface를 `Battle Table / Spell Arena`라는 stakes-first 구조로 다시 묶었다.
- 보상/상점을 공통 `Run Curation` 계열 레이아웃으로 다시 읽히게 정리했다.
- 엔딩을 `결과 정산창`보다 `Overrun Oath`에 가까운 배너 + 2컬럼 문법으로 다시 배치했다.
- top bar title/subcopy도 `Prototype Atelier`보다 `Run Table` 방향으로 재정의했다.

### 이번 패스에서 의도적으로 안 바뀐 것
- phase 구조(`library`, `battle`, `shop`, `reward`, `ending`) 자체
- 세이브/로드 계약
- overrunEvent 별도 phase handoff 문서 묶음
- 첫 런 온보딩 primer B-track 계약
- 전투 룰, 보상 계산, 오버런 계산, unlock 구조

### 이 문서의 핵심 판단
이번 패스는 **새 메카닉 제안**이 아니라 **기존 메카닉을 읽히는 방식의 재배치**다. 따라서 handoff도 `새 시스템 설계서`가 아니라 `기존 함수/화면 책임을 어떻게 재정렬했는가` 기준으로 읽어야 한다.

---

## 2. 1스크린 요약: 누가 무엇을 보면 되는가

| Surface | 지금 왜 존재하는가 | 프로그래머 handoff | UI handoff | 아트 handoff | QA/PM 체크 |
| --- | --- | --- | --- | --- | --- |
| Library / Choose Your Tome | 책 선택 직후 이번 런 기대를 세운다 | `renderGrimoireShelf()` hero + forecast + identity | 책 카드보다 먼저 `런 입구`로 읽혀야 함 | 책/양피지/예언 보드 계열 톤 | 첫 5초에 책·위험·첫 보스 기대가 읽히는가 |
| Run Atlas / Route Board | 현재 위치와 다음 문턱을 같은 보드에 묶는다 | `renderRightPanel()` + `renderRouteBoard()` | 리스트보다 route node가 먼저 읽혀야 함 | 노드/핀/경로/문턱 계열 토큰 | 현재/다음 상점/다음 보스가 즉시 읽히는가 |
| Battle Table / Spell Arena | 이번 턴의 stakes를 먼저 읽게 만든다 | `renderBattle()` stakes grid + 기존 전투 보드 | 계산 UI보다 `한 수의 무대`로 읽혀야 함 | 전장, stakes plaque, 위험 카드 | 지금 페이지 stakes와 다음 승부처가 함께 읽히는가 |
| Run Curation | 보상/상점을 같은 런 큐레이션 문법으로 묶는다 | `renderReward()` / `renderShop()` | 선택지가 `다음 페이지 변화`로 읽혀야 함 | 카드/시장/환전 패널 | 선택 전후에 왜 고르는지 이해되는가 |
| Overrun Oath | 종료가 아니라 다음 권 문턱처럼 읽힌다 | `renderEnding()` banner + side column | 결과와 carry-forward가 분리되어야 함 | 봉인, oath, carry-forward tone | 엔딩 결과와 다음 권 제안이 분리되어 읽히는가 |

---

## 3. Core Structure

### 3-1. 상위 레이아웃 문법
이번 패스의 공통 문법은 아래 4층으로 본다.

1. **Hero / Banner layer**
   - 지금 surface가 무엇인지 한 문장으로 고정한다.
   - 예: `Choose Your Tome`, `Battle Table`, `Reward Market`, `Run Cleared`
2. **Board / Stakes layer**
   - 현재 판의 문맥과 다음 문턱을 함께 보여 준다.
   - 예: route board, battle stakes cards, ending carry-forward summary
3. **Action layer**
   - 플레이어가 지금 눌러야 하는 실제 인터랙션
   - 예: 책 선택, roll/주문, reward 선택, continue overrun
4. **Memory / Log layer**
   - 최근 로그, 기록, unlock, archive 의미
   - 이 레이어는 보조여야 하며 main stakes를 가리면 안 된다.

### 3-2. 시각 톤 family

| Family | 용도 | 현재 적용 surface | 재사용 가능 범위 | 금지 |
| --- | --- | --- | --- | --- |
| `Run Lobby` | 책 선택 직후 기대감 | library hero, grimoire forecast | 책 선택 / 런 시작 입구 | battle stakes, ending banner에 그대로 복붙 금지 |
| `Run Atlas` | 페이지 경로/문턱 읽힘 | right panel route board | 경로, next threshold, page node | reward card처럼 선택 카드로 쓰지 않기 |
| `Battle Stakes` | 이번 페이지 승부 압력 | battle stakes grid | 전투 헤더/위협 강조 | library hero처럼 설명문 위주 panel로 약화 금지 |
| `Run Curation` | reward/shop 선택 문법 | reward, shop | 선택 카드, 준비 패널, infusion/boss draft | route node / ending oath와 혼합 금지 |
| `Overrun Oath` | 봉인/다음 권 제안 | ending banner + side cards | ending / overrun 문턱 | reward market, battle table에 역유입 금지 |

---

## 4. Surface별 구현 계약

## 4-1. Library / Choose Your Tome

### 존재 이유
책을 고르는 순간 `메뉴를 누른다`가 아니라 `이번 판의 성격을 고른다`가 먼저 읽혀야 한다.

### 현재 구현 anchor
- `grimoireThemeMeta(theme)`
- `buildGrimoireForecast(grimoire)`
- `renderGrimoireShelf()`
- 관련 클래스: `run-lobby-hero`, `run-overview-card`, `grimoire-forecast-grid`, `run-grimoire-card`

### 반드시 유지할 읽힘
- `책 카드`보다 `런 입구`가 먼저 보인다.
- 각 책은 최소한 `운영 축`, `테마 축`, `초반 운영`, `첫 보스/상점/후반 전리품 forecast`를 한 화면에서 읽을 수 있다.
- unlock 정보는 meta 보조 정보이지 main hero가 아니다.

### 프로그래머 handoff
- 책 데이터 구조를 늘리는 대신 **현재 grimoire metadata를 재조합하는 helper**로 유지한다.
- `forecast`는 visual read helper이지 새로운 progression truth가 아니다.
- 다음 refinement가 와도 `grimoire.playstyle`, `openingPlan`, `caution`, completion bonus 계열 current source-of-truth를 유지한다.

### UI handoff
- 카드 내부 정보량은 늘었지만 첫 시선은 여전히 `book title -> tone -> forecast -> why pick` 순이어야 한다.
- `잠금 여부`, `기록 조건`, `loadout panel`은 책 선택의 주연이 아니라 보조다.

### 아트 handoff
- 필요한 자산은 `책 카드 backplate`, `예언 타일`, `런 로비 hero 표면`의 3종이다.
- 실제 책 개별 일러스트보다 먼저 **책 선택이 의식처럼 느껴지는 보드감**이 중요하다.

### 아직 금지
- 도서관에 route board 전체를 억지로 끌어오지 않는다.
- 온보딩 primer와 hero 카피를 합쳐서 중복 설명을 만들지 않는다.

---

## 4-2. Right Panel / Run Atlas

### 존재 이유
`지금 어디쯤 왔는가`, `다음 상점/보스가 언제인가`, `현재 런 압력이 어떤가`를 같은 측면 보드에 묶는다.

### 현재 구현 anchor
- `pageTypeMeta(type)`
- `findNextPlannedPage(pagePlan, currentPage, predicate)`
- `renderRouteBoard(pagePlan, currentPage, options)`
- `renderRunPressureCards(summary)`
- `renderRightPanel(summary)`
- 관련 클래스: `run-side-panel`, `run-route-board`, `run-route-node`, `run-pressure-grid`, `forecast-item`

### 반드시 유지할 읽힘
- page preview list는 보조고, route node board가 main이다.
- `현재 위치`, `다음 상점`, `다음 보스`, `현재 압력(collapse/gold/upper/run depth)`이 한 번에 읽혀야 한다.
- right panel이 단순 로그 패널로 다시 축소되면 안 된다.

### 프로그래머 handoff
- route board는 `pagePlan`의 새로운 truth를 만들지 않는다. 현재 `pagePlan`을 요약해 보여 주는 view layer다.
- `findNextPlannedPage`는 reusable helper로 유지하되, future event/shop/boss label drift가 생기면 여기서만 정규화한다.

### UI handoff
- 우측 패널의 우선순위는 `route board -> next threshold summary -> pressure cards -> nearby pages -> logs` 순이다.
- logs가 길어져도 atlas를 밀어내면 안 된다.

### 아트 handoff
- 노드 보드 자산은 `pin/plate/seal` 톤이면 충분하다.
- 상세 지도 일러스트를 먼저 만들기보다 `current`, `past`, `boss`, `shop`의 상태 구분 토큰부터 닫는 게 우선이다.

### 아직 금지
- reward/shop 카드와 route node를 같은 card family로 통합하지 않는다.
- overrunEvent 전용 scene card를 여기에 섞지 않는다.

---

## 4-3. Battle Table / Spell Arena

### 존재 이유
전투를 계산 UI가 아니라 `이번 페이지 stakes가 걸린 승부의 테이블`로 읽히게 만든다.

### 현재 구현 anchor
- `renderBattle(summary)`
- 관련 클래스: `run-battle-stage`, `battle-stakes-grid`, `battle-stakes-card`

### 반드시 유지할 읽힘
- 상단에서 `지금 페이지 stakes`, `다음 상점/보스`, `이번 책 보상 축`이 먼저 들어온다.
- 그 다음에 주사위 테이블, 몬스터 시야, 기억 회람판이 따라온다.
- `routeHint`는 룰 설명이 아니라 현재 판 긴장을 요약하는 한 줄이어야 한다.

### 프로그래머 handoff
- battle logic은 그대로 두고 `stakes summary`만 view-side에서 계산한다.
- next boss/shop 문구는 route helper 재사용으로 맞춘다.
- 이후 refinement에서도 `전투 로직 파일 변경 없이 view copy 조정 가능`한 구조를 유지하는 것이 좋다.

### UI handoff
- stakes card 3개는 battle header의 extension이지 별도 mini-dashboard가 아니다.
- 주사위/주문/타겟 패널 가독성을 해치면 실패다.

### 아트 handoff
- 필요한 것은 `stakes plaque`, `current page pressure highlight`, `battle table backdrop`이다.
- 적 카드 자체를 새로 다 뒤엎는 것보다 stakes frame과 board tone을 먼저 닫는 게 효율적이다.

### 아직 금지
- battle surface에서 reward market 문법을 가져오지 않는다.
- route board 전체를 중앙으로 가져와 전투 집중을 깨지 않는다.

---

## 4-4. Reward / Shop = Run Curation

### 존재 이유
보상과 상점을 모두 `지금 위협을 다음 페이지 준비로 환전하는 자리`로 읽히게 만든다.

### 현재 구현 anchor
- `renderShop()`
- `renderReward(summary)`
- 관련 클래스: `run-curation-stage`, `run-curation-grid`, `run-curation-sidebar`, `run-curation-main`

### 반드시 유지할 읽힘
- reward와 shop은 다른 시스템이지만 같은 `curation` family 안에 있어야 한다.
- side summary가 `현재 골드`, `다음 보스/문턱`, `reward cue`를 먼저 잡아 준다.
- 선택 카드 자체는 기존 reward/shop card를 재사용해도 되지만, 읽힘은 `다음 승부처 준비`로 바뀌어야 한다.

### 프로그래머 handoff
- reward/shop 계산 구조는 건드리지 않고 wrapper layout을 공유한다.
- boss draft, infusion panel, offer list는 기존 data truth를 그대로 사용한다.

### UI handoff
- reward와 shop을 한 family로 묶되, `reward = 위협 환전`, `shop = 경제 환전`의 미묘한 차이는 copy와 sidebar text로 유지한다.
- CTA는 항상 선택 카드보다 덜 눈에 띄어야 한다.

### 아트 handoff
- `market tray`, `curation sidebar`, `boss draft` 정도의 공통 shell이 우선이다.
- 각각을 전혀 다른 세계관 카드처럼 만들 필요는 없고, 같은 탁자 위 다른 코너처럼 느껴지면 된다.

### 아직 금지
- reward와 shop을 완전히 같은 제목/카피 패턴으로 평준화하지 않는다.
- ending carry-forward 의미를 여기서 미리 다 말하지 않는다.

---

## 4-5. Ending / Overrun Oath

### 존재 이유
엔딩을 정산창으로 끝내지 않고 `이번 권을 봉인하고 다음 권 문턱을 마주하는 순간`처럼 읽히게 만든다.

### 현재 구현 anchor
- `renderEnding(summary)`
- 관련 클래스: `run-ending-stage`, `ending-banner`, `ending-stage-grid`, `ending-main-column`, `ending-side-column`

### 반드시 유지할 읽힘
- 결과 정산과 carry-forward 제안은 같은 화면 안에서도 역할이 분리되어야 한다.
- victory/defeat 모두 `이번 런의 의미`가 먼저 읽히고, 그 다음 `돌아간다 / 계속 간다`가 보여야 한다.
- `오버런 전리품 보관함`은 still reward-like card family를 써도, 전체 surface 읽힘은 ending/oath 쪽이어야 한다.

### 프로그래머 handoff
- ending phase는 여전히 결과/정산/CTA 의미를 맡고, overrunEvent flavor scene은 separate source-of-truth를 유지한다.
- `continue-overrun`은 문장/배너 톤이 바뀌어도 underlying overrun flow contract를 건드리지 않는다.

### UI handoff
- `main column = 이번 권의 결과`, `side column = 다음 권 제안` 구조를 유지한다.
- carry-forward card가 overrunEvent scene card처럼 과장되면 안 된다.

### 아트 handoff
- 봉인, oath, inheritance 느낌이 핵심이다.
- reward market과 다른 점은 `시장`이 아니라 `의식/문턱`처럼 읽혀야 한다는 것.

### 아직 금지
- `themeTrophy/enemyTrophy/none` flavor를 ending 본문에서 다 소모하지 않는다.
- overrunEvent scene shell을 ending에서 미리 복사해 오지 않는다.

---

## 5. Role-specific handoff points

## 5-1. 프로그래머에게 넘길 것

### 지금 handoff-ready인 것
- helper 추가만으로 surface semantics를 강화한 현재 구조
- 함수 anchor
  - `renderGrimoireShelf()`
  - `renderRightPanel()` / `renderRouteBoard()`
  - `renderBattle()`
  - `renderReward()`
  - `renderShop()`
  - `renderEnding()`
- 재사용 가능한 helper
  - `findNextPlannedPage()`
  - `pageTypeMeta()`
  - `buildGrimoireForecast()`
  - `grimoireThemeMeta()`

### 다음 구현자가 바로 할 수 있는 bounded refinement
1. helper/copy constant를 분리해 locale drift를 줄인다.
2. `Run Atlas` / `Run Curation` / `Overrun Oath` label을 component-level constant로 모아 QA snapshot 텍스트 drift를 줄인다.
3. route board / stakes / banner용 DOM test hook을 추가해 UI smoke의 기준점을 만든다.

### 프로그래머 금지 범위
- 이 패스를 핑계로 phase 구조를 새로 나누지 않는다.
- overrunEvent / onboarding packet scope를 같이 흡수하지 않는다.
- data truth를 view helper에서 새로 만들지 않는다.

## 5-2. UI 오너에게 넘길 것

### 지금 handoff-ready인 것
- surface family 5종
  - Choose Your Tome
  - Run Atlas
  - Battle Table
  - Run Curation
  - Overrun Oath
- 각 family의 정보 위계와 reuse boundary

### 다음 refinement에서 닫아야 할 것
1. top bar, hero, stakes, sidebar, card density를 1440p 기준으로 다시 한 번 정렬
2. reward/shop/ending CTA prominence를 통일
3. route node / stakes plaque / ending oath banner 간 강조 레벨 차이를 명확히 함

### UI 금지 범위
- 모든 panel family를 같은 generic glossy card로 통일하지 않는다.
- primer/overrunEvent scene/ending helper를 같은 density로 수렴시키지 않는다.

## 5-3. 아트 오너에게 넘길 것

### 우선 자산 패키지
| 우선순위 | 자산 묶음 | 왜 먼저 필요한가 |
| --- | --- | --- |
| P1 | route node / current boss/shop state token | Run Atlas 읽힘이 즉시 살아난다 |
| P1 | stakes plaque / battle board accent | Battle Table이 계산 UI에서 무대로 바뀐다 |
| P1 | ending oath banner / seal accent | 엔딩이 정산창에서 문턱처럼 읽힌다 |
| P2 | grimoire forecast tile / run lobby plate | library hero의 완성도가 오른다 |
| P2 | curation sidebar shell | reward/shop family 일관성이 올라간다 |

### 아트 금지 범위
- 개별 몬스터/책의 상세 일러스트 리워크를 이번 패스의 필수 전제처럼 두지 않는다.
- 지금은 `세계관 디테일 확대`보다 `surface 읽힘 차이`가 우선이다.

## 5-4. QA / PM에게 넘길 것

### pass 기준 질문
1. 첫 5초에 지금 화면의 역할이 읽히는가?
2. 현재 판의 stakes와 다음 문턱이 같이 읽히는가?
3. 같은 카드 family를 재사용해도 surface 목적 차이는 유지되는가?
4. ending과 overrunEvent, onboarding과 hero shell의 경계가 다시 흐려지지 않았는가?

### 기록 문장 권장 형식
- `Run Table pass는 phase/세이브 구조를 바꾸지 않고 library/atlas/battle/curation/ending 5면의 읽힘을 재배치하는 데 성공/실패했다.`

---

## 6. Acceptance / review checklist

| 체크 | 기대값 | 실패 예시 |
| --- | --- | --- |
| Library hero | 책 선택 전부터 `이번 런의 성격`이 읽힘 | 그냥 카드 리스트 상단 배너처럼 보임 |
| Route board | 현재/다음 상점/다음 보스가 바로 보임 | page preview list만 읽히고 보드감이 없음 |
| Battle stakes | 현재 페이지 pressure와 다음 문턱이 한 번에 보임 | 전투 HUD가 더 복잡해졌을 뿐 stakes가 안 느껴짐 |
| Reward/Shop curation | 선택지가 다음 승부처 준비처럼 읽힘 | 단순 카드 추가처럼만 보임 |
| Ending oath | 결과와 carry-forward가 분리되어 읽힘 | 정산/보상/오버런 flavor가 한 화면에서 섞임 |
| Top bar tone | Prototype가 아니라 현재 빌드 방향을 반영함 | 여전히 실험실/아틀리에 톤이 강함 |

---

## 7. Risks & follow-ups

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| surface family가 아직 copy/markup 레벨에 많이 묶여 있음 | 후속 수정 때 label drift 가능 | helper/constant layer로 단계적 분리 |
| route board / stakes / oath banner의 visual token이 아직 CSS 중심 | 아트 handoff 시 구분감이 약할 수 있음 | P1 자산 패키지부터 별도 토큰화 |
| reward/shop/ending이 모두 카드 family를 쓰다 보니 다시 평준화될 위험 | 런 문맥 차이가 흐려짐 | family별 목적 문장과 sidebar role을 유지 |
| ending 강화가 overrunEvent scene까지 잠식할 위험 | 기존 boundary packet과 충돌 | ending은 결과/문턱 의미까지만, scene flavor는 overrunEvent 유지 |
| library hero가 온보딩 primer 역할까지 먹어버릴 위험 | B-track 도입 시 중복 설명 발생 | hero는 런 mood, primer는 first-run guidance로 분리 유지 |

---

## 8. Immediate next actions

1. **문서 우선**: 이 packet + summary + prototype를 포털 첫 화면에서 찾게 연결한다.
2. **프로그래머 우선**: `Run Atlas`, `Battle Stakes`, `Overrun Oath`의 텍스트/DOM hook을 정리해 future UI smoke anchor를 만든다.
3. **UI 우선**: 1440p 기준 density review를 1회 돌려 right panel / curation sidebar / ending side column이 과밀하지 않은지 본다.
4. **아트 우선**: route node token, stakes plaque, oath banner 3종만 먼저 자산화한다.
5. **QA 우선**: `surface 역할이 첫 5초 안에 읽히는가` 체크로 수동 review sheet를 만든다.

---

## 9. 이 문서가 하지 않는 일
- overrunEvent 구현 packet을 대체하지 않는다.
- 첫 런 온보딩 B-track packet을 대체하지 않는다.
- 새로운 페이지/event/세이브 시스템을 정의하지 않는다.
- 밸런스나 메카닉 변경을 정당화하지 않는다.

이 문서는 오직 **현재 적용된 Run Table UI 패스가 실제 제작 handoff 언어로 남도록** 만드는 source-of-truth다.
