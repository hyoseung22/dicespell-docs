# DiceSpell `overrunEvent` / 첫 런 온보딩 비주얼 자산 요약

## 3줄 요약
- 이 문서는 `overrunEvent` Commit 2와 온보딩 B-1~B-4가 이미 기능/카피 문서로는 충분한 상태에서, **UI/아트 자산 handoff만 한 번에 읽히게** 만든 readable companion이다.
- 핵심은 새 화면을 더 만드는 것이 아니라 `scene card`, `hero-primer`, `inline hint`, `ending helper`를 **같은 브랜드 감도, 다른 레이어 책임**으로 정렬하는 것이다.
- 즉 이번 문서의 목적은 `무엇을 그릴까`보다 `이번 턴에 무엇만 만들고 무엇은 아직 만들지 말아야 하나`를 3~5분 안에 읽게 하는 데 있다.

## 한 줄 목표

`overrunEvent`와 온보딩이 같은 visual family를 공유하되, `장면`, `해석`, `결과 보조`가 한 카드처럼 뭉개지지 않게 만든다.

## 왜 중요한가

지금 DiceSpell은 문서가 부족한 상태가 아니다. 오히려 다음 위험이 남아 있다.

- `overrunEvent` 장면 카드가 ending 결과표처럼 읽히는 위험
- primer surface들이 전부 같은 패널 밀도로 평준화되는 위험
- 아트가 실제로는 token sanity만 필요해도 대형 자산 제작이 필요하다고 오해하는 위험
- QA/PM이 visual green과 runtime green을 같은 문장으로 기록하는 위험

그래서 이번 요약은 **비주얼 자산 handoff를 역할별 제작 묶음으로 다시 압축**한다.

---

## 첫 화면 결론

### 누가 어떤 부분을 읽어야 하나
- **프로그래머**: `surface bundle 매트릭스`, `프로그래머 handoff`, `즉시 다음 액션`
- **UI 오너**: `density / hierarchy rule`, `UI handoff`, `surface bundle 매트릭스`
- **아트 오너**: `최소 자산 패키지`, `아트 handoff`, `surface bundle 매트릭스`
- **QA/PM**: `acceptance evidence`, `리스크 & 후속과제`

### 가장 짧은 답
- `scene card`는 장면을 만들고,
- `primer`는 지금 뭘 봐야 하는지 해석을 돕고,
- `ending helper`는 결과/CTA 의미만 보조한다.

이 셋이 하나의 카드처럼 보이면 visual handoff 실패다.

---

## surface bundle 한눈표

| surface | 이번 턴 visual 목적 | 꼭 필요한 것 | 만들지 말아야 할 것 |
| --- | --- | --- | --- |
| Commit 2 `overrunEvent` | ending 뒤의 **장면 카드**를 닫기 | 중앙 카드 shell, badge/header/flavor/CTA 위계, display-only reward summary, `none` neutral variant | ending 결과표 재사용, onboarding helper 혼입 |
| B-1 library | 첫 책 선택의 **hero-primer** | primer panel 1종, highlight callout, 최소 token | shelf 전면 리뉴얼 |
| B-2 battle | entry/first-spell/auto-pass **trigger 힌트** | hero-primer 1종 + inline hint 2종 | 전투 UI 전체 재배치 |
| B-3 reward/shop | reward vs shop **목적 분리** | reward/shop primer 2종, accent split | 두 surface를 같은 strip으로 단순 병합 |
| B-4 ending | 결과/CTA 의미만 **보조** | helper 3종, low-density badge/divider | `overrunEvent` flavor 장면 |

---

## density rule

### 1) `scene-card`
- 가장 밀도가 높다.
- badge → header → flavor → reward summary → CTA → footnote 순서가 보이면 된다.
- primer성 설명을 먹으면 안 된다.

### 2) `hero-primer`
- 중간 밀도다.
- 무엇을 봐야 하는지 빠르게 알려 주되, 원래 surface를 점유하면 안 된다.

### 3) `inline-hint`
- 가장 작고 빠르게 읽혀야 한다.
- 전투 조작 근처에서 action을 막지 않아야 한다.

### 4) `ending helper`
- 가장 낮은 밀도다.
- CTA 의미만 보조하고 flavor는 `overrunEvent`로 넘긴다.

---

## 역할별 handoff 핵심

### 프로그래머
- `scene-card`, `hero-primer`, `inline-hint`, `result-helper`를 **서로 다른 class family**로 유지한다.
- `badgeLabel`, `accentKey`, `artTokenSet`은 입력값을 소비하고 render가 다시 추론하지 않는다.
- `reward/shop`을 한 helper로 축약하지 않는다.

### UI 오너
- 같은 family라도 같은 밀도로 만들지 않는다.
- `scene card`는 장면,
- `primer`는 해석,
- `ending helper`는 버튼 의미만 보여 줘야 한다.

### 아트 오너
- 기본값은 새 대형 자산이 아니라 token/accent sanity다.
- 특히 `none`, `reward/shop`, `ending`은 작은 만큼 drift가 잘 나므로 우선 체크한다.
- 새 key art가 진짜 필요한 경우만 예외 승인한다.

### QA
- visual 검수는 `예쁜가`보다 `레이어 책임이 지켜졌는가`로 본다.
- Commit 2는 scene card 분리 캡처,
- B-track은 surface별 primer/helper 캡처를 독립 제출물로 남긴다.

---

## 최소 자산 패키지

| 묶음 | 기본값 |
| --- | --- |
| `overrunEvent scene-card` | 기존 카드 shell 재활용 + token 조정 |
| `library hero-primer` | 기존 shelf tone 재활용 |
| `battle hint` | 기존 status chip 재활용 |
| `reward/shop primer` | 기존 card/chip 재활용 |
| `ending helper` | 기존 ending CTA row 재활용 |

즉, **새 대형 자산은 기본값이 아니다.**

---

## acceptance evidence

| 단계 | 남겨야 하는 visual evidence |
| --- | --- |
| Commit 2 | `overrunEvent` 카드 + CTA + ending과 다른 shell 확인 |
| B-1 | 첫 진입 primer / 재진입 미노출 |
| B-2 | entry / first-spell / auto-pass |
| B-3 | reward / shop / boss draft coexist |
| B-4 | defeat / victory / overrun CTA helper + flavor 비혼입 메모 |

---

## 리스크 & 후속과제

- `scene card`와 `ending helper`가 같은 카드처럼 합쳐지지 않게 계속 분리 유지
- primer surface가 전부 같은 패널처럼 평준화되지 않게 surface별 hierarchy note 유지
- 아트가 대형 자산이 필요하다고 오판하면 예외 승인 사유를 먼저 기록
- visual green과 runtime green을 같은 문장으로 쓰지 않기

---

## 즉시 다음 액션

1. Commit 2 착수 전 `commit2 closing surgery sheet -> visual asset packet -> A-3 UI packet` 순서로 읽는다.
2. `scene-card` 최소 shell/token만 먼저 닫고 `ending helper`와 공유하지 않는다.
3. B-track은 A-track green 뒤에만 열고, 각 surface에서 최소 자산 패키지만 사용한다.
4. tracker/run log에는 이번 작업을 `비주얼 자산 handoff 정렬`로 기록한다.
