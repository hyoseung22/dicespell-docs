# DiceSpell `overrunEvent` 구현 패킷

## 빠른 안내

### 3줄 요약
- 이 문서는 `overrunEvent` handoff를 실제 코드 컷오버 기준으로 다시 좁힌 **구현 계약서**다.
- 프로그래머는 상태 스키마와 함수 접점을 먼저, UI는 render payload와 재사용 한계를 먼저 보면 된다.
- 읽을 때 핵심은 `무엇을 구현할까`보다 **어디를 어떻게 분리하면 안전한가**다.

**한 줄 목표:** `overrunEvent`를 실제 파일·함수·상태 계약 수준으로 구현 가능한 범위로 압축한다.

### 왜 이 문서가 필요한가
- handoff만으로는 phase 의미는 충분하지만, 구현 직전에는 여전히 함수 경계와 save/UI 계약이 병목이 된다.
- 이 문서는 그 병목을 줄여 commit 단위를 작게 유지하기 위한 문서다.

### 누가 어디부터 읽으면 되나
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | patch surface, snapshot, fallback | `integration workorder`, `smoke migration` |
| UI | render payload, display-only 규칙 | `screen packet` |
| QA | smoke 기준과 recovery 포인트 | `acceptance matrix` |
| PM | 구현 범위와 제외 범위 | `summary` |

---

## 문서 목적

이 문서는 이미 작성된 `docs/dicespell_overrun_event_handoff_kr.md`를 **실제 구현 착수용 계약서**로 한 단계 더 좁힌 보조 문서다.

기존 handoff 문서는 방향과 범위를 잘 고정했지만, 지금 프로그래머/UI/아트가 바로 손을 대려면 아직 몇 가지가 더 필요하다.

- 현재 코드베이스의 **정확한 접점 함수**가 어디인지
- `ending.overrunDraft`에서 `overrunEvent` snapshot으로 무엇을 옮기고 무엇을 남길지
- save/load 중간 상태를 어떤 규칙으로 정규화할지
- 현재 `renderEnding()` / `continueOverrun()` 구조를 어디까지 뜯고 어디까지 재사용할지
- UI가 기존 카드/버튼 자산을 어떤 클래스 단위로 재사용할지
- 아트가 지금 단계에서 꼭 필요한 소형 패키지가 무엇인지

즉 이 문서는 `무엇을 만들지`보다 **지금 코드와 자산 위에서 어떻게 안전하게 붙일지**를 고정한다.

---

## 1. 왜 이 문서가 추가로 필요한가

현재 최고가치 blocker는 이미 명확하다.

> `오버런 전리품 보관함`에서 고른 전리품을 `실제 장면`으로 넘길 phase 구현이 아직 없다.

그런데 handoff 문서만으로 바로 들어가면 아래 세 가지가 다시 흔들릴 수 있다.

1. **엔딩 렌더 확장으로 끝낼지, 새 phase로 분리할지** 구현 중 다시 논쟁이 생길 수 있다.
2. **보상 적용 시점 이동** 때문에 save/load와 중복 적용 방지 규칙이 구현자마다 달라질 수 있다.
3. **현재 코드 접점이 추상적으로만 적혀 있어** 실제 작업이 `대규모 리팩터링`처럼 느껴질 수 있다.

이번 문서는 그 흔들림을 막기 위해, 현재 코드 기준의 **patch surface**, **상태 전이 계약**, **역할별 deliverable**을 더 세부적으로 못 박는다.

---

## 2. 현재 코드 기준 patch surface

현재 구현 기준 핵심 접점은 아래다.

### 2.1 game-engine

파일: `dice-spell-standalone/src/game-engine.js`

현재 이미 있는 관련 함수/구간:

- `chooseEndingOverrunReward(state, rewardId)`
  - 엔딩 화면의 보관함 선택만 기록한다.
- `continueOverrun(state)`
  - 현재는 여기서 선택 전리품 적용 + 오버런 세그먼트 증가 + 다음 페이지 진입을 한 번에 처리한다.
- 엔딩 정규화 구간
  - `state.ending.overrunDraft.options`
  - `state.ending.overrunDraft.selectedRewardId`
- 세그먼트 전리품 누적 구간
  - `segmentTrophyTemplateIds`
- 다음 페이지 진입 훅
  - `maybeEnterNextPage(state)`

### 2.2 app

파일: `dice-spell-standalone/src/app.js`

