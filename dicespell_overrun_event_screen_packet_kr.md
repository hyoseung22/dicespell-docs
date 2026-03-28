# DiceSpell `overrunEvent` 화면/자산 handoff 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent`를 화면·카피·자산 단위로 읽게 만드는 **screen/asset packet**이다.
- UI는 카드 위계와 density를 먼저, 아트는 badge/backplate/accent 범위를 먼저, 프로그래머는 DOM payload를 먼저 보면 된다.
- 이 화면은 새 선택지가 아니라 **이미 고른 것을 확인하고 챙겨 가는 장면**이라는 점을 첫 스크린에서 분명히 읽혀야 한다.

**한 줄 목표:** `overrunEvent`의 첫 화면이 왜 존재하는지, 무엇이 보여야 하는지, 어디까지 강조해야 하는지를 흔들리지 않게 고정한다.

### 왜 이 문서가 필요한가
- overrunEvent는 개념은 간단하지만 화면적으로는 `ending`과 `reward` 사이에서 쉽게 흐려질 수 있다.
- 이 문서는 바로 그 시각적 혼선을 줄이기 위한 source-of-truth다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| UI | badge → header → flavor → CTA 위계 | `content deck` |
| 아트 | accent / token / 배경 톤 최소선 | `production cutsheet` |
| 프로그래머 | payload와 display-only 소비 규칙 | `implementation packet` |
| QA | none/선택 branch 시각 차이 | `acceptance matrix` |

---

## 문서 목적

이 문서는 이미 작성된 아래 두 문서를, **실제 화면 제작과 역할별 handoff 단위**로 더 잘게 쪼갠 보조 패킷이다.

- `docs/dicespell_overrun_event_handoff_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`

앞선 두 문서가 `무엇을 만들지`와 `어디를 어떻게 고칠지`를 잡아줬다면, 이번 문서는 그다음 병목인 아래 질문을 닫는 데 목적이 있다.

- 프로그래머는 `summary -> render payload -> DOM`으로 어떤 필드를 넘겨야 하는가
- UI 오너는 중앙 카드 1장 화면을 어느 밀도와 우선순위로 설계해야 하는가
- 아트 오너는 지금 단계에서 어떤 소형 자산만 준비하면 되는가
- 카피 오너는 어떤 문구를 어디에 놓고, 어떤 표현은 피해야 하는가

즉 이 문서는 `overrunEvent`를 **실장 가능한 화면 계약서**로 바꾸는 역할을 한다.

---

## 1. 이번 슬롯에서 고정하는 핵심 결론

`overrunEvent`는 새 메카닉 소개 화면이 아니다.

> `방금 고른 전리품 1개를 들고 다음 권으로 넘어가는 세그먼트 경계 화면`이다.

따라서 이 화면은 아래 원칙을 반드시 지킨다.

1. **카드는 1장만 크게 보여 준다.**
2. **CTA도 1개만 둔다.**
3. **새 선택지는 만들지 않는다.**
4. **설명보다 전환 감각이 먼저 와야 한다.**
5. **skip 상태도 같은 화면 밀도로 처리한다.**

이 5개가 무너지면 `짧은 경계 장면`이 아니라 다시 `또 하나의 보상 화면`이 된다.

---

## 2. 화면이 해결해야 하는 실제 문제

현재 엔딩 화면까지는 이미 아래가 구현/문서화되어 있다.

- late normal / overrun normal의 `책 테마 전리품`
- `기록 골렘` / `붕락 정령` 처치 전리품
- 책 클리어 직후 `오버런 전리품 보관함`
- `overrunEvent` phase / snapshot / resolve / save normalization 계약

하지만 아직 제작자 입장에서 남아 있는 빈칸은 다음이다.

1. `전리품을 장면으로 어떻게 보여 줄지`가 화면 단위로 못 박혀 있지 않다.
2. 프로그래머가 `renderOverrunEvent()` payload를 어떻게 구성해야 하는지 한 번에 안 보인다.
3. UI/아트가 `기존 reward card 재사용 범위`와 `새 자산 최소 범위`를 동시에 보기 어렵다.
4. skip 상태가 허전한 placeholder로 떨어질 위험이 있다.

