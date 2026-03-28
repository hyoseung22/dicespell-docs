# DiceSpell 첫 런 온보딩 B-1 Library Primer 요약

## 3줄 요약
- B-1은 온보딩 전체가 아니라 **첫 책 선택 화면 primer 1장**을 실제 구현 가능한 commit 범위로 줄인 문서다.
- 핵심은 새 화면을 만드는 게 아니라 `renderGrimoireShelf()` 안에서 `초반 운영 -> 주의점` 읽기 순서를 먼저 보이게 만드는 것이다.
- 저장 구조는 `seenBookSelectPrimer` 1필드면 충분하고, primer가 보여도 책 선택은 절대 막히면 안 된다.

**한 줄 목표:** 첫 library 진입 10초 안에 플레이어가 `무엇부터 읽어야 하는가`를 배우게 만든다.

## 왜 이 문서가 필요한가
- 기존 온보딩 문서는 방향과 화면 위치를 충분히 닫았지만, 실제 구현 직전 `첫 커밋에서 어디까지가 B-1인가`는 별도로 좁혀 둘 필요가 있었다.
- 이 요약 문서는 프로그래머/UI/아트/QA가 B-1 범위를 3~5분 안에 맞춰 읽도록 만든 readable companion이다.

## 이 문서를 먼저 봐야 하는 사람
- **프로그래머:** `app.js`에서 `readMetaProgress()` / `persistMetaProgress()` / `renderGrimoireShelf()`만 우선 건드릴 계획일 때
- **UI/아트:** primer를 `새 phase`가 아니라 `compact hero-primer 1장`으로 제한해야 할 때
- **QA:** 첫 진입 노출, 재노출 제어, 책 선택 비차단만 빠르게 검수해야 할 때

---

## 이번 B-1에서 실제로 닫는 것

### 1) 화면
- `.section-head` 아래에 compact hero-primer 1장 추가
- 기존 `.body-copy`는 유지하되 primer보다 한 단계 뒤로 읽히게 정리
- grimoire 카드 내부에서는 `초반 운영`, `주의점` 2곳만 강조

### 2) 상태
- `meta.uiGuidance.seenBookSelectPrimer` 또는 동등 1필드
- 저장 누락 시 허용 범위는 `primer 재노출`까지
- 런 시작/책 선택은 어떤 경우에도 막지 않음

### 3) exact content
- `contentKey`: `library.book-select`
- title: `첫 책은 이렇게 읽으면 됩니다`
- line 1: `먼저 ‘초반 운영’을 보고, 바로 아래 ‘주의점’까지만 읽어도 시작하기 충분합니다.`
- line 2: `고유 규칙과 완주 보너스는 책 감이 온 뒤 읽어도 늦지 않습니다.`
- CTA: `알겠음`

---

## 역할별 handoff

### 프로그래머
- B-1에서는 library surface만 건드린다.
- battle/reward/shop/ending primer를 같이 열지 않는다.
- grimoire 데이터 schema를 새로 키우지 않는다.

### UI
- primer는 library hero-card보다 작고 빠르게 읽혀야 한다.
- `추천 책`처럼 보이는 강한 뱃지/왕관/서열 표현은 금지다.

### 아트
- `primer-library` 토큰 세트 1종이면 충분하다.
- 책별 리본, 등급 메달, 난이도 배너는 만들지 않는다.

### QA
- 첫 진입 노출
- 재진입 비재노출 또는 동등 정책
- primer 표시 상태에서도 `이 책으로 시작` 클릭 가능
- `초반 운영` / `주의점` 외 항목 과강조 없음

---

## 완료선

B-1은 아래가 모두 참이면 닫힌다.

1. 첫 library 진입에서 primer가 보인다.
2. `초반 운영 -> 주의점` 읽기 순서가 먼저 보인다.
3. primer가 있어도 책 선택이 막히지 않는다.
4. 같은 프로필에서 반복 스팸되지 않는다.
5. 이 커밋 안에 battle/reward/shop/ending 온보딩이 섞이지 않는다.

---

## 리스크
- 가장 큰 리스크는 `이왕 여는 김에 battle도 같이` 식으로 범위가 다시 커지는 것.
- 두 번째 리스크는 primer가 안내가 아니라 추천/랭킹처럼 읽히는 것.
- 세 번째 리스크는 `uiGuidance`를 첫 커밋부터 과하게 확장하는 것.

## 바로 다음 행동
- overrun A-track 실구현이 아직 최우선이면 B-1은 handoff-ready 상태로만 유지한다.
- 온보딩 B-track이 실제로 열리면, 이 문서를 기준으로 library primer 1장만 먼저 붙인다.
- 그 다음 commit-level 문서는 `B-2 battle primer packet`으로 분리한다.
