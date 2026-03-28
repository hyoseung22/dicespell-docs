# DiceSpell ending ↔ overrunEvent 온보딩 경계 패킷

## 문서 목적

이 문서는 이미 충분히 닫혀 있는 두 문서 묶음,

- `docs/dicespell_overrun_event_*`
- `docs/dicespell_first_run_onboarding_*`

사이의 **마지막 공유 경계면**만 따로 고정하는 production handoff 문서다.

지금까지의 문서화로 `overrunEvent` 자체도, 첫 런 primer 자체도 각각은 구현 착수 가능한 수준까지 정리됐다.
하지만 실제 구현 순서로 들어가면 여전히 한 군데에서 망설일 수 있다.

> `ending` 화면과 `overrunEvent` 화면이 모두 “책을 끝낸 직후”를 다루는데, 정확히 어디까지가 ending의 책임이고 어디부터가 overrunEvent의 책임인가?

이 질문이 흐리면 아래 문제가 동시에 생긴다.

- 프로그래머는 `renderEnding()`에 너무 많은 문맥을 남기거나, 반대로 `overrunEvent`가 설명해야 할 것을 미리 먹어 버릴 수 있다.
- UI는 `ending primer`, `오버런 전리품 보관함`, `overrunEvent`를 같은 밀도/같은 위계의 카드로 그려 계층이 무너질 수 있다.
- 아트는 `결과 화면 polish`와 `경계 이벤트 장면`을 같은 자산으로 처리해 구분감을 잃을 수 있다.
- QA는 `승리 직후 primer`, `오버런 계속 CTA 보조 문구`, `overrunEvent confirm` 중 무엇이 언제 나와야 pass인지 헷갈릴 수 있다.

즉 이 문서는 새 기획이 아니라,
**이미 있는 두 handoff 묶음이 shared surface에서 충돌하지 않게 하는 경계 계약서**다.

---

## 1. 한 줄 결론

> `ending`은 **결과와 버튼 의미를 설명하는 화면**이고,
> `overrunEvent`는 **선택한 전리품을 들고 다음 권으로 넘어가는 장면**이다.

둘 다 필요하지만, 둘이 같은 말을 하면 실패다.

---

## 2. 왜 이 문서가 지금 필요한가

현재 기준으로 문서 공백이 큰 영역은 이미 대부분 닫혀 있다.

- `overrunEvent`는 phase, snapshot, contentKey, screen packet, acceptance, smoke migration까지 있다.
- 첫 런 온보딩도 library / battle / reward / shop / ending primer, `meta.uiGuidance`, content deck, owner cutsheet까지 있다.

그런데 실제 구현 순서를 보면 **공유 표면은 `ending` 하나**다.

### 지금 남아 있는 마지막 오해 포인트

1. `ending primer`가 `오버런 계속` 버튼의 의미를 설명해야 한다는 사실과,
   `overrunEvent`가 실제로 다음 권으로 넘어가는 감정선을 담당한다는 사실이 한 문장으로 묶여 있지 않다.
2. `오버런 전리품 보관함`이 이미 ending 안에 있기 때문에,
   구현자가 `overrunEvent`를 새 장면이 아니라 ending의 하위 카드처럼 취급할 위험이 있다.
3. 온보딩 ending primer는 `무엇이 남는가`까지만 말해야 하는데,
   잘못 붙이면 `선택한 전리품을 챙기고 넘어간다`는 flavor까지 먹어 버릴 수 있다.
4. `overrunEvent` 도입 전후 모두 견뎌야 하는 ending primer의 밀도와 카피 경계가 아직 cross-doc 기준으로 한 장에 고정돼 있지 않다.

즉 이 문서는,
`overrunEvent`와 온보딩 ending primer가 모두 맞는 상태에서 **둘의 경계만 따로 못 박는 문서**다.

---

## 3. 책임 경계 요약

## 3.1 ending이 책임지는 것

`ending`은 아래만 책임진다.

1. 이번 런/세그먼트 결과가 무엇인가
2. 지금 남는 진척이 무엇인가
3. 지금 누르는 버튼이 어떤 분기인가
4. 오버런이 가능하다면, `오버런 계속`이 “다음 장면으로 넘어가는 버튼”이라는 점