그래서 이번 문서는 `phase 설계`가 아니라 **실제 화면 handoff**에 집중한다.

---

## 3. 최종 화면 구조

### 3.1 레이아웃 요약

화면은 아래 4블록으로 고정한다.

1. 상단 전이 헤더
2. 중앙 집중 카드 영역
3. 하단 CTA 영역
4. 하단 보조 설명 1줄

### 3.2 와이어프레임

```text
┌──────────────────────────────────────────────────────────────┐
│ eyebrow                                                      │
│ H1: 다음 권의 첫 장을 펼친다                                  │
│ body-copy: flavorText                                        │
│                                                              │
│   ┌──────────────────────────────────────────────────────┐   │
│   │ source badge                                         │   │
│   │ reward title                                         │   │
│   │ reward subtitle / short description                  │   │
│   │ reward-card body (existing reward visual reuse)      │   │
│   └──────────────────────────────────────────────────────┘   │
│                                                              │
│ [ primary CTA ]                                              │
│ 확인 후 다음 세그먼트 첫 페이지로 이동합니다.                 │
└──────────────────────────────────────────────────────────────┘
```

### 3.3 정보 우선순위

항상 아래 순서로 읽혀야 한다.

1. **지금은 세그먼트 경계다.**
2. **내가 들고 갈 대상은 이것이다.**
3. **이 버튼을 누르면 다음 권으로 넘어간다.**

즉 이 화면은 `설명 화면`이 아니라 `전환 확정 화면`이다.

---

## 4. DOM / 렌더 payload 계약

### 4.1 `renderOverrunEvent(summary)`가 받아야 할 최소 필드

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "sourceType": "themeTrophy | enemyTrophy | none",
    "rewardId": "string | null",
    "rewardTitle": "string",
    "rewardDescription": "string",
    "rewardTag": "string",
    "theme": "slime | goblin | lich | null",
    "fromTemplateId": "number | null",
    "flavorText": "string",
    "confirmLabel": "string",
    "headerTitle": "string",
    "eyebrow": "Overrun Transition"
  }
}
```

### 4.2 렌더 단계에서 새로 계산하지 말아야 할 것

아래 값은 엔진 snapshot 단계에서 이미 만들어 두는 편이 안전하다.

- `headerTitle`
- `flavorText`
- `confirmLabel`
- `rewardTag`
- `sourceType`

이유:

- save/load 복구 시 UI 분기를 단순하게 유지할 수 있다.
- 카피 변경이 생겨도 `snapshot 생성부`와 `카피 표`만 보면 된다.
- `renderOverrunEvent()`는 `계산`이 아니라 `배치`에 집중할 수 있다.

### 4.3 권장 DOM 블록

```html
<section class="hero-card overrun-event-card">
  <div class="overrun-event-shell">
    <p class="eyebrow overrun-event-eyebrow">Overrun Transition</p>
    <h1 class="overrun-event-title">다음 권의 첫 장을 펼친다</h1>
    <p class="overrun-event-copy">이번 책의 잔향을 챙겨 다음 장의 준비물로 삼는다.</p>

    <section class="meta-retirement-card overrun-event-focus-card">
      <div class="section-head compact overrun-event-head">
        <span class="overrun-source-badge theme">책 전리품</span>
        <h3>점액 잔향 표본</h3>
      </div>
      <article class="reward-card overrun-event-reward-card">
        <!-- existing reward card body -->
      </article>
    </section>

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

## 5. 상태별 화면 매트릭스

### 5.1 sourceType별 카피/배지/강조 규칙

| sourceType | 헤더 | flavorText | CTA | 배지 | accent |
| --- | --- | --- | --- | --- | --- |
| `themeTrophy` | 다음 권의 첫 장을 펼친다 | 이번 책의 잔향을 챙겨 다음 장의 준비물로 삼는다. | 이 전리품을 챙기고 다음 권으로 | 책 전리품 | 현재 책 테마 accent |
| `enemyTrophy` | 방금 넘긴 위협의 잔해를 챙긴다 | 쓰러뜨린 적의 결을 다음 권 첫 장에 들고 간다. | 이 잔해를 들고 다음 권으로 | 적 전리품 | 적 전리품 accent + 현재 책 accent 보조 |
| `none` | 가볍게 다음 권을 연다 | 이번에는 아무 잔해도 들지 않은 채 다음 장을 펼친다. | 가볍게 다음 권을 연다 | 빈손 | 저채도 neutral accent |

