# DiceSpell 첫 런 온보딩 acceptance matrix

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩이 **언제 완료인지, 어디까지 복구되어야 하는지**를 판정하는 acceptance 기준표다.
- QA는 acceptance/recovery를 먼저, 프로그래머는 fallback과 상태 누락 복구를 먼저, PM은 owner별 증거 묶음을 먼저 보면 된다.
- 온보딩이 `보이기만 하는지`가 아니라 **적절한 시점에만 보이고 과하게 반복되지 않는지**를 확인하는 문서로 읽는다.

**한 줄 목표:** 첫 런 온보딩의 pass/fail 기준을 surface별로 분리하고, 저장/분기 누락 시 복구선까지 고정한다.

### 왜 이 문서가 필요한가
- readable 문서만으로는 `좋아 보인다` 수준의 리뷰에 머무르기 쉽다.
- 이 문서는 구현·UI·QA가 같은 완료선을 쓰도록 만드는 검수용 source of truth다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| QA | acceptance/recovery 표 전체 | `screen packet`, `content deck` |
| 프로그래머 | 상태 누락 fallback, event hook 검수선 | `integration workorder` |
| UI | surface별 필수/금지 요소 | `screen packet` |
| PM | owner별 납품 증거와 완료선 | `summary` |

---

## 문서 목적

이 문서는 아래 온보딩 문서 3종이 이미 고정한 방향·화면·컷오버를, **실제 구현 완료선과 복구 기준**으로 한 번 더 좁혀서 프로그래머/UI/아트/QA가 같은 검수선을 보게 만드는 acceptance packet이다.

- `docs/dicespell_first_run_onboarding_handoff_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`

지금 온보딩 축의 남은 병목은 `무엇을 만들 것인가`가 아니라 아래 두 가지다.

1. 각 surface primer가 **언제 구현 완료라고 말할 수 있는가**
2. 저장 누락, 첫 주문/자동 종료 누락, `overrunEvent` 전후 문맥 변화 같은 복구 케이스를 **어디까지 막아야 하는가**

즉 이 문서는 새 기획서가 아니라, **첫 런 guidance layer의 완료선·증거·복구선**을 production handoff 수준으로 고정하는 문서다.

---

## 1. 이 문서가 닫으려는 마지막 공백

현재 온보딩 문서들은 이미 충분히 구체적이다.

- handoff는 왜 첫 런 guidance가 필요한지와 4개 surface 범위를 고정했다.
- screen packet은 `renderGrimoireShelf()` / `renderBattle()` / `renderReward()` / `renderShop()` / `renderEnding()` 기준 주입 위치와 밀도를 고정했다.
- integration workorder는 `meta.uiGuidance`, `getPrimerState()`, 첫 주문/자동 종료 event hook, Step 0~4 컷오버 순서를 고정했다.

하지만 실제 구현 직전 기준으로는 아직 아래가 owner마다 다르게 해석될 수 있다.

1. `도서관 hero-primer가 보인다`와 `책 선택이 더 쉬워졌다`는 서로 다른 완료선인데, 무엇을 acceptance로 볼지 아직 하나로 묶여 있지 않다.
2. `첫 주문 힌트`, `자동 종료 힌트`처럼 사건 기반 surface는 render만 보지 말고 event hook까지 검수해야 하는데, 이 검수선이 표로 고정돼 있지 않다.
3. `reward`와 `shop`의 목적 문구는 카피만 다르면 충분하지 않고, 실제로도 서로 다른 공간처럼 읽혀야 하는데, 이 차이를 무엇으로 판정할지 아직 owner별 암묵지에 가깝다.
4. `ending` primer는 `overrunEvent` 미구현/구현 후 둘 다 견뎌야 하는데, recovery 기준이 별도 표로 닫혀 있지 않다.

즉 지금 남은 병목은 아이디어가 아니라 **acceptance와 recovery를 누가 어떤 증거로 확인할 것인가**다.

---

## 2. 검수 원칙

첫 런 온보딩 acceptance는 아래 4개 원칙을 동시에 만족해야 한다.

### 2.1 설명보다 행동 우선

primer가 있어도 플레이어는 바로 행동할 수 있어야 한다.