즉 ending은 **결과 해석 + CTA 의미 설명**까지만 한다.

## 3.2 overrunEvent가 책임지는 것

`overrunEvent`는 아래를 책임진다.

1. 방금 선택한 전리품을 다시 보여 주기
2. 그 전리품의 출처(`themeTrophy` / `enemyTrophy` / `none`)를 장면으로 읽히게 만들기
3. `다음 권으로 들고 넘어가는 순간`을 짧게 고정하기
4. confirm 시점에 실제 적용이 일어난다는 감각을 만들기

즉 `overrunEvent`는 **경계 장면 + 선택 재확인 + 감정선 고정**이 책임이다.

## 3.3 둘 다 하면 안 되는 것

- 둘 다 같은 flavor 문장을 반복하는 것
- 둘 다 같은 위계의 큰 카드로 경쟁하는 것
- 둘 다 `전리품 설명`을 다시 상세 반복하는 것
- 둘 다 `다음에 무엇이 벌어지는가`를 장문으로 떠드는 것

---

## 4. 상태별 화면 책임 분리표

| 상태 | ending의 역할 | overrunEvent의 역할 | 금지 사항 |
| --- | --- | --- | --- |
| 패배 ending | `서고 기록이 남는다`, `다음 런으로 돌아간다`를 설명 | 없음 | 패배 화면에 가짜 overrun flavor 추가 |
| 승리 + 오버런 불가 ending | `완주 보너스/정산 결과` 설명 | 없음 | 다음 권 감정선 카피를 억지로 넣기 |
| 승리 + 오버런 가능 ending | `오버런 계속` 버튼 의미, 현재 정산/보관함 선택 상태 설명 | 아직 진입 전 | ending 본문이 `다음 권 장면`까지 먹는 것 |
| overrunEvent(themeTrophy) | 없음. 이미 ending에서 역할 종료 | 테마 전리품을 들고 넘어가는 장면화 | ending 카피 재탕 |
| overrunEvent(enemyTrophy) | 없음. 이미 ending에서 역할 종료 | 적 잔해/특수 전리품을 챙기는 장면화 | 결과 정산 문구 재노출 |
| overrunEvent(none) | 없음. 이미 ending에서 역할 종료 | 빈손으로 넘어가는 것도 정상 장면으로 고정 | 경고/실패 화면처럼 보이기 |

핵심은 아래다.

- `ending`은 **설명 화면**이다.
- `overrunEvent`는 **경계 장면**이다.

---

## 5. 카피 경계 규칙

## 5.1 ending primer 문장 규칙

ending primer는 아래 톤까지만 허용한다.

- 무엇이 남는가
- 무엇이 정산되는가
- 버튼이 무엇을 뜻하는가

예시 톤:

- `패배해도 이번 런에서 모은 서고 기록은 남습니다.`
- `완주 보너스를 챙긴 뒤 다음 권으로 이어갈 수 있습니다.`
- `오버런 계속은 다음 권 진입 전 준비 장면으로 이어집니다.`

### ending primer 금지 문장

- `방금 고른 전리품을 움켜쥐고 다음 권의 표지를 넘깁니다.`
- `리치의 잔향이 책장 사이를 맴돕니다.`
- `붕락 응축핵이 손 안에서 식지 않은 채...`

이건 전부 `overrunEvent`가 할 말이다.

## 5.2 overrunEvent 문장 규칙

overrunEvent는 반대로 아래 톤을 사용한다.

- 선택한 전리품의 출처와 무드
- 지금 막 넘어가는 감각
- confirm 직전의 짧은 장면

예시 톤:

- `점액 잔해를 눌러 담은 병이 아직 미지근하다.`
- `붕락 정령의 응축핵이 금 가는 소리를 내며 들썩인다.`
- `이번엔 아무 전리품도 들지 않은 채 다음 권으로 넘어간다.`

### overrunEvent 금지 문장

- `패배해도 진척은 남습니다.`
- `완주 보너스가 적용됩니다.`
- `오버런 계속 버튼은 다음 권으로 이어집니다.`

