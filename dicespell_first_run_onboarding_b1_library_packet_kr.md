# DiceSpell 첫 런 온보딩 B-1 Library Primer 컷오버 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩의 첫 실제 구현 묶음인 **B-1 library primer**를 commit-level handoff로 좁힌다.
- 프로그래머는 `readMetaProgress()` / `persistMetaProgress()` / `renderGrimoireShelf()` 현재 anchor와 `seenBookSelectPrimer` 저장 경계를 먼저 보면 된다.
- UI/아트/QA는 `hero-primer 1장 + openingPlan/caution 강조 2곳 + 책 선택 방해 금지`만 기억하면 된다.

**한 줄 목표:** 첫 책 선택 화면에서 플레이어가 `무엇부터 읽어야 하는가`를 한 화면 안에 고정하되, 새 phase를 만들지 않고 기존 도서관 surface 안에서 닫는다.

### 왜 이 문서가 필요한가
- 온보딩 문서 묶음은 이미 방향, screen packet, workorder, acceptance, cutsheet, content deck까지 충분히 닫혀 있다.
- 하지만 실제 구현 직전 기준으로는 여전히 `좋아, 그럼 B-1 첫 커밋에서는 정확히 어디를 건드리고 무엇을 일부러 하지 않지?`가 한 장으로 닫혀 있지 않다.
- 이 문서는 그 공백만 메운다. 즉 **B-1 library primer를 실제 파일/검수/owner handoff 단위로 좁히는 실행 패킷**이다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | 2. 현재 runtime anchor, 5. B-1 범위, 6. 상태 구조 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| UI | 7. 화면 구조, 8. highlight 규칙 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 아트 | 8. accent/token 최소 패키지 | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| QA | 9. acceptance / recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |

---

## 1. 이 문서가 다루는 범위

### 포함
- `library.book-select` primer 1종의 실제 컷오버 범위
- `seenBookSelectPrimer` 저장/재노출 규칙
- `renderGrimoireShelf()` 기준 삽입 위치와 highlight 범위
- 첫 커밋에서 필요한 최소 수동/자동 검수선
- 프로그래머/UI/아트/QA handoff 경계

### 제외
- battle / reward / shop / ending primer 구현
- `dismiss all primers`, 도움말 센터, 재오픈 UI
- 책 티어 추천 시스템, 별점, 추천 책 순위 뱃지
- 새 library phase 또는 modal 튜토리얼
- overrunEvent 본편 실구현

즉 이 문서는 `온보딩 전체`가 아니라 **B-1 library primer 1장**만 닫는다.

---

## 2. 현재 runtime anchor

현재 코드 기준 B-1이 닿는 실제 anchor는 아래 셋이다.

| 파일 | 현재 anchor | 이번 단계 역할 |
| --- | --- | --- |
| `dice-spell-standalone/src/app.js` | `readMetaProgress()` / `persistMetaProgress()` | `uiGuidance` 저장 경계 추가 또는 동등 정규화 |
| `dice-spell-standalone/src/app.js` | `renderGrimoireShelf()` | hero-primer slot 삽입 + highlight class 소비 |
| `dice-spell-standalone/src/app.js` | `data-action="choose-grimoire"` 흐름 | primer 표시 여부와 무관하게 책 선택 진행 가능 유지 |

현재 `renderGrimoireShelf()`는 이미 아래 구조를 갖고 있다.

1. `.section-head`
2. `.body-copy`
3. `renderLegacyLoadoutPanel()`
4. `.grimoire-grid .grimoire-card`
5. 카드 내부 `.grimoire-identity-item` 목록
   - `운영 축`
   - `초반 운영`
   - `주의점`
   - `고유 규칙`
   - `완주 보너스`

즉 B-1의 진짜 목적은 새 화면을 만드는 것이 아니라,
**이미 있는 library surface 안에서 `초반 운영 -> 주의점` 읽기 순서를 먼저 보이게 재배치하는 것**이다.

---

## 3. 왜 B-1이 별도 packet이어야 하는가

현재 온보딩 문서는 `library → battle → reward/shop → ending` 전체 순서를 이미 고정했다.
그런데 실제 구현 착수 관점에서 library는 battle보다 작아 보이지만, 오히려 아래 이유 때문에 별도 packet이 필요하다.

1. `meta.uiGuidance`의 첫 저장 지점을 가장 먼저 열게 된다.
2. 첫 primer인데도 책 선택 인터랙션을 막지 않아야 한다.
3. 카드 내부 highlight는 새 데이터 필드가 아니라 기존 라벨/순서를 재사용해야 한다.
4. `추천 책`처럼 오해될 수 있는 시각 신호를 강하게 제한해야 한다.