- 책 선택 버튼이 막히면 실패
- 전투 핵심 인터랙션이 가려지면 실패
- reward/shop/ending CTA를 primer가 덮으면 실패

### 2.2 한 화면 한 메시지

한 surface가 한 번에 하나의 역할만 수행해야 한다.

- library: 책 카드 읽는 순서
- battle: 한 턴 행동 순서와 손익 읽기
- reward/shop: 즉시 vs 장기 목적 구분
- ending: 무엇이 남는가, 지금 누르는 버튼이 무엇인가

### 2.3 저장 누락은 진행 차단이 아니라 재노출 문제여야 함

`meta.uiGuidance` 또는 동등 구조가 일부 누락돼도,

- 화면 본체는 정상 렌더돼야 하고
- 진행은 그대로 가능해야 하며
- 최악의 결과는 `primer가 한 번 더 뜬다` 수준이어야 한다.

### 2.4 `overrunEvent`와 역할 충돌 금지

ending primer는 끝까지 아래 두 가지까지만 말해야 한다.

- 무엇이 남는가
- 버튼 의미

반대로 아래는 ending primer가 먹으면 안 된다.

- 장면 연출
- 경계 이벤트의 flavor
- 다음 세그먼트 진입 직전 감정선

---

## 3. source-of-truth / 증거 소유권

| 층위 | source of truth | 이 문서에서 요구하는 증거 |
| --- | --- | --- |
| 메타 저장 | `meta.uiGuidance` 또는 동등 구조 | 새/기존 세이브에서 기본값 또는 정규화가 정상 작동함 |
| runtime/helper | `getPrimerState()` 또는 동등 helper | surface별 payload가 `visible / line / badge / ctaLine` 단위로 분리됨 |
| render | 각 `render*()` 함수 | primer slot이 기존 핵심 인터랙션을 가리지 않음 |
| action hook | 첫 주문 / 자동 종료 / ending 분기 | 사건 기반 1회 노출이 중복 스팸 없이 발생함 |
| QA evidence | smoke / 수동 시나리오 / 스크린샷 | acceptance/recovery 케이스별 pass/fail 기록 |

원칙:

- `봤는가`와 `지금 어떻게 보이는가`를 한 객체에 섞지 않는다.
- acceptance는 텍스트 존재만 보지 않고, **상태·배치·진행 가능성**까지 함께 본다.

---

## 4. surface별 acceptance matrix

## A1. 도서관 책 선택 hero-primer

| 항목 | 기준 |
| --- | --- |
| 목적 | 초보자가 책 카드에서 `초반 운영`과 `주의점`을 먼저 읽어야 함을 배운다 |
| source of truth | `uiGuidance.seenBookSelectPrimer` + `getPrimerState().library` |
| 필수 UI 요소 | `첫 런 가이드` eyebrow, `첫 책은 이렇게 읽으면 됩니다` title, 2줄 body, `알겠음` CTA 또는 동등 dismiss |
| 필수 강조 요소 | `openingPlan` / `caution` highlight, `completionBonus` 과강조 금지 |
| 배치 규칙 | `.section-head` 아래, `.body-copy` 위. primer가 떠도 그리드 첫 줄과 책 선택 버튼은 즉시 클릭 가능 |
| 프로그래머 납품물 | `renderGrimoireShelf()` primer slot, `seenBookSelectPrimer` 저장/복구, 카드 highlight class |
| UI 납품물 | compact hero-primer 배치안, 1280 기준 scroll 1회 내 가독성 유지 |
| 아트 납품물 | library 공용 backplate/ribbon 1종, 과장 없는 gold/brown accent |
| QA pass 기준 | 새 프로필 첫 진입에서 1회 노출, dismiss 후 같은 프로필에서 반복 스팸 없음, primer 유무와 무관하게 책 선택 가능 |
| 실패 조건 | primer가 그리드/버튼을 가림, 특정 책 tier ranking처럼 읽힘, `completionBonus`가 초반 핵심 정보처럼 더 강조됨 |

## A2. 첫 전투 진입 hero-primer