### 5.2 skip 상태 규칙

skip 상태에서도 아래는 절대 빠지지 않는다.

- 카드 1장
- 배지 1개
- flavorText 1개
- CTA 1개

즉 `none`은 빈 페이지가 아니라 **빈손을 확인하는 페이지**다.

---

## 6. UI 디테일 계약

### 6.1 카드 크기/밀도 원칙

기존 reward card를 완전히 새로 짜지 않는다.

권장 규칙:

- 기존 reward card 대비 **1.2~1.35배 체감 크기**
- 카드 외곽에는 중앙 강조 backplate 추가
- 카드 주변 여백은 `ending`보다 넓게
- 카드 내부 텍스트 밀도는 기존 reward card와 유사하게 유지

즉 `새 카드 시스템`이 아니라 `기존 카드의 영웅형 변주`다.

### 6.2 버튼 구조 원칙

- 버튼은 primary CTA 1개만 사용
- secondary / ghost CTA 추가 금지
- 뒤로 가기, 다시 고르기, 미리 보기 버튼 추가 금지

이 phase는 `확정`이지 `재선택`이 아니다.

### 6.3 헤더 밀도 원칙

헤더는 길게 쓰지 않는다.

권장:

- eyebrow 1줄
- 제목 1줄
- flavorText 1~2줄

금지:

- 시스템 튜토리얼형 장문
- 전리품 규칙 장황 설명
- 로그처럼 딱딱한 확인 문구를 제목에 배치

---

## 7. 아트 패키지 최소 범위

### 7.1 필수 자산

1. `overrunEvent` 헤더 패널 1종
2. 출처 배지 3종
   - 책 전리품
   - 적 전리품
   - 빈손
3. 중앙 카드 강조 backplate 1세트
4. accent 컬러 규칙 4종
   - slime
   - goblin
   - lich
   - none(neutral)

### 7.2 선택 자산

1. 책상/제본대 결을 암시하는 얇은 배경 텍스처 1종
2. CTA 주변의 약한 빛 번짐 1종
3. `enemyTrophy` 전용 작은 균열/잔해 모티프 1종

### 7.3 이번 단계에서 하지 않는 것

- 전리품 개별 신규 일러스트
- sourceType별 완전 다른 레이아웃
- 시네마틱 연출용 풀스크린 아트
- 파티클 의존형 구현

이번 단계의 아트 목표는 `장면을 새로 그리는 것`이 아니라 **기존 보상 카드를 장면처럼 읽히게 만드는 것**이다.

---

## 8. 프로그래머 handoff 세부 규칙

### 8.1 `summary` 생성부에서 보장할 것

프로그래머는 UI가 아래 질문을 다시 계산하지 않게 만들어야 한다.

- 이 화면이 책 전리품인가, 적 전리품인가, 빈손인가
- 제목은 무엇인가
- flavorText는 무엇인가
- 버튼 문구는 무엇인가
- 어떤 accent를 써야 하는가

즉 `renderOverrunEvent()`는 아래 수준의 결정만 하게 한다.

- 어떤 블록을 어떤 순서로 그릴지
- 어떤 class를 붙일지
- 어떤 action을 바인딩할지

### 8.2 reward card 재사용 규칙

- 가능하면 기존 reward renderer를 재사용한다.
- 단, `overrunEvent` 화면 안에서는 선택 버튼/선택 상태 UI를 제거한다.
- 즉 `display-only reward card` 모드가 필요하다면 새 플래그로 분리하는 것이 안전하다.

### 8.3 confirm action 계약

`data-action="confirm-overrun-event"`는 아래만 수행한다.

1. `resolveOverrunEvent(state)` 호출
2. 화면 갱신
3. 다음 phase 진입

여기서 재선택/추가 확인 modal을 붙이지 않는다.

---

## 9. 카피 오너용 금지/권장 리스트

### 9.1 권장 톤

- 짧고 장면 중심
- `넘어간다`, `챙긴다`, `펼친다` 같은 전환 동사 사용
- 보상 설명은 카드 영역에 남기고, 헤더는 문맥 중심으로 유지