현재 관련 접점:

- `renderEnding(summary)`
  - `오버런 전리품 보관함` 카드 그리드 렌더
  - `continue-overrun` 버튼 렌더
- `renderCenter(summary)`
  - phase switch 진입점
- 액션 핸들러
  - `data-action="continue-overrun"`
  - `data-action="choose-ending-overrun-reward"`

### 2.3 styles

파일: `dice-spell-standalone/src/styles.css`

현재 재사용 가능 자산:

- `.hero-card`
- `.ending-card`
- `.meta-retirement-card`
- `.reward-card`
- `.section-head`
- `.primary-button`, `.ghost-button`

결론적으로 이번 작업은 **새 시스템 추가**가 아니라 아래 축을 나누는 작업이다.

- 엔딩 화면은 `선택`까지 담당
- `overrunEvent`는 `들고 넘어가는 장면`과 `최종 확인 CTA`를 담당
- 실제 보상 적용은 `continueOverrun()`에서 분리된 새 resolve 경로가 담당

---

## 3. bounded 구현 원칙

이번 구현에서 절대 지켜야 할 boundedness는 아래와 같다.

### 반드시 할 것

1. 새 `phase = 'overrunEvent'` 추가
2. `continueOverrun()`를 `enterOverrunEvent()` + `resolveOverrunEvent()` 성격으로 분리
3. 보상 적용 시점을 `오버런 계속 클릭`이 아니라 `overrunEvent CTA 클릭`으로 이동
4. `selectedRewardId = null`이어도 fallback 카드 1장으로 동일 레이아웃 유지
5. smoke와 save normalization을 같은 슬롯에서 고정

### 하지 말 것

1. 보관함 후보 재롤
2. 추가 전리품 강화/합성
3. 새 분기 이벤트 텍스트 트리
4. 맵 노드/경로 선택 시스템
5. 새 풀아트 전용 파이프라인

즉 이번 작업의 끝은 `작은 이벤트 시스템의 첫 버전`이 아니라 **기존 오버런 선택을 페이지 경험으로 승격한 단일 phase**다.

---

## 4. 상태 전이 계약

### 4.1 현재 흐름

```text
ending
  └─(오버런 계속)
      └─ continueOverrun()
          └─ 보상 즉시 적용 + 세그먼트 추가 + 다음 페이지 진입
```

### 4.2 목표 흐름

```text
ending
  └─(오버런 계속)
      └─ enterOverrunEvent()
          └─ phase = overrunEvent
              └─ renderOverrunEvent()
                  └─(CTA)
                      └─ resolveOverrunEvent()
                          └─ 보상 적용 + 세그먼트 추가 + 다음 페이지 진입
```

### 4.3 상태 전이 표

| 현재 phase | 입력 | 전이 후 phase | 핵심 부수효과 |
| --- | --- | --- | --- |
| `ending` | `choose-ending-overrun-reward` | `ending` 유지 | `selectedRewardId`만 기록 |
| `ending` | `continue-overrun` | `overrunEvent` | 선택 snapshot 생성, 아직 보상 미적용 |
| `overrunEvent` | confirm CTA | `library` 경유 후 `battle/shop` | 보상 1회 적용, 세그먼트 추가, 다음 페이지 진입 |
| `overrunEvent` | 세이브 복구 | `overrunEvent` 또는 fallback | invalid reward면 `none`으로 강등 |

핵심은 `continue-overrun`이 더 이상 resolve가 아니라 **enter**라는 점이다.

---

## 5. 런타임 데이터 계약

### 5.1 최종 권장 스키마

```json
{
  "phase": "overrunEvent",
  "overrunEvent": {
    "sourceType": "themeTrophy | enemyTrophy | none",
    "rewardId": "string | null",
    "rewardTitle": "string | null",
    "rewardDescription": "string | null",
    "rewardTag": "string | null",
    "theme": "slime | goblin | lich | null",
    "fromTemplateId": "number | null",
    "confirmLabel": "string",
    "flavorText": "string",
    "resolved": false
  }
}
```

### 5.2 기존 데이터에서 가져오는 값

원본 소스는 우선 아래 필드를 사용한다.

- `state.ending.overrunDraft.selectedRewardId`
- `state.ending.overrunDraft.options`
- `state.grimoireId`
- 필요 시 `segmentTrophyTemplateIds`는 직접 참조하지 않고 이미 만들어진 `overrunDraft.options`를 신뢰

