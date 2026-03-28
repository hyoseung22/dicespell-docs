# DiceSpell 첫 런 온보딩 B-4 Ending Primer 컷오버 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 첫 런 온보딩의 네 번째 실제 구현 묶음인 **B-4 ending primer**를 commit-level handoff로 좁힌다.
- 프로그래머는 `renderEnding(summary)`와 `ending.defeat` / `ending.victory` / `ending.overrun-cta`, `seenDefeatPrimer` / `seenVictoryPrimer` / `seenOverrunCtaPrimer` 저장 경계만 먼저 보면 된다.
- UI/아트/QA는 `ending은 결과/정산/버튼 의미까지만 말한다`, `overrunEvent flavor는 절대 먹지 않는다`, `badge/cta 보조선만 쓴다` 세 가지만 기억하면 된다.

**한 줄 목표:** 첫 결과 화면에서 플레이어가 `이번 런에서 무엇이 남는지`, `지금 버튼이 무엇을 뜻하는지`를 짧게 이해하도록 만들되, `themeTrophy` / `enemyTrophy` / `none` flavor와 다음 권 장면성은 끝까지 `overrunEvent`에 남긴다.

### 왜 이 문서가 필요한가
- 온보딩 문서 묶음은 이미 방향, screen packet, workorder, acceptance, cutsheet, content deck까지 충분히 닫혀 있다.
- B-1 library, B-2 battle, B-3 reward/shop도 실제 첫 커밋 경계까지 내려왔지만, 마지막 `ending` surface는 shared boundary 때문에 오히려 가장 쉽게 부풀 수 있는 구간이다.
- 이 문서는 그 공백만 메운다. 즉 **B-4 ending primer를 실제 파일/카피 밀도/검수/owner handoff 단위로 좁히는 실행 패킷**이다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | 2. 현재 runtime anchor, 5. B-4 범위, 6. 상태 구조 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| UI | 7. 화면 구조, 8. ending-vs-overrun 경계 규칙 | `docs/dicespell_ending_overrun_boundary_packet_kr.md` |
| 아트 | 8. accent/token 최소 패키지 | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| QA | 9. acceptance / recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |

---

## 1. 이 문서가 다루는 범위

### 포함
- `ending.defeat` badge-primer의 실제 컷오버 범위
- `ending.victory` badge-primer의 실제 컷오버 범위
- `ending.overrun-cta` CTA 보조선의 실제 컷오버 범위
- `seenDefeatPrimer`, `seenVictoryPrimer`, `seenOverrunCtaPrimer` 저장/재노출 규칙
- `renderEnding(summary)` 기준 삽입 위치와 밀도 제한
- `ending`이 `결과/정산/버튼 의미`까지만 말하고 `overrunEvent` flavor를 먹지 않게 만드는 최소 경계 규칙
- 패배/승리/오버런 가능 ending 공존, CTA 비차단, primer 누락 fallback을 포함한 최소 검수선
- 프로그래머/UI/아트/QA handoff 경계

### 제외
- `overrunEvent` 본편 실구현
- ending 화면 전체 리디자인
- `오버런 전리품 보관함` 카드 로직 변경
- reward/shop/library/battle primer 구현
- 서고 기록/완주 보너스/오버런 적용 규칙 변경

즉 이 문서는 `ending UX 전체 개편`이 아니라 **B-4 ending 3면 primer**만 닫는다.

---

## 2. 현재 runtime anchor

현재 코드 기준 B-4가 닿는 실제 anchor는 아래 셋이다.

| 파일 | 현재 anchor | 이번 단계 역할 |
| --- | --- | --- |
| `dice-spell-standalone/src/app.js` | `renderEnding(summary)` | defeat/victory/overrun CTA primer slot 소비 |
| `dice-spell-standalone/src/app.js` | ending 상단 결과 헤더 / 정산 문구 / `오버런 전리품 보관함` / CTA 영역 | primer가 정산과 CTA 의미를 보조하고, flavor는 먹지 않게 위계 고정 |
| `dice-spell-standalone/src/app.js` | `continue-overrun` 액션과 ending CTA 묶음 | `ending.overrun-cta`가 버튼 의미만 설명하고 실제 장면성은 넘기게 함 |

현재 `renderEnding(summary)`는 이미 아래 구조를 갖고 있다.