이건 전부 `ending`이 이미 말했어야 한다.

---

## 6. 정보 위계 규칙

## 6.1 ending 화면 위계

1. 결과 헤더 / 결과 값
2. 정산 정보
3. 오버런 전리품 보관함 선택
4. CTA 의미 설명용 primer line
5. CTA 버튼

즉 ending primer는 어디까지나 **결과 해석 보조선**이다.
큰 hero-card가 되면 실패다.

## 6.2 overrunEvent 화면 위계

1. 중앙 카드 1장
2. sourceType 배지 / 헤더 / flavor
3. 선택 전리품 시각 강조
4. confirm CTA
5. skip/none fallback 보조 문장

즉 overrunEvent는 **중앙 카드 1장 장면**이어야 한다.
ending처럼 표/정산/다중 선택지가 다시 나오면 실패다.

---

## 7. 구현 순서 경계

## Step A. overrunEvent를 먼저 독립시킨다

프로그래머는 먼저 아래를 닫는다.

- `continueOverrun()`를 enter wrapper로 축소
- `overrunEvent` snapshot 생성
- `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet` source-of-truth 고정
- confirm 시점 resolve
- recovery/smoke 이관

이 단계가 끝나야 `ending`의 책임이 실제로 줄어든다.

## Step B. 그 다음 ending primer를 얹는다

온보딩 ending primer는 반드시 **overrunEvent가 분리된 이후 기준**으로 밀도를 확정한다.

왜냐하면 이 순서를 바꾸면,
구버전 ending 문맥에 맞춰 장문 primer를 올렸다가 나중에 다시 깎는 식의 중복 작업이 생기기 때문이다.

## Step C. overrunEvent 이후 ending primer 재검수

마지막으로 QA/UI는 아래를 다시 확인한다.

- ending primer가 여전히 버튼 의미만 설명하는가
- overrunEvent가 flavor와 장면을 독점하는가
- 두 화면이 같은 크기/같은 위계/같은 카피 구조로 경쟁하지 않는가

---

## 8. 파일/함수별 source-of-truth 경계

| 영역 | 파일/함수 | source-of-truth | 하면 안 되는 것 |
| --- | --- | --- | --- |
| ending 결과/CTA primer | `src/app.js` `renderEnding(summary)` + onboarding helper | `docs/dicespell_first_run_onboarding_*` | `contentKey` flavor를 다시 조립 |
| 오버런 경계 장면 | `src/app.js` `renderOverrunEvent(summary)` | `docs/dicespell_overrun_event_*` | 결과 정산 문맥 재설명 |
| ending seen 상태 | `meta.uiGuidance.seenVictoryEndingPrimer`, `seenOverrunPrimer` | onboarding 문서 묶음 | `overrunEvent` snapshot에 seen 상태 저장 |
| overrunEvent 내용 상태 | `ending.overrunDraft` → `overrunEvent` snapshot | overrunEvent 문서 묶음 | UI가 reward title 문자열만으로 branch 재판정 |
| QA 종료선 | onboarding acceptance + overrun acceptance | 두 acceptance 문서 동시 참조 | 한쪽 smoke만 pass하고 종료 선언 |

---

## 9. 역할별 handoff 메모

## 9.1 프로그래머

### 넘겨야 할 것

- `ending`은 result/cta primer만 남긴다.
- `overrunEvent`는 snapshot 기반 단일 장면으로 분리한다.
- ending primer helper는 `contentKey`를 만들지 않는다.
- overrunEvent는 onboarding seen 플래그를 읽지 않는다.

### 완료선

- `renderEnding()`에서 flavor drift가 없다.
- `renderOverrunEvent()`에서 결과 정산 문구가 없다.
- confirm 이후 1회 resolve만 일어난다.

## 9.2 UI

### 넘겨야 할 것

- ending primer는 inline/badge 레벨
- overrunEvent는 중앙 카드 1장 레벨
- 두 화면이 전혀 다른 계층으로 읽히는 레이아웃

### 완료선

- 사용자는 텍스트를 다 읽지 않아도 `결과 화면`과 `경계 장면`을 구분할 수 있다.
- ending 쪽이 overrunEvent를 미리 소비하지 않는다.

