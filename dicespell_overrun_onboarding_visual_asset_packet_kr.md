# DiceSpell `overrunEvent` / 첫 런 온보딩 비주얼 자산 패킷

## 3줄 요약
- 이 문서는 `overrunEvent` A-track Commit 2와 첫 런 온보딩 B-track이 이미 기능/카피/검수 기준으로는 충분한데도, **UI/아트 자산 handoff가 여러 문서에 흩어져 있어 실제 제작 턴에서 다시 감으로 합쳐질 위험**을 줄이기 위한 source handoff다.
- 핵심은 새 화면을 더 설계하는 것이 아니라, `scene card`, `hero-primer`, `inline hint`, `badge/accent/token`, `ending boundary`를 **한 장의 자산 번들 기준**으로 다시 묶어 프로그래머/UI/아트/QA가 같은 visual contract를 보게 만드는 것이다.
- 즉 이 문서는 `무슨 기능을 만들까`보다 `지금 어떤 visual layer를 누구에게 어떤 묶음으로 넘기고, 무엇은 아직 만들지 말아야 하는가`를 빠르게 잠그는 데 목적이 있다.

## 한 줄 목표

`Commit 2 closing`과 이후 `B-1~B-4` 온보딩 surface가 **같은 shell/tone/token 체계**를 공유하되, `ending`이 `overrunEvent` flavor를 먹거나 온보딩이 새 대형 자산 요구로 부풀지 않게 만드는 visual asset source-of-truth를 고정한다.

## 왜 이 문서가 필요한가

현재 DiceSpell은 아래 문서들 덕분에 기능/상태/카피/검수선이 이미 꽤 촘촘하다.

- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_event_content_deck_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_library_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b2_battle_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b3_reward_shop_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b4_ending_packet_kr.md`
- `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`

문제는 이제 문서 부족이 아니라 아래 같은 **비주얼 handoff 분산**이다.

1. `overrunEvent`는 카드 위계가 정리돼 있지만, 실제 아트 오너 관점에서는 `어디까지가 token 재사용이고 어디부터가 새 자산인가`가 여러 packet에 흩어진다.
2. 온보딩은 B-1~B-4 surface별 packet이 강하지만, UI/아트가 공통 shell/tone 규칙을 한 번에 보기 어렵다.
3. `ending`과 `overrunEvent`는 경계 문서가 있어도, 실제 asset bundle 수준에서는 `결과 레이어`와 `장면 레이어`가 같은 묶음으로 넘어갈 위험이 남는다.
4. 구현 턴이 오면 프로그래머가 결국 `styles.css`와 token class를 정리해야 하는데, visual owner가 같은 naming/밀도 기준으로 review할 단일 표가 없다.

즉 지금 필요한 것은 새 카피나 새 메카닉이 아니라, **시각 레이어 handoff를 역할별 제작 묶음으로 재정렬하는 일**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. visual layer 구조`, `2. surface bundle 매트릭스`, `4. 역할별 handoff` | `commit2 closing surgery sheet`, `A-3 UI packet`, 각 B-step packet | 어떤 class/token 묶음까지만 이번 턴에 열어도 되는지 안다 |
| UI 오너 | `2. surface bundle 매트릭스`, `3. density rule`, `4. UI handoff` | `A-3 UI packet`, `screen packet`, `boundary packet` | `scene card`와 `primer`가 같은 톤이되 같은 surface처럼 읽히면 안 된다는 기준을 안다 |
| 아트 오너 | `2. surface bundle 매트릭스`, `4. 아트 handoff`, `5. 최소 자산 패키지` | `content deck`, `production cutsheet`, `boundary packet` | 새 대형 자산 없이 닫히는 구간과 token sanity만 필요한 구간을 안다 |
| QA | `2. surface bundle 매트릭스`, `6. acceptance evidence`, `7. 리스크 & 후속과제` | `acceptance matrix`, `fixture ledger`, 각 B-step packet | visual drift를 기능 미구현과 분리해 어떤 캡처를 남겨야 하는지 안다 |
| PM/기획 | `0. first screen 결론`, `7. 리스크 & 후속과제`, `8. 즉시 다음 액션` | `owner workboard`, `execution reporting packet` | 이번 턴이 새 화면 추가인지, 기존 visual contract 정렬인지 한 줄로 기록할 수 있다 |

