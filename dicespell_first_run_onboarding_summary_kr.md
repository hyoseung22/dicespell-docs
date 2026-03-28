# DiceSpell 첫 런 온보딩 요약 페이지

## 빠른 안내

### 3줄 요약
- 이 문서는 `dicespell_first_run_onboarding_handoff_kr.md`의 읽기용 요약본이자, 첫 런 온보딩 작업이 왜 필요한지 빠르게 설명하는 companion page다.
- PM/기획은 목적·범위를 먼저, UI/아트는 surface별 메시지 밀도를 먼저, 프로그래머/QA는 상태 저장과 반복 노출 조건을 먼저 보면 된다.
- 3~5분 안에 `큰 튜토리얼 모드`가 아니라 `첫 10분 guidance layer`라는 점이 읽히도록 구성한다.

**한 줄 목표:** 첫 런 플레이어가 무엇을 어느 순서로 배워야 하는지 화면이 먼저 안내하게 만든다.

---

## 이 문서는 무엇인가
이 문서는 `dicespell_first_run_onboarding_handoff_kr.md`의 **읽기용 요약본**이다.
노션/컨플용 원본 handoff 문서를 빠르게 훑기 전에,
기획자 / PM / UI / 아트 / 프로그래머가 **이번 온보딩 작업의 목적과 범위를 3~5분 안에 이해**할 수 있도록 정리한다.

---

## 한 줄 목표
> 첫 런 10분 안에 플레이어가 `책 선택 이유`, `전투 1턴의 구조`, `보상/상점의 차이`, `런 종료 후 남는 것`을 각각 한 번씩 정확히 배우게 만든다.

이 요약의 핵심은 간단하다.
이번 온보딩 작업은 **대형 튜토리얼 모드**를 만드는 게 아니라,
이미 좋은 시스템이 초반 이탈 없이 읽히도록 **짧고 bounded한 가이드 레이어**를 얹는 작업이다.

---

## 왜 필요한가
현재 DiceSpell은 시스템 자체는 꽤 선명하다.

- 한 턴 = 한 주문 전투 규칙
- 책별 정체성과 고유 규칙 차별화
- 보상 → 상점 → 엔딩 → 오버런 연결 구조
- 오버런 경계 이벤트 handoff까지 정리된 상태

문제는 **처음 들어온 플레이어가 무엇부터 이해해야 하는지 순서가 약하다**는 점이다.

대표적인 초기 이탈 포인트는 아래 4개다.

1. 책 선택 화면에서 무엇을 먼저 읽어야 하는지 모르기 쉽다.
2. 첫 전투에서 규칙 우선순위가 한 번에 너무 많이 들어온다.
3. reward와 shop의 차이를 초반에 직관적으로 배우기 어렵다.
4. 실패/클리어/오버런에서 무엇이 남는지 한 번에 이해하기 어렵다.

즉, 지금 필요한 것은 설명을 더 많이 붙이는 것이 아니라,
**초보자가 처음 10분 동안 무엇을 먼저 배워야 하는지를 화면이 순서대로 알려주는 것**이다.

---

## 이번 작업 범위
### 포함
- 첫 책 선택 guidance
- 첫 전투 primer
- 첫 reward / 첫 shop primer
- 첫 ending / 첫 overrun primer
- 역할별 handoff 기준
- 최소 상태 저장(`seen` 플래그류)
- smoke / 수동 QA 기준

### 제외
- 별도 튜토리얼 맵
- 고정 샘플 전투
- 장문 도움말/백과사전 UI
- 신규 메타 보상
- 대형 컷신/일러스트 파이프라인

즉, 이번 작업은 **새 모드 제작이 아니라 실서비스형 첫 런 guidance layer**다.

---

## 핵심 해결 방식: 4개 surface
이번 온보딩은 별도 모드가 아니라 아래 4개 surface에 분산된 짧은 가이드로 설계한다.

### 1) 도서관 책 선택 primer
플레이어가 책 카드에서 **무엇을 먼저 읽어야 하는지**를 배우게 만든다.

핵심 메시지:
- 첫 책은 `추천 운영`과 `주의점`만 먼저 읽어도 충분하다.
- 완주 보너스는 지금 외울 필요 없다.