### 9.2 피해야 할 표현

- `보상이 적용됩니다`
- `선택한 아이템을 확인하세요`
- `다음 단계로 진행`
- `시스템상 오버런 전리품이 준비되었습니다`

이런 표현은 맞는 말이지만 화면을 죽인다.

### 9.3 우선순위

- 헤더 = 장면
- 카드 = 대상
- CTA = 행동
- footnote = 시스템 확인

이 순서를 뒤집지 않는다.

---

## 10. QA / smoke 관찰 포인트

기존 구현 패킷의 smoke 외에, 화면 관점에서 특히 봐야 할 것은 아래 6개다.

1. `themeTrophy` / `enemyTrophy` / `none` 3상태가 시각적으로 한눈에 구분되는가
2. 중앙 카드가 기존 ending reward grid와 충분히 다른 무게감을 가지는가
3. skip 상태가 허전한 임시 화면처럼 보이지 않는가
4. CTA를 누르기 전에는 실제 보상이 적용되지 않는가
5. 저장 후 복구했을 때 헤더/배지/CTA 카피가 어색한 기본값으로 무너지지 않는가
6. `none` fallback에서도 `왜 이 페이지가 존재하는지`가 자연스럽게 읽히는가

---

## 11. 역할별 deliverable 체크리스트

### 프로그래머

- [ ] `summary.overrunEvent`에 UI가 바로 그릴 수 있는 문자열/배지/상태 필드 포함
- [ ] reward card display-only 재사용 방식 결정
- [ ] `renderCenter()`에 `overrunEvent` 분기 추가
- [ ] `confirm-overrun-event` action 연결
- [ ] `theme/enemy/none` 상태별 스모크 또는 snapshot 확인

### UI 오너

- [ ] 카드 1장 중심 레이아웃 확정
- [ ] `문맥 -> 대상 -> 행동` 순서가 유지되는지 확인
- [ ] `ending` 화면과 시각적 계층 차이를 만들되, 컴포넌트 재사용성을 유지
- [ ] skip 상태 동일 밀도 설계

### 아트 오너

- [ ] 헤더 패널 1종
- [ ] 출처 배지 3종
- [ ] backplate/glow 1세트
- [ ] theme accent 4종
- [ ] 풀아트/대형 애니메이션 제외 범위 확인

### 카피 오너

- [ ] sourceType별 제목/flavor/CTA 표 확정
- [ ] 금지 표현 배제
- [ ] footnote는 기능 확인용 1줄로 제한

---

## 12. 리스크 & 후속 과제

### 리스크 1. 화면이 또 하나의 reward 화면처럼 보일 위험

- 영향: `세그먼트 경계 장면`이 아니라 `보상 확인 한 번 더`처럼 느껴질 수 있다.
- 대응: 카드 1장 강조, CTA 1개 고정, grid 금지, 재선택 금지.

### 리스크 2. skip 상태가 placeholder처럼 보일 위험

- 영향: 플레이어가 `굳이 왜 이 페이지가 있지?`라고 느낄 수 있다.
- 대응: `none`도 배지/제목/flavor/CTA를 모두 유지하고, neutral accent를 부여한다.

### 리스크 3. 아트 요구가 과하게 불어날 위험

- 영향: bounded 문서 작업이 다시 대형 제작 의존으로 변한다.
- 대응: 헤더 패널/배지/backplate/accent만 필수로 고정하고 풀아트는 제외한다.

### 후속 과제

이 패킷이 붙고 나면 다음 순서는 명확하다.

1. 프로그래머: `overrunEvent` runtime 구현
2. UI 오너: 실제 DOM/CSS 적용
3. QA: save/load/none fallback 포함 smoke 고정
4. 이후에만 `보스형 골렘` 또는 `오버런 이벤트 바리에이션` 검토

---

## 13. 한 줄 요약

`overrunEvent`는 새 시스템이 아니라, **이미 고른 전리품 1개를 다음 권의 첫 장으로 넘기는 화면 계약**이다. 이 문서는 그 계약을 프로그래머/UI/아트/카피 오너가 바로 집어 들 수 있는 수준으로 잘게 쪼갠 handoff 패킷이다.
