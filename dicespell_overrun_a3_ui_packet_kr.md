# DiceSpell `overrunEvent` A-3 중앙 카드 UI 컷오버 패킷

## 문서 목적

이 문서는 `docs/dicespell_overrun_a2_snapshot_packet_kr.md` 다음 단계인 **A-3 = 중앙 카드 1장 UI + ending과의 시각 경계 고정**만을 다루는 구현 전용 handoff packet이다.

이미 DiceSpell에는 아래 문서가 있다.

- `docs/dicespell_overrun_event_screen_packet_kr.md`
- `docs/dicespell_overrun_event_content_deck_kr.md`
- `docs/dicespell_ending_overrun_boundary_packet_kr.md`
- `docs/dicespell_overrun_onboarding_delivery_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`

하지만 실제 구현 직전의 남은 작은 공백은 여전히 아래 질문이다.

> `좋아, A-2에서 canonical snapshot은 고정됐어. 그런데 A-3에서는 중앙 카드 1장 UI를 정확히 어디까지 닫고, ending과 무엇을 시각적으로 다르게 읽히게 만들어야 하지?`

이 문서는 그 질문에 답한다. 즉 `overrunEvent` 전체를 다시 설명하지 않고, **A-3 commit 범위 / render payload 소비 규칙 / DOM 위계 / CSS 재사용 한계 / owner별 납품물 / A3 smoke 증거**만 좁혀서 다룬다.

---

## 왜 지금 이 문서가 필요한가

A-2가 닫히면 snapshot은 이미 `contentKey`, `badgeLabel`, `accentKey`, `artTokenSet`, `headerTitle`, `flavorText`, `confirmLabel`을 들고 있다. 그런데 바로 다음 커밋에서 구현자가 다시 흔들릴 지점은 아래 다섯 가지다.

1. `renderCenter()` 안에서 `ending`와 `overrunEvent`가 같은 카드 밀도로 읽혀 경계가 흐려질 수 있다.
2. `reward card 재사용` 범위가 명확하지 않으면 `overrunEvent`가 다시 `보상 선택 화면`처럼 보일 수 있다.
3. app.js가 snapshot display-only 소비를 벗어나 branch를 재판정하려는 유혹이 생길 수 있다.
4. `none` branch가 neutral 장면이 아니라 placeholder처럼 허전해질 수 있다.
5. CTA가 `확인 후 적용`이 아니라 `즉시 적용이 이미 끝난 상태`처럼 읽히면 A-4 resolve 의미가 약해진다.

즉 A-3에서 필요한 것은 새 아이디어가 아니라,
**중앙 카드 1장 UI가 무엇을 보여 주고 무엇을 절대 다시 계산하지 않아야 하는지**를 한 장으로 고정하는 일이다.

---

## A-3의 한 줄 정의

> `overrunEvent`는 결과표가 아니라, canonical snapshot을 display-only로 소비하는 단일 장면 카드다. A-3에서는 그 장면이 ending과 다른 계층으로 읽히게 만드는 UI만 닫고, 적용/진행은 아직 A-4로 미룬다.

---

## A-3 범위

### 포함
- `renderCenter(summary)` 내 `overrunEvent` 분기 고정
- `renderOverrunEvent(summary)` 또는 동등 함수 추가
- 중앙 카드 1장 DOM 위계 확정
- `badge → header → flavor → reward summary → CTA → footnote` 시각 계층 고정
- `none` branch 포함 3축(theme/enemy/none) 카드 밀도 보장
- ending과 `overrunEvent`의 시각 경계 고정
- A3-S1~S5 smoke 또는 동등 DOM sanity 기준 정의

### 제외
- confirm CTA 시점의 실제 resolve
- reward 적용 / segment advance / cleanup
- primer helper 주입
- 전용 풀아트 시스템 도입

즉 A-3의 끝은 `버튼을 누르면 실제 적용된다`가 아니라,
**버튼을 누르기 직전 장면이 문서가 말한 위계로 정확히 읽힌다**다.

---

## canonical source