즉 이번 phase는 **reward 재생성 단계가 아니라 snapshot 단계**다.

### 5.3 snapshot 규칙

#### 선택 있음

- `selectedRewardId`로 `ending.overrunDraft.options`에서 option 조회
- `kind === 'bundle'` 포함 기존 option payload는 그대로 유지 가능
- 렌더용으로 `title`, `description`, `rewardTag`를 snapshot에 복사
- 적 전리품이면 `fromTemplateId` 유지

#### 선택 없음

- `sourceType = 'none'`
- `rewardId = null`
- `rewardTitle = '챙긴 전리품 없음'`
- `rewardDescription = '이번에는 아무 잔해도 들지 않고 다음 권으로 넘어간다.'`
- `rewardTag = '빈손'`

### 5.4 sourceType 판정 규칙

권장 우선순위:

1. option에 적 전리품 라벨/메타가 있으면 `enemyTrophy`
2. option이 책 테마 전리품이면 `themeTrophy`
3. 선택 자체가 없으면 `none`

가능하면 option 생성 시점의 기존 metadata를 그대로 재사용하고, 새로 추론하는 분기를 최소화한다.

---

## 6. save/load 정규화 계약

### 6.1 최우선 원칙

- save는 항상 **복구 가능성이 우선**이다.
- `overrunEvent`가 조금 비어 있어도 런이 죽으면 안 된다.
- 최악의 경우 `none` fallback으로 강등해도 진행은 살려야 한다.

### 6.2 normalize 규칙

#### 케이스 A. `phase === 'overrunEvent'`인데 `overrunEvent`가 없음

대응:

- `ending`과 `ending.overrunDraft`가 남아 있으면 snapshot 재생성
- 둘 다 불완전하면 `sourceType = none` fallback 생성

#### 케이스 B. `rewardId`가 있는데 option lookup 실패

대응:

- `sourceType = none`
- `rewardId = null`
- `resolved = false`
- 로그에 복구 메시지 1줄 남김 권장

#### 케이스 C. `resolved === true` 상태로 `phase === 'overrunEvent'`

대응:

- resolve를 재실행하지 않는다.
- 이미 다음 페이지 쪽 상태가 준비되어 있으면 그 상태로 바로 복귀
- 불완전하면 `maybeEnterNextPage()` 이전 단계 재실행 대신 안전한 페이지 전개 함수로 강등 복구

#### 케이스 D. 구세이브는 `phase === 'ending'`이고 `continueOverrun()` 즉시 적용 흐름만 알고 있음

대응:

- 기존 save는 그대로 유효
- 새 버전부터 `continue-overrun` 액션만 `overrunEvent` 진입으로 바꿈
- 데이터 마이그레이션은 강제 버전업보다 **정규화 함수 흡수**가 안전

---

## 7. 엔진 함수 분리 제안

### 7.1 권장 함수 세트

#### `createOverrunEventSnapshot(state)`

역할:

- `ending.overrunDraft`를 읽어 `state.overrunEvent` payload 생성
- 선택 있음/없음 fallback 모두 처리

#### `enterOverrunEvent(state)`

역할:

- `ending` phase 검증
- snapshot 생성
- `state.phase = 'overrunEvent'`
- 아직 보상 적용은 하지 않음

#### `resolveOverrunEvent(state)`

역할:

- 선택 전리품 1회 적용
- `overrunLevel += 1`
- `segmentTrophyTemplateIds = []`
- pagePlan 확장
- `currentPage += 1`
- `ending = null`
- `overrunEvent.resolved = true`
- 다음 페이지 진입

#### `normalizeOverrunEventState(state)`

역할:

- save/load 복구
- invalid reward fallback 강등
- 중복 resolve 방지

### 7.2 기존 `continueOverrun(state)` 처리 방안

가장 안전한 방법은 이름을 유지하되 의미를 바꾸는 것이다.

#### 옵션 A. `continueOverrun()`를 enter 함수로 전환

장점:

- app.js 액션 이름을 덜 바꿔도 됨
- 외부 호출 지점이 적음

권장 방식:

- `continueOverrun(state)`는 이제 `enterOverrunEvent()` 래퍼가 된다.
- 실제 resolve는 `confirmOverrunEvent(state)` 또는 `resolveOverrunEvent(state)`가 맡는다.

#### 옵션 B. 이름도 분리