1. 결과 헤더 / 요약
2. 정산 정보 / 완주 보너스 문맥
3. 필요 시 `오버런 전리품 보관함`
4. CTA 버튼들 (`새 런`, `오버런 계속` 등)

즉 B-4의 진짜 목적은 새 ending 화면을 만드는 것이 아니라,
**이미 있는 ending surface 안에서 `패배해도 남는 것`, `완주 후 무엇을 고르는지`, `오버런 계속이 무슨 뜻인지`만 짧게 고정하는 것**이다.

---

## 3. 왜 B-4가 별도 packet이어야 하는가

현재 온보딩 문서는 `library -> battle -> reward/shop -> ending` 전체 순서를 이미 고정했다.
그런데 실제 구현 착수 관점에서 ending은 마지막이라서 더 위험하다.

1. `오버런 전리품 보관함`이 이미 ending 안에 있어, 구현자가 `overrunEvent` flavor까지 ending에서 같이 설명하고 싶어지기 쉽다.
2. 패배/승리/오버런 가능 ending이 모두 다른 문맥인데, `결과 화면 안내`라는 이유로 하나의 일반 문장으로 뭉개기 쉽다.
3. `ending.overrun-cta`가 CTA 주변 보조선이어야 하는데, 잘못 붙이면 hero-card가 되어 CTA보다 더 큰 정보 덩어리가 되기 쉽다.
4. B-4를 열면서 `overrunEvent` 문구나 정산 카드 개편까지 같이 끌고 들어와 범위가 부풀기 쉽다.

즉 B-4는 단순한 카피 배치가 아니라,
**ending이 어디까지 말하고 overrunEvent에 무엇을 남길지 마지막으로 고정하는 첫 결과 화면 commit**이다.

---

## 4. canonical source 묶음

| 질문 | source-of-truth |
| --- | --- |
| ending primer의 존재 이유와 범위 | `docs/dicespell_first_run_onboarding_handoff_kr.md` |
| 실제 삽입 위치와 화면 밀도 | `docs/dicespell_first_run_onboarding_screen_packet_kr.md` |
| 상태 저장 위치와 cutover 순서 | `docs/dicespell_first_run_onboarding_integration_workorder_kr.md` |
| exact 카피/배지/accent/token | `docs/dicespell_first_run_onboarding_content_deck_kr.md` |
| 완료선과 recovery | `docs/dicespell_first_run_onboarding_acceptance_matrix_kr.md` |
| owner별 DoD | `docs/dicespell_first_run_onboarding_production_cutsheet_kr.md` |
| ending과 overrunEvent 책임 경계 | `docs/dicespell_ending_overrun_boundary_packet_kr.md` |
| 전체 cutover 순서 | `docs/dicespell_overrun_onboarding_cutover_master_plan_kr.md` |

이 B-4 packet은 위 문서를 대체하지 않는다.
대신 **B-4 ending 첫 커밋에 필요한 부분만 추려 commit boundary로 다시 묶는다.**

---

## 5. B-4의 한 줄 정의

> 패배 ending에서는 `무엇이 남는가`, 승리 ending에서는 `완주 뒤 무엇을 고르는가`, 오버런 가능 ending의 CTA 주변에서는 `이 버튼이 다음 준비 장면으로 이어진다`는 의미만 얇은 badge/cta 보조선으로 고정한다.

이 정의에서 벗어나면 B-4가 아니라 다른 작업이다.

---

## 6. 상태 구조와 helper 계약

## 6.1 최소 저장 필드

B-4에서 필요한 장기 저장 필드는 아래 셋이면 충분하다.

```json
{
  "uiGuidance": {
    "seenDefeatPrimer": true,
    "seenVictoryPrimer": true,
    "seenOverrunCtaPrimer": true
  }
}
```

### 규칙
- 기본 위치는 `appState.meta` 하위다.
- 저장 누락 시 해석은 `해당 primer가 한 번 더 뜰 수 있음`까지다.
- 세이브가 망가졌다고 취급하거나 ending 진행을 막아선 안 된다.
- `seenVictoryPrimer`와 `seenOverrunCtaPrimer`는 분리한다. 승리 본문 안내와 CTA 의미 안내는 같은 보조선이 아니다.

## 6.2 B-4 전용 helper 최소 shape