| 항목 | 기준 |
| --- | --- |
| 목적 | 플레이어가 `가능한 족보 확인 -> 주문 1회 -> 로그 확인` 3축을 첫 전투 시작 전에 읽는다 |
| source of truth | `uiGuidance.seenBattlePrimer` + `getPrimerState().battle.heroVisible` |
| 필수 UI 요소 | `첫 전투 가이드` eyebrow, 주문 1회 title, 3개 bullet 또는 동등 3행 구조 |
| 배치 규칙 | `.battle-subcopy` 바로 아래 full-width compact card 우선. 주사위/타깃/핸드 인터랙션을 물리적으로 가리면 실패 |
| 프로그래머 납품물 | `renderBattle(summary)` primer slot, `seenBattlePrimer` 저장/복구 |
| UI 납품물 | floating tooltip 금지, compact full-width card 안 |
| 아트 납품물 | 공용 glyph 3종(`주사위`, `주문 1회`, `잠금`) |
| QA pass 기준 | 첫 전투 진입 1회 노출, dismiss 없이도 전투 진행 가능, primer 유무와 무관하게 dice/target/hand 클릭 가능 |
| 실패 조건 | hero-primer가 HUD와 경쟁해 핵심 패널을 가림, subcopy와 합쳐 과잉 장문이 됨 |

## A3. 첫 주문 후 inline-primer

| 항목 | 기준 |
| --- | --- |
| 목적 | 방금 사용한 족보가 잠길 수 있고, 결과는 로그에서 읽는다는 점을 사건 직후 1회만 알려 준다 |
| source of truth | `uiGuidance.seenFirstSpellHint` + cast success hook + `getPrimerState().battle.firstSpellHintVisible` |
| 필수 UI 요소 | `방금 쓴 족보는 이번 페이지 동안 잠길 수 있습니다.` 또는 동등 문장, 로그 확인 보조 문장 최대 1개 |
| 배치 규칙 | `.battle-hand-section` 헤더 아래 또는 최신 로그 위 1~2줄 ribbon |
| 프로그래머 납품물 | cast success 이후 seen 갱신, battle render의 inline slot |
| UI 납품물 | hand/log 주변에 배치하되 타깃 전환과 경쟁하지 않음 |
| QA pass 기준 | 첫 주문 직후 1회 노출, 이후 같은 프로필/같은 런에서 반복 스팸 없음, 주문 처리/피해 계산/로그 출력 회귀 없음 |
| 실패 조건 | 캐스트 순서를 바꾸거나 추가 confirm이 필요해짐, 동일 전투 내 다중 노출, 로그를 덮어 실제 결과를 못 읽음 |

## A4. 자동 종료 inline-primer

| 항목 | 기준 |
| --- | --- |
| 목적 | `쓸 수 있는 주문이 없어 몬스터 턴으로 넘어간다`는 점을 modal 없이 이해시킨다 |
| source of truth | `uiGuidance.seenAutoPassHint` + auto-pass event hook + `getPrimerState().battle.autoPassHintVisible` |
| 필수 UI 요소 | `지금은 쓸 수 있는 주문이 없어 몬스터 턴으로 넘어갔습니다.` 또는 동등 1문장 |
| 배치 규칙 | `.battle-action-panel` 하단 또는 `.battle-subcopy` 대체 line. 흐름을 끊는 modal 금지 |
| 프로그래머 납품물 | auto-pass 감지, seen 갱신, inline slot |
| UI 납품물 | 한 줄 또는 두 줄 ribbon, 전투 진행 버튼을 가리지 않음 |
| QA pass 기준 | 실제 자동 종료 상황에서만 1회 노출, MP 부족/가용 족보 없음 상황이 묵시적으로라도 읽힘, primer 없어도 전투 흐름 동일 |
| 실패 조건 | pass 상황이 아닌데도 노출, modal처럼 전투를 멈춤, 문장이 길어 원인보다 설명이 앞섬 |

## A5. 첫 reward purpose primer

