# DiceSpell ending ↔ overrunEvent 경계 요약 페이지

## 이 문서는 무엇인가
이 문서는 `dicespell_ending_overrun_boundary_packet_kr.md`의 **읽기용 요약본**이다.

이미 정리된
- `overrunEvent` 문서 묶음
- 첫 런 온보딩 문서 묶음

사이에서, 실제 구현 직전 가장 헷갈리기 쉬운 **ending 화면과 overrunEvent 화면의 역할 경계**만 빠르게 이해하게 만드는 용도다.

---

## 한 줄 목표
> `ending`은 **결과와 버튼 의미를 설명**하고,
> `overrunEvent`는 **선택한 전리품을 들고 다음 권으로 넘어가는 장면**을 맡는다.

둘은 이어지지만, 같은 말을 하면 실패다.

---

## 왜 이 문서가 필요한가
현재 DiceSpell은 이미 아래 문서들이 충분히 닫혀 있다.

- `overrunEvent` handoff / implementation / screen / acceptance / workorder / content deck
- 첫 런 onboarding handoff / screen / workorder / acceptance / cutsheet / content deck

문제는 실제 구현 순서로 들어가면 두 축이 모두 `ending` 근처를 건드린다는 점이다.

대표적인 충돌 위험은 아래와 같다.

1. ending primer가 너무 많은 flavor를 먹어 `overrunEvent`가 할 말을 빼앗는다.
2. `오버런 전리품 보관함`이 이미 ending 안에 있어서, `overrunEvent`가 별도 장면이 아니라 ending 확장 카드처럼 축소 해석된다.
3. UI/아트가 ending과 overrunEvent를 같은 위계의 카드로 그려, 결과 화면과 경계 장면의 차이가 사라진다.
4. QA가 `버튼 의미 설명`과 `다음 권으로 넘어가는 장면`을 같은 acceptance로 잘못 묶는다.

즉 이 문서는 새로운 시스템을 설계하는 게 아니라,
**이미 있는 두 시스템이 shared surface에서 충돌하지 않게 경계를 고정하는 문서**다.

---

## 책임 경계 한눈에 보기

### ending이 해야 할 일
- 이번 결과가 무엇인지 설명
- 무엇이 남는지 설명
- `오버런 계속` 버튼이 무엇을 뜻하는지 설명
- 정산/완주 보너스/진척 문맥 정리

### overrunEvent가 해야 할 일
- 방금 고른 전리품을 다시 보여 주기
- `themeTrophy / enemyTrophy / none` 장면 차이 만들기
- 다음 권으로 들고 넘어가는 감각 고정
- confirm 시점의 짧은 감정선 만들기

### 둘 다 하면 안 되는 일
- 같은 flavor 문장 반복
- 같은 크기/같은 위계의 카드 경쟁
- 전리품 설명과 CTA 설명을 양쪽에서 동시에 재탕

---

## 상태별 책임 분리

### 패배 ending
- ending만 존재
- `서고 기록이 남는다`를 설명
- overrunEvent 없음

### 승리 ending + 오버런 불가
- ending만 존재
- 완주 보너스/정산 결과 설명
- overrunEvent 없음

### 승리 ending + 오버런 가능
- ending은 `오버런 계속` 버튼 의미까지만 설명
- 아직 flavor를 크게 먹지 않음
- 실제 장면은 다음 `overrunEvent`에서 처리

### overrunEvent(theme/enemy/none)
- 결과 설명은 이미 끝났다고 보고 시작
- 중앙 카드 1장으로 전리품/빈손 상태를 장면화
- `다음 권으로 넘어간다`는 감각을 여기서 마무리

---

## 카피 규칙

### ending 카피는 이렇게
- `패배해도 이번 런에서 모은 기록은 남습니다.`
- `완주 보너스를 챙긴 뒤 다음 권으로 이어갈 수 있습니다.`
- `오버런 계속은 다음 권 진입 전 준비 장면으로 이어집니다.`

즉 **결과와 버튼 의미**만 말한다.

### overrunEvent 카피는 이렇게
- `점액 잔해를 눌러 담은 병이 아직 미지근하다.`
- `붕락 응축핵이 손 안에서 들썩인다.`
- `이번엔 아무 전리품도 들지 않은 채 다음 권으로 넘어간다.`

즉 **전리품과 경계 장면의 flavor**를 말한다.

---

## 화면 위계 규칙

### ending
- 결과 헤더
- 정산 정보
- 오버런 전리품 보관함
- 짧은 primer line
- CTA 버튼

ending primer는 어디까지나 **얇은 보조선**이어야 한다.
hero-card가 되면 과하다.

### overrunEvent
- 중앙 카드 1장
- sourceType 배지 / 헤더 / flavor
- 전리품 강조
- confirm CTA

즉 overrunEvent는 **경계 장면**처럼 보여야 한다.
정산표처럼 보이면 실패다.

---

## 구현 순서 요약

1. 먼저 `overrunEvent`를 실제 phase로 독립시킨다.
2. 그 다음 ending primer를 `결과/버튼 의미`만 남기도록 얹는다.
3. 마지막으로 ending ↔ overrunEvent가 같은 말/같은 위계로 경쟁하지 않는지 교차 QA 한다.

핵심은,
**ending를 먼저 과하게 키우지 말고 overrunEvent 분리 후에 ending 밀도를 정리해야 한다**는 점이다.

---

## 역할별 메모

### 프로그래머
- ending helper는 flavor를 만들지 않는다
- overrunEvent는 snapshot/contentKey 기반으로만 그린다
- onboarding seen 플래그와 overrunEvent snapshot을 섞지 않는다

### UI
- ending은 inline/badge 레벨
- overrunEvent는 중앙 카드 1장 레벨
- 사용자가 텍스트를 다 안 읽어도 두 화면 계층이 달라 보여야 한다

### 아트
- ending은 얇은 배지/accent
- overrunEvent는 header/backplate/focus accent
- `none`도 비어 보이지 않게 최소 장면성을 준다

### QA
- ending은 결과/버튼 의미만 설명하는지 확인
- overrunEvent는 장면/flavor만 책임지는지 확인
- 양쪽 카피가 서로를 침범하지 않는지 본다

---

## 한 줄 결론
> `ending`은 플레이어가 **방금 끝난 결과를 이해하게 만드는 화면**이고,
> `overrunEvent`는 플레이어가 **방금 고른 전리품을 들고 다음 권으로 넘어간다고 느끼게 만드는 화면**이다.

둘 다 필요하지만, 둘은 서로의 일을 대신하면 안 된다.

---

## 원본 문서
- 원본 경계 패킷: `./dicespell_ending_overrun_boundary_packet_kr.md`
- OverrunEvent handoff: `./dicespell_overrun_event_handoff_kr.md`
- OverrunEvent screen packet: `./dicespell_overrun_event_screen_packet_kr.md`
- OverrunEvent content deck: `./dicespell_overrun_event_content_deck_kr.md`
- Onboarding handoff: `./dicespell_first_run_onboarding_handoff_kr.md`
- Onboarding screen packet: `./dicespell_first_run_onboarding_screen_packet_kr.md`
- Onboarding content deck: `./dicespell_first_run_onboarding_content_deck_kr.md`
