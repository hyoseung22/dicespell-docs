# DiceSpell overrunEvent ↔ 첫 런 온보딩 계약 예시 요약

## 이 문서는 무엇인가
이 문서는 `dicespell_overrun_onboarding_contract_examples_kr.md`의 읽기용 요약본이다.

지금 DiceSpell은 방향 문서가 부족한 상태가 아니다.
오히려 문서는 충분한데, 실제 구현 직전에

- snapshot이 어떤 모양이어야 하는지
- primer payload가 얼마나 얇아야 하는지
- ending과 overrunEvent가 어디서 갈라져야 하는지
- QA가 어떤 예시 상태를 기준으로 pass/fail을 봐야 하는지

를 한 장에서 보기 어렵다는 작은 병목이 남아 있다.

이 요약은 그 예시 기준만 짧게 묶는다.

---

## 한 줄 목표
> 다음 구현자는 `overrunEvent`와 onboarding 문서를 다시 조합하지 말고,
> canonical example 상태 A~H를 기준으로 snapshot / helper payload / DOM / fixture를 맞춘다.

---

## 왜 이 문서가 필요한가
기존 문서에는 이미 다음이 있다.

- handoff
- implementation packet
- screen packet
- acceptance matrix
- content deck
- cutover master plan
- boundary packet

문제는 문서 부족이 아니라, **실제 예시 상태가 한 장에 안 모여 있다**는 점이다.

즉 지금 필요한 것은 새 설계가 아니라,
`이 상황이면 payload는 이 정도 밀도로 생기고 화면은 이렇게 보여야 한다`는 샘플 교정본이다.

---

## 가장 중요한 기준 4개
모든 branch와 primer는 아래 4개를 먼저 고정한다.

- `contentKey`
- `badgeLabel`
- `accentKey`
- `artTokenSet`

카피는 다듬을 수 있어도, 이 4개가 같으면 같은 branch로 본다.

---

## overrunEvent canonical 예시

### 상태 A — 책 테마 전리품
- 예: `theme.goblin.cache`
- 배지: `책 테마 전리품`
- 의미: 이번 책의 결을 다음 권 첫 장으로 넘김
- 화면: 중앙 카드 1장 + reward card + CTA 1개

### 상태 B — 적 처치 전리품
- 예: `enemy.golem.fragment`
- 배지: `적 처치 전리품`
- 의미: 방금 쓰러뜨린 위협의 잔해를 다음 권 준비물로 씀
- 화면 구조는 상태 A와 같고, tone만 바뀜

### 상태 C — 선택 없음 / fallback
- 예: `fallback.none`
- 배지: `준비 정리`
- 의미: 추가 효과가 없어도 오버런 진행은 끊기지 않음
- 중요한 점: 빈 화면이 아니라 정상적인 1장 카드여야 함

---

## onboarding canonical 예시

### 상태 D — library primer
- key: `library.book-select`
- 말해야 하는 것: 책은 이번 런의 플레이 성격을 정한다
- 하면 안 되는 것: 책 카드보다 primer가 더 커지는 것

### 상태 E — battle entry primer
- key: `battle.entry`
- 말해야 하는 것: 한 턴에는 주문을 한 번만 쓴다

### 상태 F — first spell primer
- key: `battle.first-spell`
- 말해야 하는 것: 주문을 쓰면 그 족보는 이번 페이지 동안 잠긴다

### 상태 G — reward / shop primer
- key: `reward.first`, `shop.first`
- 차이:
  - reward = 다음 1~2전투용 단기 보정
  - shop = 런 전체 방향을 굳히는 장기 투자

### 상태 H — ending primer
- key: `ending.victory`, `ending.overrun-cta`
- 말해야 하는 것: 결과와 버튼 의미
- 하면 안 되는 것: overrun flavor를 미리 먹는 것

---

## 가장 중요한 경계

### ending이 해야 하는 일
- 결과 설명
- 정산 문맥
- 버튼 의미 설명

### ending이 하면 안 되는 일
- slime / goblin / lich / golem / collapse / none flavor 말하기
- 다음 권으로 넘어가는 장면을 hero-card처럼 먼저 소비하기

즉 장면성은 `overrunEvent`, 안내는 `ending primer`가 맡는다.

---

## owner별 바로 쓸 결론

### 프로그래머
- snapshot과 helper payload를 예시 상태 A~H 밀도로 만든다
- render는 branch를 다시 추론하지 말고 payload를 소비한다

### UI
- `overrunEvent`는 중앙 카드 1장
- onboarding은 안내 레이어
- ending primer는 얇게 유지

### 아트
- 대부분 차이는 구조 차이가 아니라 토큰 차이로 해결한다
- `overrunEvent`와 onboarding은 같은 장면 카드가 아니다

### QA
- 카피 일부보다 `contentKey`, `phase`, `CTA semantics`, `density`, `fallback`을 먼저 본다

---

## 한 줄 결론
> 지금 DiceSpell의 마지막 문서 공백은 방향이 아니라 **같은 샘플을 함께 보는 일**이다.
> `overrunEvent`와 onboarding 구현은 이 예시 상태를 기준으로 맞추면 된다.

---

## 원본 문서
- 원본 예시 패킷: `./dicespell_overrun_onboarding_contract_examples_kr.md`
- 컷오버 마스터 플랜: `./dicespell_overrun_onboarding_cutover_master_plan_kr.md`
- boundary packet: `./dicespell_ending_overrun_boundary_packet_kr.md`
- overrun content deck: `./dicespell_overrun_event_content_deck_kr.md`
- onboarding content deck: `./dicespell_first_run_onboarding_content_deck_kr.md`