---

## 0. first screen 결론

### 왜 이 문서를 열어야 하나
- **프로그래머**: `overrunEvent`와 온보딩 primer에 공통으로 쓸 shell/tone/token 규칙을 어디까지 같이 잡아도 되는지 확인하려고 연다.
- **UI 오너**: `scene card`, `hero-primer`, `inline hint`, `ending helper`를 같은 브랜드 감도로 묶되 서로 다른 기능 레이어로 읽히게 만들 기준을 확인하려고 연다.
- **아트 오너**: 어떤 턴은 새 illustration이 아니라 accent/token sanity만 넘기면 된다는 점을 명확히 하려고 연다.
- **QA/PM**: visual green을 `예뻐 보인다`가 아니라 `문서가 요구한 layer separation이 지켜졌다`로 기록하려고 연다.

### 가장 짧은 답
- `overrunEvent` Commit 2의 visual 자산은 **중앙 장면 카드 1장**을 닫는 것이고, 온보딩 B-track의 자산은 **surface별 guidance layer 1개씩**을 닫는 것이다.
- 둘은 같은 `badge / accent / token / shell family`를 공유할 수 있지만, `ending` 결과 레이어와 `overrunEvent` flavor 레이어는 절대 같은 카드로 합쳐지면 안 된다.
- B-track도 새 대형 key art가 기본값이 아니다. 대부분의 턴은 **token/accent/hierarchy sanity + 최소 보조 그래픽**만으로 닫아야 한다.

### 한 줄 판정 규칙
- `scene card`는 `장면`을 만든다.
- `primer`는 `해석`을 돕는다.
- `ending helper`는 `버튼 의미`만 보조한다.
- 위 세 레이어가 한 카드처럼 합쳐지면 visual handoff 실패다.

---

## 1. visual layer 구조

## 3-line summary
- DiceSpell의 다음 visual handoff는 사실상 세 레이어다: `scene`, `primer`, `result helper`.
- 같은 프로젝트 톤을 유지하되, 레이어별 밀도와 책임은 다르게 가져가야 한다.
- 프로그래머와 UI/아트는 공통 token을 재사용해도 되지만, 레이어 책임은 섞으면 안 된다.

**한 줄 목표:** `shared visual system`과 `surface responsibility`를 동시에 고정한다.

### 1.1 레이어 정의

| 레이어 | 대표 surface | 무엇을 말하나 | 무엇을 말하면 안 되나 | 기본 밀도 |
| --- | --- | --- | --- | --- |
| Scene layer | `overrunEvent` Commit 2 | 방금 고른 전리품을 들고 다음 권으로 넘어가는 장면, flavor, confirm CTA | 결과표 해석, 튜토리얼성 설명, 온보딩 primer | 가장 높음 |
| Primer layer | B-1/B-2/B-3 | 지금 surface에서 무엇을 보아야 하는지, 왜 여기서 판단하는지 | 다음 surface 이야기, 장면 flavor, 과도한 서사 | 중간 |
| Result helper layer | B-4 ending | 패배/승리/오버런 CTA 의미, 결과 해석 보조 | `themeTrophy`/`enemyTrophy`/`none` flavor 장면 | 가장 낮음 |

### 1.2 공통 visual token family

| 항목 | 공통 규칙 | 재사용 가능 범위 | 금지 범위 |
| --- | --- | --- | --- |
| `accentKey` | surface 목적을 1차 분기한다 | `overrunEvent` content deck, onboarding content deck, badge/accent line | header/flavor branch를 accent만으로 역추론 |
| `badgeLabel` | 읽기 시작점 역할만 한다 | 카드 상단, primer 뱃지, helper 꼬리표 | 메인 문장 전체를 badge가 대신 설명 |
| `artTokenSet` | 최소 장식/아이콘/배경 토큰 정의 | icon, divider, glow, frame motif | 새 대형 illustration 필요 여부를 토큰으로 과장 |
| shell class | 카드 외곽 tone과 밀도를 정한다 | `scene`, `hero-primer`, `inline-hint`, `result-helper` family | 하나의 shell을 모든 surface에 동일 적용 |
| reward summary block | 이미 얻은/얻을 보상 핵심만 보여 준다 | `overrunEvent` display-only 요약, reward/shop coexist | ending 본문이나 primer body가 reward summary를 대체 |