| 항목 | 기준 |
| --- | --- |
| 목적 | reward를 `즉시 선택`으로 읽게 만든다 |
| source of truth | `uiGuidance.seenRewardPrimer` + `getPrimerState().reward` |
| 필수 UI 요소 | `첫 보상` badge, `보상은 다음 1~2전투를 버티고 밀어 주는 즉시 선택입니다.` line 또는 동등 문장 |
| 배치 규칙 | `.section-head` 아래, `reward-block` 위. 카드 그리드보다 먼저 눈에 들어오되 선택지를 가리면 실패 |
| 프로그래머 납품물 | `renderReward(summary)` primer slot, seen 저장 |
| UI 납품물 | 얇은 accent strip 또는 badge-primer. full card 금지 |
| 아트 납품물 | warm accent 1종 |
| QA pass 기준 | 첫 reward 진입 시 1회 노출, boss artifact draft가 함께 열려도 reward block 상단 1회만 유지 |
| 실패 조건 | reward taxonomy 전체 설명으로 길어짐, shop과 체감상 같은 문장처럼 읽힘 |

## A6. 첫 shop purpose primer

| 항목 | 기준 |
| --- | --- |
| 목적 | shop을 `장기 투자` 공간으로 reward와 분리해 읽게 만든다 |
| source of truth | `uiGuidance.seenShopPrimer` + `getPrimerState().shop` |
| 필수 UI 요소 | `첫 상점` badge, `상점은 이번 런의 장기 방향을 굳히는 투자 공간입니다.` line 또는 동등 문장 |
| 배치 규칙 | `.section-head` 아래 또는 기존 `.body-copy` 대체 line. `다음 페이지로` CTA보다 강하면 실패 |
| 프로그래머 납품물 | `renderShop()` primer slot, seen 저장 |
| UI 납품물 | reward와 다른 accent/badge/placement |
| 아트 납품물 | cool accent 1종 |
| QA pass 기준 | 첫 shop 진입 시 1회 노출, reward primer와 같은 공간처럼 보이지 않음 |
| 실패 조건 | reward와 시각/문장 구조가 너무 비슷함, grid가 과도하게 아래로 밀림 |

## A7. 패배 ending primer

| 항목 | 기준 |
| --- | --- |
| 목적 | 패배해도 `서고 기록이 남는다`는 점을 먼저 읽게 만든다 |
| source of truth | `uiGuidance.seenDefeatEndingPrimer` + `getPrimerState().ending.line` |
| 필수 UI 요소 | `첫 결과 정산` badge 또는 동등 표식, `런은 끝나도 ... 서고 기록은 남습니다.` line |
| 배치 규칙 | `.ending-card` 본문 최상단 compact line. 정산 카드와 CTA를 가리지 않음 |
| 프로그래머 납품물 | 패배 분기 line 계산, render slot |
| UI 납품물 | 과도한 hero-card 금지, badge/inline 수준 유지 |
| QA pass 기준 | 첫 패배 ending에서 1회 노출, 이후 같은 프로필에서 반복 스팸 없음 |
| 실패 조건 | 패배 감정선을 장문 설명으로 덮음, 정산/재시도 CTA보다 더 큰 카드가 됨 |

## A8. 승리 / 오버런 ending primer

| 항목 | 기준 |
| --- | --- |
| 목적 | 승리 시 `완주 보너스 + 이어가기`, 오버런 가능 시 `버튼 의미`를 각각 분리해 읽힌다 |
| source of truth | `uiGuidance.seenVictoryEndingPrimer`, `uiGuidance.seenOverrunPrimer`, `getPrimerState().ending.line/ctaLine` |
| 필수 UI 요소 | 승리 line 1개, `오버런 계속` 근처 ctaLine 1개 |
| 배치 규칙 | 본문 primer는 `.ending-card` 상단, ctaLine은 `오버런 계속` 버튼 근처 1줄 서브카피 |
| 프로그래머 납품물 | 승리/오버런 분기 helper, CTA 주변 slot |
| UI 납품물 | `런 정산`, `완주 보너스`, `오버런 전리품 보관함`보다 낮은 위계 유지 |
| 아트 납품물 | `완주`, `오버런`, `남는 진척` 상태 배지 3종 |
| QA pass 기준 | 첫 승리/오버런 가능한 ending에서 각각 1회 노출, `overrunEvent` 미구현 상태에서도 자연스럽고 향후 구현 후 밀도만 낮춰 재사용 가능 |
| 실패 조건 | ending primer가 `overrunEvent` 장면 설명까지 먹음, CTA 문구와 본문이 같은 말을 반복함 |