```js
getEndingPrimerState(summary, appState) => ({
  defeat: {
    visible: boolean,
    contentKey: 'ending.defeat',
    badgeLabel: '첫 결과 정산',
    lines: [
      '런은 끝나도 이번에 남긴 페이지 기록과 서고 진척은 다음 선택에 남습니다.'
    ],
    accentKey: 'ending-ash',
    artTokenSet: 'primer-ending-defeat'
  },
  victory: {
    visible: boolean,
    contentKey: 'ending.victory',
    badgeLabel: '첫 완주',
    lines: [
      '책을 끝내면 완주 보너스를 챙긴 뒤, 이어서 다음 권으로 넘어갈지 고를 수 있습니다.'
    ],
    accentKey: 'ending-victory',
    artTokenSet: 'primer-ending-victory'
  },
  overrunCta: {
    visible: boolean,
    contentKey: 'ending.overrun-cta',
    badgeLabel: '오버런 계속',
    lines: [
      '이 버튼은 정산을 끝내고, 지금 챙긴 보너스를 들고 다음 권으로 이어가는 선택입니다.'
    ],
    accentKey: 'ending-overrun',
    artTokenSet: 'primer-ending-overrun'
  }
})
```

### 규칙
- 첫 구현에서는 공용 `getPrimerState()` 안의 ending branch여도 좋고, ending 전용 helper여도 좋다.
- render는 이 payload를 소비만 하고, flavor 문장을 재조합하지 않는다.
- B-4에서는 `overrunSourceType`, `eventMood`, `sceneFlavor`, `branchTitle` 같은 필드를 늘리지 않는다.
- `ending.overrun-cta`는 CTA 주변 보조 문구여야지 별도 hero-card가 아니다.

---

## 7. 화면 구조 계약

## 7.1 defeat primer 삽입 위치

권장 DOM 순서는 아래다.

```text
[result header / summary]
[defeat-primer]
[settlement block]
[primary CTA]
```

### 금지
- defeat primer가 패배 화면 전체를 덮는 hero-card가 되기
- 서고 기록/페이지 기록 수치보다 primer가 더 큰 시각 덩어리가 되기
- 패배 flavor나 재도전 서사를 장문화하기

## 7.2 victory primer 삽입 위치

권장 DOM 순서는 아래다.

```text
[result header / completion summary]
[victory-primer]
[completion bonus / overrun draft block]
[CTA group]
```

### 금지
- victory primer가 `오버런 전리품 보관함`과 같은 카드 밀도로 경쟁하기
- victory primer가 선택한 전리품 flavor를 미리 설명하기
- victory primer와 overrun CTA 보조선이 같은 문장을 반복하기

## 7.3 overrun CTA primer 삽입 위치

권장 DOM 순서는 아래다.

```text
[ending content]
[CTA group]
[overrun-cta helper line near continue button]
```

### 허용 대안
- CTA 그룹 위 1줄 보조선
- `오버런 계속` 버튼 바로 아래 1줄 보조선

### 금지
- CTA를 감싸는 큰 카드
- `오버런 전리품 보관함` 내부 보조 문구와 중복 노출
- `themeTrophy` / `enemyTrophy` / `none` flavor를 넣는 것

## 7.4 ending-vs-overrun 차등 규칙

B-4에서 ending이 반드시 `overrunEvent`와 다르게 읽혀야 하는 축은 아래 셋이다.

1. **책임 문장**
   - ending: 무엇이 남는가 / 무엇을 고르는가 / 버튼이 무슨 뜻인가
   - overrunEvent: 무엇을 들고 넘어가는가 / 어떤 장면인가
2. **정보 위계**
   - ending: 결과 헤더와 정산, CTA 보조선
   - overrunEvent: 중앙 카드 1장 + flavor + confirm
3. **시각 밀도**
   - ending: badge/cta 보조선
   - overrunEvent: focus card

### 일부러 하지 않을 것
- ending에 `이번엔 아무 전리품도 들지 않은 채...` 같은 flavor 쓰기
- `오버런 계속` 설명을 별도 hero-card로 키우기
- victory primer와 overrun CTA 보조선을 같은 위치/같은 톤으로 복붙하기

---

## 8. role-specific handoff

## 8.1 프로그래머