### 1.3 visual source-of-truth 우선순위
1. **레이어 책임**: 이 문서
2. **surface별 카드/위계**: `docs/dicespell_overrun_a3_ui_packet_kr.md`, `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
3. **exact 카피/토큰**: `docs/dicespell_overrun_event_content_deck_kr.md`, `docs/dicespell_first_run_onboarding_content_deck_kr.md`
4. **ending vs overrun 경계**: `docs/dicespell_ending_overrun_boundary_packet_kr.md`
5. **커밋 범위와 금지선**: `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`, 각 B-step packet

---

## 2. surface bundle 매트릭스

## 3-line summary
- 아래 표는 실제 handoff를 `surface 1개 = visual bundle 1개`로 다시 묶는다.
- 목표는 각 오너가 이번 턴에 요구되는 asset 밀도를 한 줄로 파악하게 만드는 것이다.
- 특히 `새로 만들어야 하는 것`보다 `재사용해도 되는 것`을 먼저 고정한다.

**한 줄 목표:** surface별로 `필수 / 재사용 / 금지 / owner 제출물`을 한 표에서 읽게 만든다.

| surface | visual 목적 | 필수 자산 묶음 | 우선 재사용 | 이번 턴 금지 | owner 제출물 |
| --- | --- | --- | --- | --- | --- |
| A-track Commit 2 `overrunEvent` | ending 이후 `장면`으로 읽히게 만들기 | 중앙 카드 shell 1종, badge/header/flavor/CTA 위계, display-only reward summary, `none` neutral variant | reward card 일부 토막, existing portal/card frame, content deck token | ending 결과표 재사용, 새 reward 타입, onboarding helper 혼입 | 프로그래머: branch/class 연결 / UI: 위계 스케치 / 아트: token sanity / QA: S4/S5/S6/S10/S12 캡처 |
| B-1 library | 책 선택의 첫 진입 해석 | hero-primer panel 1종, book-select badge, highlight callout 2곳 | shelf 패널 shell, existing highlight style, content deck token | shelf 전면 리뉴얼, 신규 key art 요구 | 프로그래머: seen flag + panel / UI: hierarchy sanity / 아트: accent token note / QA: 첫 진입·재진입 캡처 |
| B-2 battle | entry/first-spell/auto-pass 트리거 해석 | hero-primer 1종 + inline hint 2종 + badge variant | battle panel shell, log area spacing, existing status chip | 전투 UI 전체 재배치, 대형 연출 자산 추가 | 프로그래머: trigger 주입 / UI: density sanity / 아트: inline token check / QA: 3트리거 캡처 |
| B-3 reward/shop | reward vs shop 목적 분리 | reward primer 1종, shop primer 1종, accent split, boss draft coexist spacing | reward/shop 카드 shell, existing chip/divider, content deck token | reward·shop strip 단일화, 상점 일러스트 팩 확대 | 프로그래머: primer 분리 / UI: accent split sanity / 아트: token boundary note / QA: reward·shop·boss draft 캡처 |
| B-4 ending | 결과/CTA 의미만 보조 | inline/badge helper 3종, CTA meaning assist, victory/defeat helper copy shell | ending result shell, existing CTA row, boundary packet rule | `overrunEvent` flavor, 장면 카드, trophy branch illustration | 프로그래머: helper 주입 / UI: low-density sanity / 아트: neutral/result token note / QA: ending/CTA 캡처 |

---

## 3. density / hierarchy rule

## 3-line summary
- DiceSpell 다음 visual 턴은 `많이 그린다`보다 `무엇이 먼저 읽히는가`를 안정화하는 작업이다.
- 같은 gold/green 계열을 써도 `scene`, `hero-primer`, `inline hint`, `ending helper`는 밀도가 달라야 한다.
- 아트/프론트엔드 모두 class 재사용보다 hierarchy 분리가 더 중요하다.

**한 줄 목표:** surface별 visual 톤을 통일하되 밀도까지 평준화하지 않는다.

### 3.1 밀도 규칙

| 묶음 | 시각 우선순위 | 허용 요소 | 의도적으로 줄일 요소 |
| --- | --- | --- | --- |
| `scene-card` | 최상위 | 큰 header, flavor, CTA, reward summary, footnote, accent backdrop | primer성 설명, 복수 helper badge |
| `hero-primer` | 상위 | title/body/badge, 1~2 highlight, compact illustration/token | 과한 flavor 문장, CTA 경쟁 |
| `inline-hint` | 중간 | 짧은 badge/body, 작은 token, 상태 옆 배치 | 큰 패널, 배경 장식, 다중 배지 |
| `result-helper` | 하위 | CTA meaning 보조 문장, 작은 badge, subtle divider | flavor 서사, 보상 카드화, scene backdrop |

### 3.2 `ending` / `overrunEvent` 경계 규칙
- `ending`은 **왜 이 버튼을 누르는가**만 말한다.
- `overrunEvent`는 **무엇을 들고 다음 권으로 넘어가는가**를 장면으로 말한다.
- 같은 badge family를 써도 `ending`에는 큰 flavor block을 넣지 않는다.
- `none` branch도 `빈 화면`이 아니라 `중립 장면 카드`여야 하지만, 그 장면은 `ending`에 선적용하지 않는다.

### 3.3 온보딩 공통 규칙
- B-track은 대부분 **새 대형 자산 없이 닫히는 것**이 기본값이다.
- primer는 항상 기존 play surface를 보조해야지 surface를 점유하면 안 된다.
- UI/아트 sanity는 `읽힘 개선`이지 `새 화면 발명`이 아니다.

---

## 4. 역할별 handoff 포인트

## 4.1 프로그래머에게 넘길 것

### 3-line summary
- 프로그래머는 이번 visual packet을 `새 디자인 문서`로 읽는 것이 아니라 `class/token/surface 범위 제한서`로 읽어야 한다.
- Commit 2에서는 `renderOverrunEvent(summary)`와 shell 분리가 핵심이고, B-track에서는 surface별 primer container 주입이 핵심이다.
- 중요한 것은 visual token naming과 layer separation을 코드에서 재추론하지 않는 것이다.

**한 줄 목표:** `display-only scene`과 `non-blocking primer`를 코드 구조에서도 분리한다.

- `overrunEvent`
  - `renderCenter()` 분기에서 `overrunEvent`를 독립 surface로 취급한다.
  - `scene-card` class family를 `ending` shell과 분리한다.
  - `badgeLabel`, `accentKey`, `artTokenSet`은 snapshot/content deck에서 받고 render가 다시 추론하지 않는다.
- B-track
  - `hero-primer`, `inline-hint`, `result-helper`를 별도 class family로 유지한다.
  - seen 플래그 구현이 끝났다고 shell 통합까지 같이 하지 않는다.
  - `reward/shop`은 한 shared helper로 축약하지 않는다.

### 프로그래머 제출물
1. class/token mapping 표 또는 코드 diff
2. surface별 DOM payload 주입 스크린샷/HTML 캡처
3. `ending` / `overrunEvent` 분리 확인 메모
4. 이번 턴에 새 대형 asset loader를 열지 않았다는 메모

## 4.2 UI 오너에게 넘길 것

### 3-line summary
- UI 오너는 새 스타일 발명이 아니라 `같은 family, 다른 위계`를 닫는 역할이다.
- Commit 2에서는 scene card가 결과표처럼 읽히지 않는지, B-track에서는 primer가 조작을 가리지 않는지 확인한다.
- 가장 자주 나는 drift는 `예쁘게 맞추다 보니 다 같은 카드처럼 보이는 것`이다.

**한 줄 목표:** surface별 reading order를 오너 handoff 단위로 고정한다.

- 확인 포인트
  - `scene card`: badge → header → flavor → reward summary → CTA → footnote
  - `hero-primer`: badge → title → body → highlight
  - `inline-hint`: badge/body 또는 body-only, action 근처 저밀도
  - `result-helper`: CTA 보조선만 유지, flavor 차단
- 금지 포인트
  - `scene card`를 reward result table처럼 그리기
  - battle inline hint를 hero card처럼 키우기
  - reward/shop primer를 같은 strip으로 정렬해 목적 차이를 지우기
  - ending helper에 overrun flavor 문장을 넣기

### UI 제출물
1. surface별 hierarchy note 1개씩
2. low/mid/high density sanity 캡처
3. `ending` vs `overrunEvent` 분리 체크 1줄
4. 이번 턴 범위를 넘는 polish 보류 메모

## 4.3 아트 오너에게 넘길 것

### 3-line summary
- 아트 오너의 기본 임무는 새 그림을 많이 만드는 것이 아니라 token/accent drift를 막는 것이다.
- 대부분의 surface는 기존 motif 재사용 + 소형 보조 asset로 닫히며, 새 key art는 blocker가 아니다.
- 특히 `none`, `reward/shop`, `ending`은 `작아서 덜 중요`가 아니라 drift가 가장 잘 나는 곳이다.

**한 줄 목표:** 대형 asset 생산보다 `token boundary`와 `neutral variant`를 먼저 잠근다.

- 꼭 확인할 것
  - `overrunEvent none`은 빈 카드가 아니라 neutral token variant다.
  - library/battle/reward/shop/ending은 같은 프로젝트 감도를 공유하되 서로 같은 panel처럼 보이면 안 된다.
  - `ending` helper에는 trophy flavor용 큰 아트 자산을 넣지 않는다.
- 우선순위
  1. `scene-card` token set sanity
  2. primer badge/icon family sanity
  3. reward/shop accent 분리 sanity
  4. ending helper neutral/result token sanity
- 이번 턴 기본값
  - 새 대형 key art 없음
  - 새 배경 일러스트 없음
  - 필요 시 icon/divider/glow/frame motif 수준만 추가

### 아트 제출물
1. token/accent sanity 메모
2. 필요 시 소형 asset 목록
3. `새 대형 자산 불필요` 또는 `예외 필요` 판정 1줄
4. `ending`과 `overrunEvent` 비혼입 확인 메모

## 4.4 QA에게 넘길 것

### 3-line summary
- QA는 visual 검수를 `예쁜가`가 아니라 `문서가 요구한 layer separation이 지켜졌는가`로 본다.
- Commit 2는 S4/S5/S6/S10/S12와 DOM/캡처 evidence를 같이 보고, B-track은 surface별 acceptance 캡처를 남긴다.
- visual drift와 기능 drift를 섞어 쓰지 않는 것이 중요하다.

**한 줄 목표:** visual evidence를 기능 evidence와 나란히 남기되 서로 대체하지 않게 만든다.

- `overrunEvent`
  - `scene-card`가 `ending` shell과 다른지
  - `none` branch가 비어 보이지 않는지
  - reward summary가 display-only인지
- B-track
  - library hero-primer가 shelf 선택을 가리지 않는지
  - battle inline hint가 action을 가리지 않는지
  - reward/shop primer가 같은 strip처럼 읽히지 않는지
  - ending helper가 flavor를 먹지 않는지

### QA 제출물
1. surface별 캡처 묶음
2. visual pass/fail 코멘트
3. 기능 pass/fail과 분리된 visual drift 코멘트
4. tracker/run log용 한 줄 요약

---

## 5. 최소 자산 패키지

## 3-line summary
- 아래 표는 `이번 턴에 정말 필요한 최소 자산`만 적는다.
- 기본 원칙은 기존 shell/tone을 최대한 재사용하고, 새 자산은 drift를 막는 데 필요한 만큼만 연다.
- 이 표가 커지기 시작하면 범위가 이미 부푼 것이다.

**한 줄 목표:** visual 작업을 `bounded production`으로 유지한다.

| 묶음 | 최소 필요 자산 | 선택 가능 | 기본값 |
| --- | --- | --- | --- |
| `overrunEvent scene-card` | shell variant 1, badge/icon token, accent backdrop 1, neutral variant 1 | 소형 divider/glow | 기존 카드 shell 재활용 + token 조정 |
| `library hero-primer` | primer header motif 1, highlight marker 1 | 작은 badge icon | 기존 shelf tone 재활용 |
| `battle hint` | inline badge/icon 1~2 | subtle pulse/glow | 기존 status chip 재활용 |
| `reward/shop primer` | reward accent token 1, shop accent token 1 | divider variant | 기존 card/chip 재활용 |
| `ending helper` | neutral/result badge token 1 | CTA divider 1 | 기존 ending CTA row 재활용 |

### 예외 승인 기준
- 아래 중 하나가 아니면 새 대형 자산은 열지 않는다.
  1. 기존 shell로는 `scene`과 `result helper`가 절대 구분되지 않는다.
  2. neutral/none variant가 비어 보여 fallback 경험이 깨진다.
  3. primer가 기존 surface와 충돌해 가독성 저하가 명확하다.

---

## 6. acceptance evidence 묶음

## 3-line summary
- visual handoff도 evidence bundle이 있어야 tracker/run log에서 과장되지 않는다.
- Commit 2와 B-track은 서로 다른 증거 묶음을 남겨야 한다.
- `캡처 1장`보다 `surface 책임이 지켜졌음을 보여 주는 묶음`이 중요하다.

**한 줄 목표:** visual green을 commit/surface별로 분리 기록한다.

| 단계 | 필수 visual evidence | 같이 남길 문장 |
| --- | --- | --- |
| Commit 2 closing | `overrunEvent` 카드 3축(theme/enemy/none 중 해당 분기), CTA, ending과 다른 shell 확인 캡처 | `scene card visual contract를 닫았다.` |
| B-1 | 첫 진입 primer + 재진입 미노출 + book 선택 비차단 캡처 | `library primer visual floor를 닫았다.` |
| B-2 | entry / first-spell / auto-pass 캡처 | `battle trigger visual handoff를 닫았다.` |
| B-3 | reward primer / shop primer / boss draft coexist 캡처 | `reward/shop visual intent split을 닫았다.` |
| B-4 | defeat / victory / overrun CTA helper 캡처 + overrun flavor 비혼입 메모 | `ending helper visual boundary를 닫았다.` |

---

## 7. 리스크 & 후속과제

| 리스크 | 왜 문제인가 | 이번 대응 | 다음 후속 |
| --- | --- | --- | --- |
| `scene card`와 `ending helper`가 같은 카드처럼 합쳐지는 위험 | 결과 화면과 장면 화면의 책임이 다시 무너진다 | layer 기준과 금지 범위를 표로 분리 | Commit 2 리뷰에서 shell 분리 캡처를 필수 제출물로 유지 |
| B-track이 primer라는 이유로 같은 panel family로 평준화되는 위험 | library/battle/reward/shop/ending이 모두 같은 밀도로 보여 읽힘이 둔해진다 | density rule과 surface bundle 매트릭스로 분리 | B-1~B-4 착수 시 surface별 hierarchy note를 계속 남긴다 |
| 아트가 새 대형 자산이 필요하다고 오판하는 위험 | bounded commit이 asset production wait 상태로 밀린다 | 최소 자산 패키지와 예외 승인 기준을 분리 | 예외가 생기면 run log에 `예외 승인 필요`를 별도 기록 |
| 프로그래머가 token/class를 render에서 다시 추론하는 위험 | content deck/source-of-truth와 visual 구현이 다시 갈라진다 | `accentKey`/`badgeLabel`/`artTokenSet`은 입력값 소비 원칙을 재강조 | Commit 2 코드 리뷰에서 재추론 코드 유무를 체크 |
| QA/PM이 visual green과 runtime green을 같은 문장으로 기록하는 위험 | half-visual closure가 runtime-ready처럼 보인다 | evidence bundle별 문장을 분리 | tracker/run log에 단계별 복붙 문장 유지 |

---

## 8. 즉시 다음 액션

1. 이번 턴 문서 사용 순서는 `commit2 closing surgery sheet -> 이 visual asset packet -> A-3 UI packet -> boundary/content deck`이다.
2. Commit 2 실제 구현 전, 프로그래머/UI/아트는 `scene-card`에 필요한 최소 shell/token만 확정하고 `ending helper`와 공유하지 않는다.
3. A-track green 뒤 B-track을 열 때는 `이 문서 -> 해당 B-step packet -> onboarding content deck` 순서로 읽고, surface별 최소 자산 패키지만 연다.
4. tracker / blueprint / portal index / run log에는 이번 작업을 `새 화면 추가`가 아니라 `visual asset handoff 정렬`로 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_commit2_closing_surgery_sheet_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_overrun_event_content_deck_kr.md`
- `docs/dicespell_first_run_onboarding_content_deck_kr.md`
- `docs/dicespell_first_run_onboarding_owner_workboard_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