| 질문 | source-of-truth |
| --- | --- |
| branch별 카피/배지/accent/token은 어디서 오나 | `docs/dicespell_overrun_event_content_deck_kr.md` |
| 화면 밀도와 레이아웃 원칙은 어디서 오나 | `docs/dicespell_overrun_event_screen_packet_kr.md` |
| ending과의 책임 경계는 어디서 오나 | `docs/dicespell_ending_overrun_boundary_packet_kr.md` |
| A-2에서 이미 고정된 payload는 무엇인가 | `docs/dicespell_overrun_a2_snapshot_packet_kr.md` |
| 전체 커밋 순서와 A-3 위치는 무엇인가 | `docs/dicespell_overrun_onboarding_delivery_packet_kr.md` |
| UI 이후 confirm/resolve는 어디서 보나 | `docs/dicespell_overrun_event_integration_workorder_kr.md` |

---

## A-3 완료 정의

A-3는 아래 네 줄이 모두 참일 때만 닫혔다고 본다.

1. `overrunEvent`는 ending과 다른 **단일 장면 카드**로 읽힌다.
2. render는 snapshot field를 소비만 하고 branch를 다시 추론하지 않는다.
3. `themeTrophy`, `enemyTrophy`, `none` 3축이 모두 같은 카드 밀도와 CTA 의미를 유지한다.
4. CTA는 `확인 후 적용` 의미로 읽히고, 적용 완료 화면처럼 보이지 않는다.

---

## UI 핵심 결론

A-3에서는 아래 다섯 가지를 절대 흔들리지 않는 규칙으로 본다.

1. **카드는 1장만 크게 보인다.**
2. **CTA는 1개만 둔다.**
3. **reward summary는 display-only다.**
4. **ending 본문은 flavor를 다시 먹지 않는다.**
5. **`none`도 정상 장면이다.**

즉 A-3는 `새 보상 화면`을 만드는 커밋이 아니라,
`ending 다음에 잠깐 멈춰 서는 경계 장면`을 읽히게 만드는 커밋이다.

---

## render payload 소비 규칙

### 입력 전제

A-3의 app.js는 A-2가 만든 canonical snapshot을 아래처럼 이미 받은 상태라고 가정한다.

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "sourceType": "themeTrophy | enemyTrophy | none",
    "contentKey": "overrun.theme.slime | overrun.enemy.golem | overrun.none",
    "rewardId": "string | null",
    "rewardTitle": "string",
    "rewardDescription": "string",
    "rewardTag": "string | null",
    "badgeLabel": "string",
    "accentKey": "slime | goblin | lich | golem | collapse | neutral",
    "artTokenSet": "theme-slime | enemy-golem | none-neutral",
    "headerTitle": "string",
    "flavorText": "string",
    "confirmLabel": "string",
    "resolved": false
  }
}
```

### app.js에서 다시 계산하지 말아야 할 것

- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`
- `headerTitle`
- `flavorText`
- `confirmLabel`

### 금지 패턴

- `rewardTitle.includes('슬라임')`로 배지/톤 분기
- `sourceType === 'themeTrophy'`면 app.js에서 별도 headline 재작성
- `none`일 때만 CTA/footnote를 줄이는 식의 밀도 축소
- ending 카드 클래스를 그대로 재사용해 `overrunEvent`가 결과표처럼 보이게 두기

핵심은 **render가 layout만 맡고 branch identity는 맡지 않는다**는 점이다.

---

## 권장 DOM 위계

```html
<section class="hero-card overrun-event-card tone-slime token-theme-slime">
  <div class="overrun-event-shell">
    <p class="eyebrow overrun-event-eyebrow">Overrun Transition</p>
    <div class="overrun-event-focus-card">
      <div class="overrun-event-badge-row">
        <span class="overrun-source-badge">책 전리품</span>
      </div>
      <h2 class="overrun-event-title">다음 권의 첫 장을 펼친다</h2>
      <p class="overrun-event-flavor">이번 책의 잔향을 챙겨 다음 장의 준비물로 삼는다.</p>

      <article class="reward-card overrun-event-reward-summary">
        <div class="reward-card-head">
          <span class="reward-tag">책 테마 전리품</span>
          <h3>점액 잔향 표본</h3>
        </div>
        <p class="reward-copy">HP +8, Shield +8, Collapse -8</p>
      </article>
    </div>

    <div class="hero-actions overrun-event-actions">
      <button class="primary-button" data-action="confirm-overrun-event">
        이 전리품을 챙기고 다음 권으로
      </button>
    </div>

    <p class="muted overrun-event-footnote">확인 후 다음 세그먼트 첫 페이지로 이동합니다.</p>
  </div>
</section>
```

---

## 필수 시각 계층