## 9.3 아트

### 넘겨야 할 것

- ending용은 얇은 배지/accent
- overrunEvent용은 header/backplate/focus accent
- `none`도 정상 장면처럼 보이는 최소 세트

### 완료선

- ending과 overrunEvent가 같은 카드 변주처럼 보이지 않는다.
- `theme / enemy / none` 구분이 텍스트 없이도 최소한 남는다.

## 9.4 QA

### 넘겨야 할 것

- 패배 ending
- 승리 ending (오버런 불가)
- 승리 ending (오버런 가능)
- overrunEvent theme/enemy/none
- overrunEvent 도입 전후 ending primer 밀도 비교

### 완료선

- ending은 결과와 버튼 의미만 설명한다.
- overrunEvent는 장면과 flavor만 책임진다.
- 양쪽 카피가 서로를 침범하지 않는다.

---

## 10. acceptance 교차 체크리스트

### A. ending pass

- [ ] 패배 ending primer가 `남는 진척`만 설명한다
- [ ] 승리 ending primer가 `완주 보너스/오버런 버튼 의미`만 설명한다
- [ ] ending 본문이 선택 전리품 flavor를 먹지 않는다
- [ ] CTA 주변 서브카피가 overrunEvent 헤더/flavor를 미리 반복하지 않는다

### B. overrunEvent pass

- [ ] sourceType별 헤더/배지/flavor가 중앙 카드 1장에 고정된다
- [ ] confirm CTA가 실제 적용 시점과 일치한다
- [ ] `none`도 정상 장면처럼 보인다
- [ ] result/정산 설명 문구가 다시 나오지 않는다

### C. cross-surface pass

- [ ] ending → overrunEvent로 넘어갈 때 문장 역할이 바뀐다
- [ ] 두 화면이 같은 정보 위계처럼 보이지 않는다
- [ ] QA가 카피 중복을 스크린샷만으로도 판정할 수 있다

---

## 11. 리스크 & 후속 과제

- 가장 큰 리스크는 `ending primer를 친절하게 만들수록 overrunEvent가 할 말을 빼앗기기 쉽다`는 점이다.
- 두 번째 리스크는 `오버런 전리품 보관함`이 이미 ending 안에 있어서, 구현자가 `overrunEvent`를 새 phase가 아니라 ending 확장 카드처럼 축소 해석할 수 있다는 점이다.
- 세 번째 리스크는 `none` 상태를 `전리품이 없으니 비어 있는 상태`로 처리해 장면성이 사라지는 것이다.

### 다음 후속 과제

1. `overrunEvent` Step 0~4 컷오버 시 본 문서의 경계 규칙을 smoke fixture 이름과 스크린샷 캡처 체크리스트에 직접 반영한다.
2. `도서관 + 첫 전투 primer` 구현 이후에도 ending primer는 본 문서 기준으로 다시 재점검해, 온보딩이 끝으로 갈수록 장문이 되는 drift를 막는다.
3. 공개 포털의 readable companion에서도 이 경계를 별도 요약해, 비개발 오너가 `왜 화면이 두 장으로 나뉘는가`를 빠르게 이해하게 만든다.

---

## 관련 문서

- `docs/dicespell_overrun_event_handoff_kr.md`
- `docs/dicespell_overrun_event_implementation_packet_kr.md`
- `docs/dicespell_overrun_event_screen_packet_kr.md`
- `docs/dicespell_overrun_event_production_cutsheet_kr.md`
- `docs/dicespell_overrun_event_acceptance_matrix_kr.md`
- `docs/dicespell_overrun_event_integration_workorder_kr.md`
- `docs/dicespell_overrun_event_source_of_truth_map_kr.md`
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_overrun_event_content_deck_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_kr.md`
- `docs/dicespell_first_run_onboarding_screen_packet_kr.md`
- `docs/dicespell_first_run_onboarding_integration_workorder_kr.md`
- `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- `docs/dicespell_first_run_onboarding_source_of_truth_map_kr.md`
- `docs/dicespell_first_run_onboarding_production_cutsheet_kr.md`
- `docs/dicespell_first_run_onboarding_content_deck_kr.md`