### deliverable
- `meta.uiGuidance.seenDefeatPrimer` / `seenVictoryPrimer` / `seenOverrunCtaPrimer` 정규화
- `renderEnding(summary)` defeat/victory primer slot 추가
- `오버런 계속` CTA 주변 `ending.overrun-cta` 보조선 추가
- `오버런 전리품 보관함`과 primer가 서로 말을 먹지 않는 guard
- primer와 무관하게 새 런 / 오버런 계속 / 정산 흐름 정상 유지

### 일부러 하지 않을 것
- B-4에서 `overrunEvent` snapshot/resolve 로직 열기
- ending body-copy 전체 리라이트
- `오버런 전리품 보관함` 로직/선택 규칙 변경

### 완료선
- 패배, 승리, 오버런 CTA 안내가 각각 1회만 뜬다.
- primer가 떠 있어도 CTA와 정산 흐름이 그대로 동작한다.
- ending 본문이 `overrunEvent` flavor를 미리 먹지 않는다.

## 8.2 UI

### deliverable
- defeat용 muted ash badge strip 1종
- victory용 compact gold badge strip 1종
- overrun CTA용 violet helper line 1종
- 1280px 기준 결과 헤더/정산/CTA가 첫 스크린 위계에서 깨지지 않는 배치

### 일부러 하지 않을 것
- victory와 overrun CTA 둘 다 같은 큰 카드로 처리하기
- CTA 주변 helper line이 버튼보다 더 눈에 띄게 만들기
- ending을 `overrunEvent` preview 카드처럼 보이게 만들기

### 완료선
- ending은 끝까지 결과 화면처럼 읽히고, overrunEvent와 다른 계층으로 보인다.
- 패배/승리/오버런 CTA가 같은 `결과 안내` 톤 안에서 분화된다.
- `오버런 전리품 보관함` 카드와 primer가 경쟁하지 않는다.

## 8.3 아트

### deliverable
- `primer-ending-defeat`용 muted ash accent/token 1세트
- `primer-ending-victory`용 gold/ivory accent/token 1세트
- `primer-ending-overrun`용 violet helper accent/token 1세트
- 신규 대형 일러스트 없이 배지/라인/아이콘 재사용 중심 최소 패키지

### 일부러 하지 않을 것
- ending primer용 신규 대형 배경 제작
- overrunEvent와 같은 focus backplate 재사용

### 완료선
- defeat/victory/overrun CTA가 서로 다른 정산 상태로 읽힌다.
- ending primer와 overrunEvent 카드가 같은 시스템처럼 오독되지 않는다.

## 8.4 QA

### 반드시 볼 것
- 신규 프로필 첫 패배 ending
- 신규 프로필 첫 승리 ending
- 승리 + `오버런 전리품 보관함` + `오버런 계속` CTA 동시 노출
- `uiGuidance` 전체 누락 / 일부 키 누락 저장
- primer 비노출 상태에서 기존 정산/CTA 회귀 없음

### 완료선
- defeat/victory/overrun CTA primer가 각각 1회만 뜨고, 서로 다른 의미를 전달한다.
- primer가 없어도 flow는 그대로 진행된다.
- `overrunEvent` flavor가 ending으로 새지 않는다.

---

## 9. acceptance / recovery 압축판

## 9.1 B4-A1 ~ B4-A7 acceptance

| ID | 기준 | 통과 조건 |
| --- | --- | --- |
| B4-A1 | 첫 패배 ending | `첫 결과 정산` badge + `무엇이 남는가` 문장 1회 노출 |
| B4-A2 | 첫 승리 ending | `첫 완주` badge + `완주 뒤 선택` 문장 1회 노출 |
| B4-A3 | 첫 오버런 CTA | `오버런 계속` 보조선이 버튼 의미만 1회 설명 |
| B4-A4 | CTA 비차단 | 새 런 / 오버런 계속 CTA 정상 동작 |
| B4-A5 | 보관함 공존 | victory primer와 `오버런 전리품 보관함`이 겹치지 않음 |
| B4-A6 | ending-vs-overrun 경계 | ending 본문/primer가 sourceType flavor를 먹지 않음 |
| B4-A7 | 결과별 분화 | defeat/victory/overrun CTA가 같은 문장 반복 없이 구분됨 |

## 9.2 recovery