즉 B-1은 단순한 카피 배치가 아니라,
**온보딩 저장 구조 + 첫 surface 밀도 규칙 + highlight 절제 규칙을 같이 시험하는 첫 commit**이다.

---

## 4. canonical source 묶음

| 질문 | source-of-truth |
| --- | --- |
| library primer의 존재 이유와 범위 | `docs/dicespell_first_run_onboarding_handoff_kr.md` |
| 실제 삽입 위치와 화면 밀도 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 상태 저장 위치와 helper 순서 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| exact 카피/배지/accent/token | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| 완료선과 recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |
| owner별 DoD | `docs/dicespell_first_run_onboarding_production_cutsheet_kr.md` |

이 B-1 packet은 위 문서를 대체하지 않는다.
대신 **B-1 첫 커밋에 필요한 부분만 추려 commit boundary로 다시 묶는다.**

---

## 5. B-1의 한 줄 정의

> 첫 책 선택 화면에서 `초반 운영`과 `주의점`을 먼저 읽게 만드는 compact hero-primer 1장을 추가하고, 이 primer는 첫 맥락에서만 보이며 책 선택 진행을 절대 막지 않는다.

이 정의에서 벗어나면 B-1이 아니라 다른 작업이다.

---

## 6. 상태 구조와 helper 계약

## 6.1 최소 저장 필드

B-1에서 필요한 장기 저장 필드는 아래 하나면 충분하다.

```json
{
  "uiGuidance": {
    "seenBookSelectPrimer": true
  }
}
```

### 규칙
- 기본 위치는 `appState.meta` 하위다.
- 저장 누락 시 해석은 `primer가 한 번 더 뜰 수 있음`까지다.
- 세이브가 망가졌다고 취급하거나 런을 막아선 안 된다.

## 6.2 B-1 전용 helper 최소 shape

```js
getLibraryPrimerState(appState) => {
  visible: boolean,
  contentKey: "library.book-select",
  eyebrow: "첫 런 가이드",
  title: "첫 책은 이렇게 읽으면 됩니다",
  lines: [
    "먼저 ‘초반 운영’을 보고, 바로 아래 ‘주의점’까지만 읽어도 시작하기 충분합니다.",
    "고유 규칙과 완주 보너스는 책 감이 온 뒤 읽어도 늦지 않습니다."
  ],
  confirmLabel: "알겠음",
  accentKey: "library-gold",
  artTokenSet: "primer-library",
  highlightKeys: ["openingPlan", "caution"]
}
```

### 규칙
- 첫 구현에서는 공용 `getPrimerState()` 안의 library branch여도 좋고, library 전용 helper여도 좋다.
- render는 이 payload를 소비만 하고, 문자열을 재조합하지 않는다.
- B-1에서는 `dismissedThisRun`, `variant`, `priority` 같은 필드를 늘리지 않는다.

---

## 7. 화면 구조 계약

## 7.1 삽입 위치

권장 DOM 순서는 아래다.

```text
[section-head]
[hero-primer]
[body-copy]
[legacy loadout panel]
[grimoire grid]
```

### 허용 대안
- body-copy를 약간 얇게 줄이고 primer를 그 위에 삽입
- primer를 `.section-head` 바로 아래 두고 기존 body-copy는 유지

### 금지
- primer를 modal로 띄우기
- primer가 `renderLegacyLoadoutPanel()` 아래로 내려가 첫 스크린 밖으로 밀리기
- primer 때문에 grimoire 카드 첫 줄이 과도하게 내려가기

## 7.2 카드 내부 highlight 범위

B-1에서 강조 가능한 영역은 정확히 두 곳이다.

1. `초반 운영`
2. `주의점`

### 허용 방식
- 얇은 outline
- subtle accent strip
- 라벨 아이콘 또는 marker

### 금지 방식
- `완주 보너스`까지 같은 세기로 강조
- 책 전체에 `추천` 리본 부착
- 특정 책이 정답처럼 보이는 강한 왕관/메달/서열 뱃지

---

## 8. role-specific handoff

## 8.1 프로그래머

### deliverable
- `meta.uiGuidance.seenBookSelectPrimer` 정규화
- `renderGrimoireShelf()` primer slot 추가
- primer visible 조건 연결
- 카드 내부 `openingPlan` / `caution` highlight class 소비
- primer와 무관하게 `choose-grimoire` 클릭 가능 유지

### 일부러 하지 않을 것
- B-1에서 battle/reward/shop/ending primer hook 열기
- primer 닫기용 별도 persistence schema 확장
- grimoire 데이터 schema 자체를 새로 늘리기

### 완료선
- 신규/기존 메타 상태 모두 library 진입이 깨지지 않는다.
- 첫 진입에서는 primer가 뜨고, 이후에는 재노출되지 않거나 동등 정책으로 제어된다.
- primer가 떠 있어도 책 선택 버튼은 그대로 동작한다.

