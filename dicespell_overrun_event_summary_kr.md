# DiceSpell 오버런 이벤트 요약 페이지

## 빠른 안내

### 3줄 요약
- 이 문서는 `dicespell_overrun_event_handoff_kr.md`의 읽기용 요약본이자, `overrunEvent`가 왜 필요한지 첫 화면에서 이해시키는 companion page다.
- PM/기획은 목적·범위를 먼저, UI/아트는 장면 정체성과 카드 구조를 먼저, 프로그래머/QA는 phase 경계와 상태 적용 타이밍을 먼저 보면 된다.
- 3~5분 안에 `이 작업이 새 보상 시스템이 아니라 경계 장면 추가`라는 점이 읽히도록 구성한다.

**한 줄 목표:** 이미 고른 전리품을 `다음 권으로 들고 넘어가는 순간`으로 짧고 선명하게 보여 준다.

---

## 이 문서는 무엇인가
이 문서는 `dicespell_overrun_event_handoff_kr.md`의 **읽기용 요약본**이다.
노션/컨플에 붙여넣는 원본 handoff 문서와 별개로,
기획자 / PM / UI / 아트 / 프로그래머가 **오버런 이벤트 작업의 핵심 의도와 구현 범위를 짧게 파악**할 수 있도록 정리한다.

---

## 한 줄 목표
> 이미 선택한 오버런 전리품을, 다음 권으로 넘어가기 직전의 **짧은 이벤트 페이지 경험**으로 승격시킨다.

핵심은 새 메카닉을 더 얹는 게 아니다.
현재 선택 구조는 유지한 채, 그 선택이 실제로 `다음 권으로 들고 넘어가는 장면`으로 느껴지게 만드는 것이다.

---

## 왜 필요한가
현재 DiceSpell은 이미 아래 연결이 구현돼 있다.

- late normal / overrun normal 보상에 책 테마 전리품 추가
- 특수 적 전리품 연결
- 책 클리어 엔딩에서 오버런 전리품 보관함 3택 1

문제는 그 다음이 비어 있다는 점이다.

플레이어는 전리품을 골랐지만,
- 실제로 다음 권으로 넘어가는 짧은 장면이 없고
- 구현자는 이것이 엔딩 확장인지, 새 phase인지, 별도 이벤트인지 애매하며
- UI / 아트도 어떤 화면을 준비해야 하는지 기준이 흐리다.

즉 지금 필요한 것은 더 큰 보상 시스템이 아니라,
**선택을 기억점으로 고정하는 bounded 이벤트 페이지**다.

---

## 이번 spec의 핵심 정의
### 제안 구현안
- 새 phase 이름: `overrunEvent`
- 진입 시점: `책 클리어 엔딩 → 오버런 계속` 직후
- 역할: 선택한 전리품을 다시 보여주고, 그 전리품을 챙겨 다음 권으로 넘어간다는 감각을 짧게 고정

이 구조가 중요한 이유는 다음과 같다.

1. `ending` 안에 억지로 밀어 넣지 않고, 화면적 경계를 분명히 한다.
2. 프로그래머 / UI / 아트가 모두 같은 phase 이름으로 이해할 수 있다.
3. 이후 오버런 확장이 생겨도 확장 포인트가 명확해진다.

---

## 플레이어가 실제로 겪는 경험
### 전리품을 선택한 경우
1. 책 클리어
2. 전리품 1개 선택
3. `오버런 계속`
4. `overrunEvent` 페이지 진입
5. 선택한 전리품을 크게 다시 보여줌
6. 짧은 서문 문구 표시
7. `이 전리품을 챙기고 다음 권으로` 버튼
8. 다음 세그먼트 진입

### 선택 없이 넘어간 경우
- 빈손 상태도 별도 페이지로 보여줌
- `이번엔 아무 잔해도 챙기지 않고 넘긴다` 같은 fallback 카피 사용
- 그냥 생략하는 것이 아니라, 선택하지 않은 상태도 하나의 장면으로 처리

핵심은 **skip도 페이지 경험으로 만든다**는 점이다.

---