- `continueOverrun()` -> `enterOverrunEvent()`
- `confirmOverrunEvent()` 새 추가

이 방식이 더 명확하지만, 현재 UI 액션과 코드 검색 흐름을 생각하면 **옵션 A가 더 bounded**하다.

이번 작업에서는 옵션 A를 우선 권장한다.

---

## 8. app/UI 구현 계약

### 8.1 `renderCenter()` phase 분기 추가

현재:

- `battle`
- `shop`
- `reward`
- `ending`
- default

목표:

- `overrunEvent` 케이스 추가

```text
case 'overrunEvent':
  return renderOverrunEvent(summary)
```

### 8.2 `renderEnding()`에서 남길 것과 뺄 것

남길 것:

- 런 정산
- 완주 보너스 표시
- `오버런 전리품 보관함` 선택 UI
- `오버런 계속` 버튼

바꿀 것:

- 현재 안내 문구의 `다음 권 진입 직전에 즉시 적용` 표현은, 구현 후에는 `다음 권으로 넘기기 직전에 최종 확인` 쪽으로 수정하는 편이 문맥상 맞다.

### 8.3 새 `renderOverrunEvent()` 레이아웃 계약

권장 구조:

```html
<section class="hero-card overrun-event-card">
  <div>
    <p class="eyebrow">Overrun Transition</p>
    <h1>다음 권의 첫 장을 펼친다</h1>
    <p class="body-copy">flavorText</p>
    <section class="meta-retirement-card overrun-event-focus-card">
      <div class="section-head compact">
        <h3>rewardTitle</h3>
        <span>source badge</span>
      </div>
      <article class="reward-card overrun-event-reward-card">...</article>
    </section>
    <div class="hero-actions">
      <button class="primary-button" data-action="confirm-overrun-event">...</button>
    </div>
  </div>
</section>
```

### 8.4 기존 CSS 재사용 방침

우선 재사용:

- `.hero-card`
- `.ending-card`
- `.meta-retirement-card`
- `.reward-card`
- `.section-head.compact`
- `.hero-actions`

새로 추가할 최소 클래스:

- `.overrun-event-card`
- `.overrun-event-focus-card`
- `.overrun-event-reward-card`
- `.overrun-source-badge`
- `.overrun-source-badge.theme`
- `.overrun-source-badge.enemy`
- `.overrun-source-badge.none`

즉 CSS는 새 레이아웃 엔진을 만드는 게 아니라 **기존 ending card 계열의 중앙 집중형 변주**만 추가한다.

---

## 9. UI 카피/상태 매트릭스

### 9.1 헤더 문구

| sourceType | 헤더 | 보조 문구 |
| --- | --- | --- |
| `themeTrophy` | 다음 권의 첫 장을 펼친다 | 이번 책의 잔향을 챙겨 다음 장의 준비물로 삼는다. |
| `enemyTrophy` | 방금 넘긴 위협의 잔해를 챙긴다 | 쓰러뜨린 적의 결을 다음 권 첫 장에 들고 간다. |
| `none` | 가볍게 다음 권을 연다 | 이번에는 아무 잔해도 들지 않은 채 다음 장을 펼친다. |

### 9.2 CTA 문구

| sourceType | CTA |
| --- | --- |
| `themeTrophy` | 이 전리품을 챙기고 다음 권으로 |
| `enemyTrophy` | 이 잔해를 들고 다음 권으로 |
| `none` | 가볍게 다음 권을 연다 |

### 9.3 배지 문구

| sourceType | 배지 |
| --- | --- |
| `themeTrophy` | 책 전리품 |
| `enemyTrophy` | 적 전리품 |
| `none` | 빈손 |

핵심은 세 문구가 서로 같은 말을 반복하지 않고, 각각

- 헤더 = 문맥
- 카드 = 대상
- CTA = 행동

을 나눠 맡는 것이다.

---

## 10. 아트 패키지 계약

이번 단계에서 아트 오너에게 필요한 deliverable은 아래가 전부다.

### 필수

1. `overrunEvent` 헤더 패널 1종
2. 출처 배지 3종
   - 책 전리품
   - 적 전리품
   - 빈손
3. 중앙 카드 강조용 glow/backplate 1세트
4. 테마 accent 규칙 3종
   - slime 계열
   - goblin 계열
   - lich 계열

### 선택

- 책상/제본대/펼친 책 느낌의 배경 결 1종
- CTA 주변 미세 광원 변주