| 계층 | 역할 | 필수 여부 | 비고 |
| --- | --- | --- | --- |
| badge | 출처 인지 (`책 전리품` / `적 전리품` / `빈손`) | 필수 | 가장 짧게 |
| header | 장면 headline | 필수 | 1줄 우선 |
| flavor | 경계 장면의 감정선 | 필수 | 1~2줄 |
| reward summary | 실제 들고 갈 대상 확인 | 필수 | display-only |
| CTA | 다음 단계 이동 의미 | 필수 | 1개만 |
| footnote | `확인 후 적용` 명시 | 필수 | 짧게 |

### 읽기 순서 목표

1. 지금은 결과표가 아니라 **넘어가기 직전 장면**이다.
2. 들고 갈 대상은 이것이다.
3. 버튼을 누르면 그때 적용되고 다음 권으로 간다.

---

## ending과의 시각 경계

### ending이 계속 맡는 것
- 승패 결과 해석
- 정산 정보
- `오버런 계속` 버튼 의미
- primer의 낮은 밀도 설명

### `overrunEvent`가 맡는 것
- 전리품 장면화
- branch별 flavor
- source badge
- 다음 권 직전의 단일 카드 집중

### 금지 패턴
- ending 본문에서 `themeTrophy`/`enemyTrophy`/`none` flavor를 미리 길게 설명
- `overrunEvent`가 정산표처럼 수치 카드 여러 장을 늘어놓음
- 같은 색/같은 카드 클래스로 ending과 `overrunEvent`가 거의 같은 화면처럼 보이게 둠

---

## CSS 재사용 규칙

### 재사용 허용
- `.hero-card`
- `.hero-actions`
- `.primary-button`
- 기존 `.reward-card` 내부 타이포 일부
- muted/eyebrow 계열 텍스트 스타일

### 새 클래스로 분리해야 하는 것
- `.overrun-event-card`
- `.overrun-event-shell`
- `.overrun-event-focus-card`
- `.overrun-event-badge-row`
- `.overrun-source-badge`
- `.overrun-event-title`
- `.overrun-event-flavor`
- `.overrun-event-reward-summary`
- `.overrun-event-footnote`
- tone/token class (`tone-slime`, `tone-goblin`, `tone-lich`, `tone-golem`, `tone-collapse`, `tone-neutral` 등 동등 구조)

### 이유

reward card 내부 body를 일부 재사용하더라도, 외곽 구조까지 재사용하면 다시 `선택 화면`처럼 보이기 쉽다. 따라서 **내부 정보 블록은 빌리되, 외곽 장면 틀은 분리**한다.

---

## `none` branch 화면 규칙

`none`은 빈 화면이 아니다. 아래 요소를 모두 유지한다.

- neutral badge (`빈손` 또는 동등 카피)
- header 1줄
- flavor 1~2줄
- 중립 reward summary 또는 equivalent summary card
- CTA 1개
- footnote 1줄

### 금지 패턴
- reward summary 박스를 통째로 없앰
- CTA를 secondary처럼 약하게 처리
- flavor를 제거해 placeholder처럼 보이게 함

핵심은 `none`도 **선택하지 않음이라는 정상 서사 상태**라는 점이다.

---

## owner별 deliverable

### 프로그래머
- `renderCenter(summary)`에 `overrunEvent` 분기 추가
- `renderOverrunEvent(summary)` 또는 동등 함수 구현
- snapshot display-only 소비 구조 확정
- `data-action="confirm-overrun-event"` 버튼 배치까지 연결
- 필요 시 DOM sanity smoke 추가

### UI
- 중앙 카드 1장 위계 확정
- ending 대비 여백/집중도 차이 명확화
- badge / headline / flavor / summary / CTA / footnote의 읽기 순서 보정
- `none` branch가 비어 보이지 않게 neutral density 확보

### 아트
- backplate / accent strip / badge tone / focus token 최소 패키지
- theme/enemy/none 3축이 같은 구조에서 tone만 달라 보이게 조정
- overrunEvent가 ending보다 약간 더 장면적으로 보이게 보조

### QA
- 3축(theme/enemy/none) 수동 캡처
- ending과 `overrunEvent` 화면 비교 캡처
- CTA가 `확인 후 적용`으로 읽히는지 텍스트/위계 sanity 확인

---

## 추천 구현 순서

1. `renderCenter()` 분기 추가
2. `renderOverrunEvent(summary)` 스켈레톤 추가
3. snapshot field 그대로 꽂는 title/flavor/badge/CTA 연결
4. reward summary display-only 블록 연결
5. 새 CSS shell/tone class 추가
6. `none` branch density 확인
7. DOM sanity smoke 또는 equivalent render test 추가