---

## 5. recovery matrix

## R1. 오래된 meta 저장에 `uiGuidance`가 아예 없음

- 기대 동작: 기본값 정규화 후 primer만 재노출될 수 있고, 앱은 정상 진행
- 확인 owner: 프로그래머, QA
- 필요 증거: 기존 세이브 로드 후 크래시 없음, primer payload 계산 정상
- 실패 처리: meta 전체 reset 금지, `uiGuidance = {}` 수준의 안전한 fallback

## R2. 특정 `seen*` 키만 누락

- 기대 동작: 해당 primer만 다시 뜨고 나머지는 기존 상태 유지
- 확인 owner: 프로그래머, QA
- 필요 증거: `seenRewardPrimer`만 제거한 저장에서 reward primer만 재노출
- 실패 처리: 전체 primer 재초기화로 확장 금지

## R3. battle action hook가 누락되거나 primer payload 계산 실패

- 기대 동작: 첫 주문/자동 종료 힌트가 안 뜰 수는 있어도 전투 흐름은 그대로 진행
- 확인 owner: 프로그래머, QA
- 필요 증거: cast / auto-pass 회귀 없음, 앱 에러 없음
- 실패 처리: hook 내부 예외가 battle phase 자체를 깨지 않도록 guard

## R4. reward/shop primer payload가 비어 있음

- 기대 동작: 헤더 본체와 카드 그리드는 정상 렌더
- 확인 owner: 프로그래머, QA
- 필요 증거: `visible:false` 또는 null payload에서 화면 정상 출력
- 실패 처리: primer DOM 생성을 건너뛰고 기존 body-copy만 유지

## R5. ending 분기 계산이 틀리거나 `overrunEvent` 도입 전후 문맥이 어긋남

- 기대 동작: primer 문장만 약해지거나 빠질 수는 있어도 `오버런 계속`, `정산`, `보관함` 본체는 정상 작동
- 확인 owner: 프로그래머, QA, UI
- 필요 증거: `overrunEvent` 미구현/구현 가정 양쪽에서 CTA 의미 충돌 없음
- 실패 처리: 장면 설명 삭제, 행동 의미만 남기는 더 얇은 문장으로 fallback

## R6. primer가 레이아웃을 밀어 핵심 클릭 영역을 덮음

- 기대 동작: primer를 숨기거나 compact variant로 강등해도 본 기능 사용 가능
- 확인 owner: UI, QA
- 필요 증거: 1280 기준 핵심 버튼/카드 클릭 가능, 스크롤 과증가 없음
- 실패 처리: floating/fixed 배치 폐기, inline/badge variant로 강등

---

## 6. owner별 납품 증거

## 6.1 프로그래머

반드시 제출해야 하는 증거:

1. `meta.uiGuidance` 기본값/정규화 코드
2. `getPrimerState()` 또는 동등 helper shape
3. 5개 render surface 주입 포인트
4. 첫 주문/자동 종료 event hook 코드 위치
5. primer 비노출 시 기존 플로우가 그대로 도는 smoke 또는 최소 회귀 검증

완료로 보지 않는 것:

- 텍스트가 DOM에 찍혔다는 이유만으로 완료 처리
- battle hook 없이 정적 render만 넣고 `첫 주문/자동 종료`를 문서상 완료로 적는 것

## 6.2 UI 오너

반드시 제출해야 하는 증거:

1. library hero-primer 배치 시안
2. battle hero-primer / inline-primer 2종 배치 시안
3. reward/shop purpose primer 배치 시안
4. ending 본문 line / ctaLine 배치 시안
5. 1280 기준 겹침/가림 없음 체크

완료로 보지 않는 것:

- 디자인 시안만 있고 실제 핵심 클릭 영역 overlap 검토가 없는 상태
- reward와 shop이 같은 색/같은 라벨/같은 위계로 남는 상태

## 6.3 아트 오너

반드시 제출해야 하는 증거:

1. primer 공통 backplate 또는 ribbon 1세트
2. badge/icon 최소 세트 6종
3. reward/shop accent 분리 가이드
4. ending 상태 배지 3종

완료로 보지 않는 것:

- 신규 메인 일러스트를 전제로 한 과도한 범위 확장
- 자산이 없으면 구현 자체를 못 하는 구조

## 6.4 QA

반드시 제출해야 하는 증거:

1. first-run profile 시나리오 pass/fail 기록
2. `seen*` 재노출/비노출 regression 결과
3. 오래된 meta / 일부 키 누락 recovery 결과
4. `overrunEvent` 미구현 / 구현 후 가정 양쪽 ending 카피 적합성 결과

완료로 보지 않는 것:

- happy path만 확인하고 recovery를 건너뛴 상태
- `한 번 뜬다`만 보고 `진행이 막히지 않는가`를 확인하지 않은 상태

---

## 7. 최소 검증 패키지

### 자동 검증 최소선

- primer 관련 helper/render 분기가 undefined/null payload에도 안전하게 돈다
- primer 비노출 상태에서도 기존 battle/reward/shop/ending flow가 그대로 통과한다
- `uiGuidance` 정규화가 누락 저장에도 안전하다

### 수동 검증 최소선

1. 새 프로필 → 도서관 primer 1회
2. 첫 전투 진입 → hero-primer 1회
3. 첫 주문 → inline hint 1회
4. 자동 종료 상황 → inline hint 1회
5. 첫 reward → `즉시 선택`
6. 첫 shop → `장기 투자`
7. 첫 패배 ending → `서고 기록이 남음`
8. 첫 승리/오버런 ending → `이어 간다` / CTA 의미
9. 같은 프로필에서 중복 스팸 없음
10. meta 일부 손상/초기화 상태에서도 진행 차단 없음

---

## 8. 리스크 & 후속 과제

### 리스크 1. acceptance가 텍스트 존재 확인으로만 축소될 위험

- 영향: primer는 보이지만 실제 플레이 해석 비용은 그대로 남는다.
- 대응: 이 문서의 pass 기준은 항상 `배치 + 상태 + 진행 가능성` 3축을 같이 본다.

### 리스크 2. battle 후속 힌트가 render 중심 구현으로 누락될 위험

- 영향: 가장 중요한 사건 기반 학습이 빠져 온보딩 체감이 반쪽이 된다.
- 대응: 첫 주문/자동 종료는 반드시 action hook 증거를 요구한다.

### 리스크 3. reward/shop primer가 카피만 다르고 체감상 같은 화면으로 남을 위험

- 영향: 첫 런 플레이어는 둘 다 `카드 고르는 화면`으로만 읽는다.
- 대응: accent/badge/placement를 acceptance 기준에 포함한다.

### 리스크 4. ending primer가 `overrunEvent`의 장면 감각을 미리 소모할 위험

- 영향: 오버런 경계 이벤트가 붙었을 때 존재감이 줄어든다.
- 대응: ending은 끝까지 `무엇이 남는가`와 `버튼 의미`만 담당하게 검수한다.

### 다음 후속 과제

1. 다음 UX 구현 슬롯은 이 matrix를 기준으로 Step 1(`도서관 + 전투 진입`)부터 실제 UI를 붙인다.
2. 구현이 들어가면 본 matrix의 A1~A8 / R1~R6을 그대로 smoke/manual checklist로 옮긴다.
3. `overrunEvent` 구현이 먼저 닫히더라도, ending primer 검수는 이 matrix 기준으로 다시 한 번 얇게 확인한다.

---

## 9. 최종 결론

첫 런 온보딩의 남은 문서 공백은 방향도, 화면도, 컷오버 순서도 아니다.

이제 남은 마지막 공백은 **언제 완료라고 말할 수 있는가**, 그리고 **무엇이 깨져도 어디까지는 반드시 안전해야 하는가**다.

이 acceptance matrix는 그 마지막 공백을 닫는다. 이제 온보딩 축도 프로그래머/UI/아트/QA가 같은 종료선과 같은 복구선을 보고 실제 구현에 들어갈 수 있다.