### 이번 단계에서 요구하지 않는 것

- 전리품 개별 일러스트 신규 제작
- 전용 시네마틱
- 대형 파티클 애니메이션

즉 이번 아트 작업은 **장면 암시용 UI 패키지**면 충분하다.

---

## 11. smoke 테스트 계약

최소 smoke는 아래 9개를 권장한다.

1. `ending.victory=true`, `canOverrun=true`, `selectedRewardId` 있음 -> `continueOverrun()` 호출 시 `phase='overrunEvent'`
2. 선택한 option이 책 전리품 -> `sourceType='themeTrophy'`
3. 선택한 option이 적 전리품 -> `sourceType='enemyTrophy'`
4. 선택 없음 -> `sourceType='none'`, fallback payload 생성
5. `overrunEvent` CTA 클릭 -> bundle 효과 1회만 적용
6. resolve 후 `segmentTrophyTemplateIds` 초기화
7. resolve 후 pagePlan이 세그먼트 1개 늘어남
8. resolve 후 `overrunEvent` 재진입 없이 다음 페이지 phase로 정상 이동
9. invalid `rewardId`로 load -> `none` fallback 강등

### 추가 권장 회귀

10. 패배 엔딩에서는 `continueOverrun()`가 실패하고 phase 유지
11. `overrunEvent` 도중 저장/로드 후 CTA를 눌러도 중복 적용 없음
12. 선택 없이 넘어가도 오버런 세그먼트 증설은 정상 동작

---

## 12. 역할별 handoff 체크리스트

### 프로그래머

- [ ] `game-engine.js`에 `overrunEvent` snapshot/create/resolve/normalize 경로 추가
- [ ] `continueOverrun()`를 enter 경로로 전환
- [ ] 보상 적용을 resolve 시점으로 이동
- [ ] `app.js`에 `renderOverrunEvent()` 및 confirm action 추가
- [ ] save/load normalization 고정
- [ ] smoke 9개 이상 통과

### UI 오너

- [ ] 기존 ending card와 충돌하지 않는 중앙 집중형 레이아웃 설계
- [ ] `문맥 -> 대상 -> 행동` 순서 유지
- [ ] skip fallback도 동일한 화면 밀도로 설계
- [ ] reward card 재사용 범위와 새 클래스 범위 최소화

### 아트 오너

- [ ] 헤더 패널 1종
- [ ] 출처 배지 3종
- [ ] 중앙 카드 강조 backplate 1세트
- [ ] theme accent 규칙 3종
- [ ] 풀아트/대형 애니메이션 요구 금지 확인

---

## 13. 리스크 & 방지책

### 리스크 1. `continueOverrun()` 의미 변경으로 기존 호출 가정이 깨질 위험

대응:

- 함수명은 유지하되 반환값 계약을 명확히 문서화
- `success: true`가 곧 `다음 페이지 진입 완료`를 뜻하지 않도록 관련 호출부 주석/문서 동기화

### 리스크 2. `ending`와 `overrunEvent`가 같은 데이터를 서로 들고 있어 중복 source of truth가 생길 위험

대응:

- `ending.overrunDraft`는 선택 기록용
- `overrunEvent`는 렌더/resolve snapshot용
- resolve 완료 후 `ending = null`, `overrunEvent = null` 정리 원칙 고정

### 리스크 3. skip fallback이 허전해 보여 `굳이 왜 이 phase가 있지?`가 될 위험

대응:

- 빈손도 플레이스홀더 카드 + 배지 + flavorText를 반드시 유지
- CTA만 있는 빈 화면은 금지

### 리스크 4. save/load 복구에서 invalid reward가 런을 죽일 위험

대응:

- reward lookup 실패 시 무조건 `none` fallback
- 런 진행을 살리는 것이 우선

---

## 14. 이번 문서의 한 줄 결론

`docs/dicespell_overrun_event_handoff_kr.md`가 방향을 고정했다면, 이 문서는 **현재 코드베이스 위에서 `overrunEvent`를 어디에 어떻게 꽂아 넣을지**를 고정한다.

즉 다음 구현 슬롯은 더 이상 `무엇을 만들까`를 고민할 단계가 아니라, 이 패킷대로 **엔진 진입점 분리 -> snapshot 생성 -> 중앙 카드 1장 UI -> CTA 시점 resolve -> save/smoke 고정** 순서로 닫으면 된다.