## 8.2 UI

### deliverable
- compact hero-primer 1종
- library-gold accent 1종
- highlight 2종(`openingPlan`, `caution`) 시각 구분

### 일부러 하지 않을 것
- 새 페이지 shell 제작
- floating tooltip 군집
- 카드 전체 재레이아웃

### 완료선
- 1280px 기준 첫 스크린에서 section-head, primer, body-copy, 카드 첫 줄이 함께 읽힌다.
- primer가 grimoire grid보다 시각적으로 강하지만 library hero-card처럼 과도하게 크지 않다.

## 8.3 아트

### deliverable
- `primer-library` 토큰 세트 1종
- `첫 런 가이드` ribbon/badge 1종
- subtle outline 또는 accent chip 가이드

### 일부러 하지 않을 것
- 책별 개별 배너 제작
- 추천/난이도 메달 체계 도입

### 완료선
- library tone과 어긋나지 않는다.
- primer가 `안내`로 읽히고 `순위`로 읽히지 않는다.

## 8.4 QA

### deliverable
- 첫 진입 노출 확인
- 재진입 비재노출 또는 동등 정책 확인
- primer 표시 상태에서도 책 선택 가능 확인
- `openingPlan` / `caution`만 강조되고 나머지 항목은 과강조되지 않는지 확인
- 메타 저장 누락/초기화 시 recovery 확인

### 완료선
- B-1이 library surface만 건드렸다는 점이 검수에서 명확하다.
- primer 때문에 런 시작이 막히지 않는다.

---

## 9. acceptance / recovery

## 9.1 acceptance 5종

| ID | 확인 항목 | 통과 기준 |
| --- | --- | --- |
| B1-A1 | 첫 library 진입 | hero-primer 1장이 `.section-head` 아래에 보인다 |
| B1-A2 | 카피 정확성 | `library.book-select` content가 `title + 2 lines + 알겠음` 구조를 유지한다 |
| B1-A3 | 강조 범위 | `초반 운영`, `주의점`만 강조되고 `고유 규칙`, `완주 보너스`는 과강조되지 않는다 |
| B1-A4 | 진행 가능성 | primer가 보여도 `이 책으로 시작` 클릭이 정상 작동한다 |
| B1-A5 | 재노출 제어 | 같은 프로필/동등 조건에서 primer가 반복 스팸되지 않는다 |

## 9.2 recovery 3종

| ID | 상황 | 기대 결과 |
| --- | --- | --- |
| B1-R1 | `uiGuidance` 누락 | primer가 다시 떠도 library와 런 시작은 정상 동작 |
| B1-R2 | 메타 JSON 일부 손상 | normalize 후 primer 정책만 초기화되고 다른 library 기능은 유지 |
| B1-R3 | highlight class 누락 | primer는 표시되되 카드 목록과 선택 버튼은 정상 유지 |

---

## 10. 커밋 경계

### 권장 포함 파일
- `dice-spell-standalone/src/app.js`
- 필요 시 primer 관련 스타일 파일 1곳
- 필요 시 library primer 검수용 최소 smoke/수동 체크 메모
- 문서 갱신 파일

### 같은 커밋에 넣지 말 것
- battle primer hook
- reward/shop badge primer
- ending primer
- overrunEvent runtime 변경
- 메타 대규모 리팩터링

### 이 커밋의 정의
> `meta.uiGuidance`의 첫 저장 지점과 library primer 1장만 닫는 커밋

---

## 11. 다음 단계와의 경계

B-1이 닫힌 뒤에야 다음 선택지가 생긴다.

1. 여전히 overrun A-track 실구현이 최우선이면, 온보딩은 문서 ready 상태로만 유지
2. 온보딩 B-track이 실제로 열리면, 다음 commit-level 문서는 **B-2 battle primer packet**이 된다

즉 B-1 문서는 `온보딩 전체 시작` 문서가 아니라,
**온보딩이 열릴 경우 첫 커밋을 다시 부풀리지 않게 만드는 anchor**다.

---

## 12. 리스크 & 후속 메모

- 현재 가장 큰 리스크는 B-1 자체보다도, `library는 쉬워 보이니 battle hint까지 같이 넣자` 식으로 범위가 다시 커지는 것이다.
- 두 번째 리스크는 primer가 `도움말`이 아니라 `추천 책`처럼 읽히는 drift다.
- 세 번째 리스크는 `uiGuidance`를 B-1에서 과설계해 이후 B-2~B-4보다 저장 구조 정리가 더 커지는 것이다.

### 이번 문서의 결론
- B-1은 `library.book-select` primer 1종 + `seenBookSelectPrimer` 1필드 + highlight 2곳으로 닫는다.
- 그것보다 커지면 B-1이 아니라 다른 작업이다.