## 포함 / 제외 범위
### 포함
- `overrunEvent` 새 phase
- 화면 정보 구조
- 버튼 구조
- 전리품 출처별 강조 규칙
- 프로그래머 / UI / 아트 handoff
- smoke 기준

### 제외
- 새 전리품 타입 추가
- 추가 선택지/굴리기 메카닉
- 맵 노드 확장
- 대형 삽화 파이프라인
- 대규모 밸런스 재조정

즉 이 작업은 **현재 메카닉을 유지한 채 phase와 장면만 추가하는 bounded 구현**이다.

---

## 역할별로 봐야 할 핵심
### 프로그래머
- 새 phase `overrunEvent` 추가
- 진입 조건 / 미진입 조건 정의
- `rewardId`와 선택 snapshot을 넘겨줄 상태 구조 필요
- CTA 클릭 시점에 실제 적용되도록 타이밍 고정
- 세이브 호환 fallback 필요

### UI
- 이 화면은 `새 선택 화면`이 아니라 `선택을 확인하고 넘기는 장면`
- 카드 1장 강조형 레이아웃이 핵심
- 장면은 짧고 읽기 쉬워야 하며, 전투 템포를 끊으면 안 됨
- CTA는 단일 주행 버튼 위주가 적합

### 아트
- 새 대형 세계관 일러스트보다
  - 전리품 카드 강조
  - 배경 톤 차별화
  - 소형 장식 / 보관함 / 책장 무드
정도로 bounded하게 잡는 게 적절
- 현재 톤과 어긋나지 않는 오버런 진입 무드가 중요

### QA
- 승리 시 정상 진입되는지
- 패배 시 잘 생략되는지
- 선택 있음 / 없음 모두 정상 렌더되는지
- CTA 이후 다음 세그먼트 진입이 꼬이지 않는지
- 저장/복구 시 상태 꼬임이 없는지

---

## 이 문서를 읽을 때의 판단 기준
이 작업이 잘 정의됐는지 보려면 아래 질문을 보면 된다.

1. 현재 보상 메카닉을 유지하면서도 장면적 기억점이 생기는가?
2. 구현 범위가 bounded돼 있는가?
3. 역할별로 같은 그림을 볼 수 있는가?
4. `ending 확장`이 아니라 `명확한 새 phase`로 이해되는가?
5. 플레이어가 다음 권으로 넘어간다는 감각을 짧고 강하게 받는가?

---

## 한 줄 결론
> 오버런 이벤트 페이지는 새로운 보상 시스템이 아니라,
> **이미 고른 전리품을 ‘다음 권으로 들고 넘어가는 순간’으로 시각화하는 짧은 경계 장면**이다.

---

## 원본 문서
- 원본 handoff: `./dicespell_overrun_event_handoff_kr.md`
- 스크린 패킷: `./dicespell_overrun_event_screen_packet_kr.md`
- Acceptance: `./dicespell_overrun_event_acceptance_matrix_kr.md`
- 구현 패킷: `./dicespell_overrun_event_implementation_packet_kr.md`
- 통합 작업지시: `./dicespell_overrun_event_integration_workorder_kr.md`
- Production Cutsheet: `./dicespell_overrun_event_production_cutsheet_kr.md`
- Smoke Migration: `./dicespell_overrun_event_smoke_migration_packet_kr.md`
- Source of Truth: `./dicespell_overrun_event_source_of_truth_map_kr.md`
- Runtime Patch Map: `./dicespell_overrun_runtime_patch_map_kr.md`
- Runtime Patch Map Summary: `./dicespell_overrun_runtime_patch_map_summary_kr.md`
- First Runtime Slice: `./dicespell_overrun_first_runtime_slice_packet_kr.md`
- First Runtime State Matrix: `./dicespell_overrun_first_runtime_state_matrix_kr.md`
- First Runtime Review: `./dicespell_overrun_first_runtime_review_packet_kr.md`
- First Runtime Delivery: `./dicespell_overrun_first_runtime_delivery_packet_kr.md`
- First Runtime Delivery Summary: `./dicespell_overrun_first_runtime_delivery_summary_kr.md`
- Content Deck: `./dicespell_overrun_event_content_deck_kr.md`