### 2) 첫 전투 primer
플레이어가 시스템 전체가 아니라 **이번 턴 행동 순서**를 먼저 배우게 만든다.

핵심 메시지:
- 이번 턴엔 주문을 한 번만 쓴다.
- 쓸 수 있는 족보를 먼저 본다.
- 왜 이 주문이 나갔고 무엇이 잠겼는지 로그/카드에서 읽는다.

### 3) 첫 reward / 첫 shop primer
보상과 상점의 차이를 짧게 고정한다.

핵심 메시지:
- reward는 지금 전투 흐름을 바로 바꾸는 선택
- shop은 다음 전투들까지 포함한 장기 투자

### 4) 첫 ending / 첫 overrun primer
결과 화면에서 **무엇이 남고, 지금 누르는 버튼이 무엇을 의미하는지**를 이해시킨다.

핵심 메시지:
- 패배해도 남는 기록이 있다.
- 클리어 후에는 완주 보너스와 오버런 연결이 있다.
- 지금 버튼은 정산인지, 다음 권 진입인지 의미를 짧게 고정해야 한다.

---

## 역할별로 무엇이 필요한가
### 프로그래머
- `seen` 플래그 저장 구조
- 조건부 노출 규칙
- primer 노출 후 다시 반복하지 않는 상태 처리
- 새 시스템 추가가 아니라 기존 UI 위 bounded layer 추가
- `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet` 기준 exact primer payload 고정

### UI
- fullscreen modal 남발 금지
- 현재 화면을 가리지 않는 hero-card / callout / inline primer 우선
- 한 화면 한 메시지 원칙 유지
- 기존 패널 톤과 맞는 bounded 강조

### 아트
- 새 대형 삽화보다 기존 UI 톤과 맞는 소형 가이드 카드 / 배지 / 콜아웃 강조 위주
- 에셋 수를 최소로 유지
- 기존 세계관 톤과 어긋나지 않게 관리

### QA
- 첫 책 선택에서 primer가 정상 노출되는지
- 첫 전투 primer가 과하게 반복되지 않는지
- reward/shop 구분 문구가 적절한 시점에만 노출되는지
- ending/overrun primer가 결과 의미를 오해 없이 전달하는지

---

## 이번 문서를 읽을 때의 판단 기준
이 온보딩 작업이 잘 설계됐는지 보려면 아래 질문을 보면 된다.

1. 첫 런 플레이어가 10분 안에 무엇부터 배워야 하는지가 분명한가?
2. 시스템 설명량이 아니라 **읽는 순서**가 설계됐는가?
3. 기존 전투/보상/오버런 구조를 해치지 않는가?
4. 새 모드가 아니라 실제 서비스형 가이드 레이어로 bounded돼 있는가?
5. 역할별 handoff가 이미 제작자가 바로 움직일 수 있는 수준인가?

---

## 한 줄 결론
> 이번 온보딩 작업은 DiceSpell의 복잡한 시스템을 줄이는 작업이 아니라,
> **좋은 시스템을 처음 10분 안에 읽히게 만드는 최소한의 안내 레일을 까는 작업**이다.

추가로 이번 content deck은 그 안내 레일이 구현 직전에도 다시 문장/배지/accent를 감으로 조합하지 않도록,
`library.book-select` / `battle.entry` / `battle.first-spell` / `battle.auto-pass` / `reward.first` / `shop.first` / `ending.defeat` / `ending.victory` / `ending.overrun-cta` 9개 exact key를 source-of-truth로 고정한다.

---

## 원본 문서
- 원본 handoff: `./dicespell_first_run_onboarding_handoff_kr.md`
- 스크린 패킷: `./dicespell_first_run_onboarding_screen_packet_kr.md`
- Acceptance: `./dicespell_first_run_onboarding_acceptance_matrix_kr.md`
- 통합 작업지시: `./dicespell_first_run_onboarding_integration_workorder_kr.md`
- Production Cutsheet: `./dicespell_first_run_onboarding_production_cutsheet_kr.md`
- Source of Truth: `./dicespell_first_run_onboarding_source_of_truth_map_kr.md`
- Content Deck: `./dicespell_first_run_onboarding_content_deck_kr.md`