---

## A3-S1~S5 smoke / sanity 기준

| ID | 시나리오 | 기대 결과 |
| --- | --- | --- |
| A3-S1 | 슬라임 theme branch 렌더 | `badge/header/flavor/summary/CTA/footnote` 6요소 존재, tone=`slime` |
| A3-S2 | 기록 골렘 enemy branch 렌더 | `badgeLabel=적 전리품`, title/flavor/CTA가 snapshot 그대로 노출 |
| A3-S3 | `none` branch 렌더 | neutral tone 유지, summary/CTA/footnote가 빠지지 않음 |
| A3-S4 | ending → `overrunEvent` 비교 sanity | ending 본문이 flavor를 중복하지 않고 `overrunEvent`가 더 집중된 카드로 보임 |
| A3-S5 | render drift guard | app.js가 `rewardTitle` 문자열 검색 없이 snapshot field만 사용 |

### 증거 형식
- 가능하면 smoke string/DOM fragment
- 최소한 manual capture checklist
- `none` 포함 3축 모두 같은 density로 읽힌다는 증거 필수

---

## 파일 경계

| 파일 | 역할 | 이번 단계 변경 여부 |
| --- | --- | --- |
| `src/app.js` | 분기/렌더/DOM | 필수 |
| `src/styles.css` | shell/tone/density | 필수 |
| `scripts/smoke-test.mjs` | DOM/render sanity | 권장 |
| `src/game-engine.js` | resolve/진행 | 원칙적으로 제외 |

A-3는 UI commit이다. 따라서 `game-engine.js`를 다시 흔드는 순간 범위가 쉽게 A-4로 번진다.

---

## DoD

- `overrunEvent`가 결과표가 아니라 중앙 장면 카드로 읽힌다.
- badge / header / flavor / summary / CTA / footnote 6요소가 3축 모두 유지된다.
- `none` branch도 placeholder가 아니라 정상 장면처럼 보인다.
- app.js에 branch 재추론 코드가 없다.
- ending과 `overrunEvent`의 시각 경계가 최소 1개 이상의 수동 캡처 또는 동등 증거로 남는다.

---

## 자주 생길 오해와 금지선

### 오해 1. reward summary는 선택 카드처럼 보여도 된다
금지다. A-3 시점엔 더 이상 선택이 아니다. 요약 카드여야 한다.

### 오해 2. ending 카드 스타일을 거의 그대로 쓰면 구현이 빠르다
빠르지만 drift가 크다. 외곽 shell은 반드시 분리한다.

### 오해 3. `none`은 정보가 없으니 카드 밀도를 줄여도 된다
금지다. `none`은 fallback이자 정상 branch다.

### 오해 4. CTA가 한 개뿐이니 footnote는 없어도 된다
금지다. A-4 전까지는 `확인 후 적용` 의미를 footnote로 분명히 남겨야 한다.

---

## 리스크 & 후속 과제

| 리스크 | 영향 | 대응 |
| --- | --- | --- |
| UI가 reward card 재사용 범위를 넘겨 다시 선택 화면처럼 보이는 위험 | A-4 resolve 의미 약화 | 외곽 shell 분리, summary display-only 규칙 고수 |
| `none` branch가 빈 화면처럼 보이는 위험 | fallback 신뢰 저하 | neutral tone + summary + CTA + footnote 6요소 유지 |
| app.js가 snapshot field 대신 title 문자열로 tone/badge를 재조합하는 위험 | A-2 canonical contract 붕괴 | A3-S5 drift guard 추가 |
| ending과 `overrunEvent`의 시각 차이가 약한 위험 | shared surface 경계 재붕괴 | 비교 캡처를 DoD 증거에 포함 |

---

## 다음 단계

A-3가 닫히면 다음 A-4는 더 단순해진다.

- confirm CTA 시점 resolve
- reward 적용 + 다음 세그먼트 진입
- cleanup / resolved guard
- S1~S12 + recovery 세트 최종 고정

즉 A-3는 `장면이 어떻게 보여야 하는가`를 끝내는 단계고,
A-4는 `그 장면의 버튼이 실제로 무엇을 하게 할 것인가`를 닫는 단계다.

다음 구현자는 A-2 canonical snapshot을 display-only로 소비하는 구조를 먼저 지키고, 그 위에 이 문서의 카드 위계와 tone 규칙을 그대로 얹는다.