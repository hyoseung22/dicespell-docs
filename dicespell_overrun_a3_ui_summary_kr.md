# DiceSpell `overrunEvent` A-3 중앙 카드 UI 요약

## 이번 문서가 닫는 공백

A-2 문서로 `overrunEvent` snapshot의 canonical field set과 normalize/recovery 규칙은 이미 고정됐다. 그런데 그 다음 실제 구현 직전에는 더 작은 공백이 남는다.

> `좋아, payload는 고정됐어. 그런데 A-3에서는 중앙 카드 1장 UI를 정확히 어디까지 닫고, ending과는 무엇을 다르게 읽히게 만들어야 하지?`

이번 문서 `docs/dicespell_overrun_a3_ui_packet_kr.md`는 바로 이 질문에 답한다.

---

## 핵심 결론

A-3의 `overrunEvent`는 **결과표가 아니라 단일 장면 카드**다.

그래서 아래 다섯 가지를 고정한다.

1. 카드 1장만 크게 보인다.
2. CTA는 1개만 둔다.
3. reward summary는 display-only다.
4. ending은 flavor를 다시 먹지 않는다.
5. `none`도 placeholder가 아니라 정상 branch다.

즉 이 단계는 `새 보상 화면`을 만드는 일이 아니라,
`다음 권으로 넘어가기 직전의 짧은 장면`을 정확히 읽히게 만드는 일이다.

---

## A-3에서 app.js가 해야 할 일

- `renderCenter(summary)`에 `overrunEvent` 분기를 붙인다.
- `renderOverrunEvent(summary)` 또는 동등 함수를 만든다.
- A-2가 만든 snapshot field를 **그대로 소비**한다.
- `badge → header → flavor → reward summary → CTA → footnote` 위계를 고정한다.

반대로 아래는 금지다.

- `rewardTitle` 문자열로 슬라임/고블린/리치를 다시 판정하기
- `sourceType`만 보고 app.js에서 새 headline을 다시 만들기
- `none`일 때 summary/CTA 밀도를 줄이기

핵심은 **render가 branch identity를 만들지 않는다는 점**이다.

---

## ending과의 차이

### ending이 맡는 것
- 승패 결과
- 정산 정보
- 버튼 의미

### `overrunEvent`가 맡는 것
- 전리품 장면화
- branch별 flavor
- 다음 권 직전의 집중 카드

그래서 두 화면은 같은 카드처럼 보여선 안 된다.

---

## A-3 완료선

아래 네 가지가 모두 성립해야 A-3가 닫힌다.

1. `overrunEvent`가 결과표가 아니라 장면 카드로 읽힌다.
2. 3축(theme / enemy / none) 모두 같은 카드 밀도를 유지한다.
3. app.js에 branch 재추론 코드가 없다.
4. CTA가 `확인 후 적용` 의미로 읽힌다.

---

## owner별 포인트

### 프로그래머
- `src/app.js` 분기/렌더 추가
- `src/styles.css` shell/tone 클래스 추가
- 필요 시 render sanity smoke 추가

### UI
- 중앙 카드 1장 위계 확정
- ending보다 더 집중된 레이아웃 보정
- `none`도 비어 보이지 않게 neutral density 유지

### 아트
- backplate / accent / badge tone 최소 패키지
- theme/enemy/none 3축 tone 차이 정리

### QA
- theme / enemy / none 캡처
- ending과 `overrunEvent` 비교 캡처
- CTA semantics sanity 확인

---

## 다음 작업

A-3가 닫히면 다음 A-4는 더 단순해진다.

- confirm CTA 시점 resolve
- reward 적용 + 다음 세그먼트 진입
- cleanup / resolved guard
- S1~S12 + recovery 최종 고정

즉 이번 문서는
**payload가 이미 고정된 상태에서, 그 payload를 어떤 카드 위계로 보여 줄지 commit 단위로 고정하는 문서**다.