| 상황 | 기대 동작 | 실패 시 fallback |
| --- | --- | --- |
| `uiGuidance` 전체 누락 | primer만 다시 뜰 수 있고 flow는 정상 진행 | `uiGuidance = {}` 수준 정규화 |
| `seenDefeatPrimer`만 누락 | defeat primer만 재노출 | 다른 ending primer 초기화 금지 |
| `seenVictoryPrimer`만 누락 | victory primer만 재노출 | overrun CTA primer 초기화 금지 |
| `seenOverrunCtaPrimer`만 누락 | overrun CTA 보조선만 재노출 | victory primer 중복 노출 금지 |
| primer payload 비어 있음 | ending 본체/CTA 렌더 정상 유지 | primer DOM 생략 |
| victory + overrun draft 동시 노출 | victory primer는 1회만 유지, CTA helper는 버튼 의미만 보조 | flavor/helper 중복 금지 |

---

## 10. 금지 패턴

아래가 보이면 B-4가 아니라 다른 작업이다.

1. ending primer를 한 커밋에서 `overrunEvent` flavor 카드처럼 확장
2. `오버런 계속` 설명을 CTA보다 큰 hero-card로 확대
3. victory primer와 overrun CTA helper가 같은 문장을 반복
4. `오버런 전리품 보관함` 카드 내부와 CTA helper에서 같은 뜻을 두 번 설명
5. ending primer 구현을 핑계로 정산/보상 적용 로직까지 건드림
6. `themeTrophy` / `enemyTrophy` / `none` flavor를 ending 카피에 끌고 옴

---

## 11. 왜 이 단계가 지금 가장 가치가 큰가

B-1, B-2, B-3는 각각 `무엇부터 읽을까`, `이번 턴은 어떻게 흘러가나`, `reward와 shop은 어떻게 다른가`를 고정했다.
마지막으로 가장 쉽게 새는 구간은 ending이다.

- 패배에서는 `아무것도 안 남는 게 아니다`
- 승리에서는 `완주 뒤 선택이 있다`
- 오버런 CTA에서는 `이 버튼은 다음 준비 장면으로 이어진다`

이 차이가 흐리면 플레이어는 ending을 `그냥 정산 화면`으로만 읽고,
구현자는 ending이 `overrunEvent`가 할 장면 설명까지 미리 먹게 만들기 쉽다.

즉 B-4의 목적은 새 기능 추가가 아니라,
**첫 결과 화면이 overrunEvent와 경쟁하지 않으면서도 초보자에게 필요한 최소 의미만 남기는 handoff anchor**를 남기는 것이다.

---

## 12. 다음 액션

B-4가 닫힌 뒤에야 온보딩 B-track은 surface 기준으로 모두 commit-level anchor를 갖는다.

1. 여전히 overrun A-track 실구현이 최우선이면, B-4는 handoff-ready 상태로만 유지
2. 온보딩 B-track이 실제로 열리면, 순서는 반드시 `B-1 library -> B-2 battle -> B-3 reward/shop -> B-4 ending`
3. B-4 구현 시에는 반드시 `docs/dicespell_ending_overrun_boundary_packet_kr.md`를 함께 읽고, ending은 끝까지 결과/정산/버튼 의미만 남긴다

즉 B-4 문서는 `ending polish 전체`를 여는 문서가 아니라,
**ending primer가 overrun flavor를 먹지 않게 만드는 bounded anchor**다.

---

## 13. 리스크 & 후속과제

- 가장 큰 리스크는 ending이 마지막 surface라는 이유로, 구현자가 `이왕 여기까지 왔으니 overrun flavor도 같이 설명하자`로 범위를 넓히는 것이다.
- 두 번째 리스크는 victory primer와 `오버런 계속` helper가 같은 의미를 반복해 CTA 주변 정보 밀도만 늘리는 것이다.
- 세 번째 리스크는 `오버런 전리품 보관함` 카드와 ending primer가 서로 다른 owner 문서를 따르지 않아 같은 시각 레벨에서 경쟁하는 것이다.
- 따라서 B-4는 `ending.defeat` 1면 + `ending.victory` 1면 + `ending.overrun-cta` 1면 + `seenDefeatPrimer` / `seenVictoryPrimer` / `seenOverrunCtaPrimer` 3필드 + CTA 비차단 + ending-vs-overrun 경계 검수선으로만 닫는다.
- 그것보다 커지면 B-4가 아니라 다른 작업이다.
