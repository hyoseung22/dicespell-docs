# DiceSpell `overrunEvent` / 첫 런 온보딩 채워진 매니페스트 예시집

## 3줄 요약
- 이 문서는 `manifest fill packet`이 정한 field-level contract를 **실제 문장 밀도**로 보여 주는 source-of-truth 예시집이다.
- 핵심은 새 규칙을 더 만드는 것이 아니라, Commit 1 / Commit 2 / B-1~B-4에서 `manifest.md`가 실제로 어떤 한국어/영문 혼합 stage 문장으로 채워져야 drift가 줄어드는지 못 박는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `이 정도면 archive-ready 문장으로 쓸 수 있는가`, `아직 half-cutover인데 green처럼 과장된 문장인가`, `tracker/run log/git에 어디까지 재사용해도 되는가`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
starter manifest 템플릿과 작성 규칙을 **복붙 가능한 stage exemplar**까지 내려, 다음 implementer가 첫 archive drop에서도 같은 단계 언어를 망설임 없이 재사용하게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell 문서 스택은 이미 충분히 두껍다.

- `archive bootstrap packet`은 stage bundle을 어떻게 시작할지 알려 준다.
- `artifact archive packet`은 어디에 보관할지 알려 준다.
- `manifest fill packet`은 각 필드에 무엇을 쓰고 쓰지 말아야 하는지 알려 준다.

그런데 실제 첫 실행 직전에는 마지막 작은 공백이 남아 있다.

1. 규칙은 알아도 **완성된 예시 한 장**이 없으면 owner마다 문장 밀도가 다시 흔들린다.
2. PM/QA는 규칙 문서를 읽고도 `이 문장을 tracker에 그대로 써도 되나?`에서 다시 멈출 수 있다.
3. 프로그래머/UI/아트는 `좋은 예시`를 이해해도 실제 첫 archive에서 `내 stage manifest 전체가 어떤 밀도로 보여야 하는가`를 재조합하게 된다.
4. starter template가 비어 있으면 `boundary / non-change`와 `blockers / notes`가 다시 섞일 위험이 남는다.

즉 지금 필요한 것은 새 packet이 아니라, **채워진 manifest를 stage별로 끝까지 보여 주는 exemplar deck**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 것 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. A-track 채워진 예시` | `commit1/commit2 surgery sheet`, `semantic anchor packet`, `runtime rehearsal packet` | Commit 1은 floor, Commit 2는 closing 문장을 실제 manifest 한 장에서 어떻게 분리하는지 본다 |
| UI | `3. B-track 채워진 예시`, `4. 복붙/재사용 규칙` | `visual asset packet`, `B-1~B-4 packet`, `ending boundary packet` | capture 파일명만이 아니라 owner summary와 handoff line도 surface boundary를 말해야 함을 본다 |
| 아트 | `3. B-track 채워진 예시`, `5. 금지 문장` | `content deck`, `visual asset packet` | tone note는 감상문이 아니라 token/boundary sanity 문장이어야 함을 본다 |
| QA | `2. A-track 예시`, `3. B-track 예시`, `6. 리스크 & 후속과제` | `fixture ledger`, `execution reporting packet`, `scorecard` | smoke/acceptance 로그가 어떤 stage 문장과 같이 움직여야 하는지 본다 |
| PM/기획 | `1. first screen 판정`, `4. 복붙/재사용 규칙`, `6. 리스크 & 후속과제` | `development tracker`, `automation run log`, `document stack packet` | manifest 한 줄이 tracker/run log/git 메시지 원본으로 재사용되는 수준까지 닫혀야 함을 본다 |

---

## 1. first screen 판정

### 이 문서를 왜 열어야 하나
- **프로그래머**: 첫 archive drop에서 템플릿을 채우다 다시 stage 문장을 발명하지 않기 위해 연다.
- **UI/아트**: surface capture만 올리고 summary/handoff를 비워 두는 반쪽 handoff를 막기 위해 연다.
- **QA/PM**: `archive-ready`와 `in-progress`의 문장 밀도를 실제 완성본 기준으로 판단하기 위해 연다.

### 가장 짧은 답
- 채워진 manifest는 `이 정도면 충분`을 보여 주는 샘플이 아니라, **이보다 느슨해지면 drift가 다시 생기는 최소선**을 보여 주는 exemplar다.
- Commit 1 / Commit 2 / B-1~B-4는 각각 다른 handoff line을 가져야 하며, 한 장의 generic green 문장으로 묶이면 실패다.
- `boundary / non-change`는 stage가 안 하는 일을 잠그고, `blockers / notes`는 정말 남은 문제만 짧게 남긴다.
- tracker/run log/git은 가능하면 이 exemplar의 `handoff line`과 `next allowed step`을 거의 그대로 재사용한다.

### 한 줄 판정 규칙
이 문서는 매니페스트 문장 스타일 가이드가 아니라, **첫 실전 archive를 닫는 기준 샘플**이다.

---

## 2. A-track 채워진 예시

## 3줄 요약
- A-track은 둘 다 overrunEvent runtime이지만 Commit 1과 Commit 2의 문장 목적이 다르다.
- Commit 1은 `floor / 비변경선 / recovery 선행`을 말하고, Commit 2는 `closing / resolve / old baseline 제거`를 말한다.
- 아래 예시는 tracker/run log/git에도 거의 그대로 재사용 가능한 수준의 압축 문장을 목표로 한다.

## 한 줄 목표
A-track implementer가 첫 manifest부터 `floor green`과 `closing green`을 서로 다른 언어로 쓰게 만든다.

### 2-1. Commit 1 floor — 채워진 예시

```md
# commit1-floor manifest

- stage: commit1-floor
- date: 2026-03-18
- status: archive-in-progress
- archive path: docs/artifacts/20260318/commit1-floor/

## owner summary
- programmer: `phase='overrunEvent'` 진입 wrapper와 canonical snapshot field floor를 닫았다. reward apply / page advance / confirm resolve는 여전히 열지 않았다.
- ui: payload freeze 검토만 완료했고 scene card shell closing은 아직 열지 않았다.
- art: neutral/theme/enemy token sanity만 확인했고 신규 장면 자산 마감은 Commit 2 전까지 금지다.
- qa: S1/S2/S3/S7/S8/S9/S10/S11 smoke log를 floor evidence로만 기록했고 S4/S5/S6/S12 closing smoke는 아직 열지 않았다.
- pm: Commit 1은 floor green으로만 기록하며 runtime closing 또는 onboarding 시작 문장은 금지한다.

## required artifacts
- [x] logs/20260318-commit1-qa-smoke-log.txt
- [x] notes/20260318-commit1-programmer-state-diff.md
- [x] notes/20260318-commit1-pm-nonchange-note.md
- [x] notes/20260318-commit1-programmer-payload-sanity.md
- [x] handoff line confirmed

## boundary / non-change
- Commit 1에서는 reward apply / currentPage 증가 / confirm resolve를 열지 않는다.
- Commit 1에서는 `renderOverrunEvent()` 장면 shell, ending helper closing, onboarding primer helper를 열지 않는다.
- old immediate baseline 제거는 Commit 2 evidence bundle에서만 닫는다.

## blockers / notes
- semantic locator drift가 있으면 `continueOverrun`, `pagePlan.push`, `segmentTrophyTemplateIds`, overrun smoke block 기준으로 다시 찾는다.
- `none` fallback은 recovery sanity만 확인하고 visual density closing 평가는 Commit 2로 넘긴다.

## next allowed step
- Commit 1 review packet과 floor evidence 확인 뒤에만 Commit 2 closing을 연다.

## handoff line
> Commit 1 floor archive-in-progress: docs/artifacts/20260318/commit1-floor/ manifest 기준 floor evidence drop 완료, reward apply / scene closing / onboarding은 아직 금지.
```

#### 왜 이 예시가 중요한가
| 포인트 | 이유 |
| --- | --- |
| programmer 1문장 + 금지선 1문장 구조 | `무엇을 닫았는지`와 `무엇을 안 열었는지`가 한눈에 분리된다 |
| UI/아트가 `대기 검토`만 기록 | Commit 1을 시각 closing 턴처럼 오독하지 않게 만든다 |
| QA가 허용 fixture와 금지 fixture를 같이 적음 | floor/closing smoke 섞임을 막는다 |
| handoff line이 금지 범위까지 포함 | run log에 그대로 재사용해도 과장되지 않는다 |

### 2-2. Commit 2 closing — 채워진 예시

```md
# commit2-closing manifest

- stage: commit2-closing
- date: 2026-03-18
- status: archive-ready
- archive path: docs/artifacts/20260318/commit2-closing/

## owner summary
- programmer: `renderCenter()` explicit `overrunEvent` branch와 confirm 1회 resolve, `resolved` reload guard, old immediate baseline 제거 diff를 closing 범위 안에서 닫았다. onboarding helper와 reward/shop primer는 여전히 열지 않았다.
- ui: `theme` / `enemy` / `none` 중앙 장면 카드 capture 3종과 CTA density sanity를 회수했고 ending result helper와 장면 flavor는 분리 유지했다.
- art: neutral/theme/enemy token과 accent sanity를 확인했고 scene/result/primer 경계가 섞이지 않는다는 boundary note를 남겼다.
- qa: S4/S5/S6/S10/S12 closing smoke와 resolve/boundary evidence를 Commit 2 stage로만 묶어 기록했다.
- pm: Commit 2는 runtime closing green으로 기록하고, 다음 허용 단계는 B-1 library 하나만 연다.

## required artifacts
- [x] logs/20260318-commit2-qa-closing-smoke-log.txt
- [x] captures/20260318-commit2-ui-theme-scene.png
- [x] captures/20260318-commit2-ui-enemy-scene.png
- [x] captures/20260318-commit2-ui-none-scene.png
- [x] notes/20260318-commit2-programmer-resolve-diff.md
- [x] notes/20260318-commit2-pm-boundary-note.md
- [x] handoff line confirmed

## boundary / non-change
- Commit 2에서는 first-run onboarding primer helper를 열지 않는다.
- ending helper는 결과/CTA 의미만 남기고 overrun scene flavor를 먹지 않는다.
- reward/shop primer 문구와 library hero-primer 문구는 B-track 전용으로 유지한다.

## blockers / notes
- `none` branch는 무보상 진행이면서도 빈 placeholder가 아니어야 하므로 neutral density sanity를 별도 capture로 유지한다.
- scene capture는 display-only reward summary를 포함하되 실제 apply state는 confirm 이후 로그에서만 증명한다.

## next allowed step
- Commit 2 closing green과 boundary evidence 확인 뒤에만 B-1 library를 연다.

## handoff line
> Commit 2 closing archive-ready: docs/artifacts/20260318/commit2-closing/ manifest 기준 scene/resolve/boundary evidence 완료, 다음 허용 단계는 B-1 library뿐이다.
```

#### 왜 이 예시가 중요한가
| 포인트 | 이유 |
| --- | --- |
| `archive-ready` 조건이 required artifacts와 연결 | 감각 문장 대신 증거 충족 상태로 해석된다 |
| UI/아트가 둘 다 boundary를 직접 언급 | scene/result/primer 혼입을 가장 싸게 막는다 |
| PM handoff line이 다음 허용 단계 하나만 말함 | A-track green 뒤 범위 팽창을 줄인다 |
| `none` branch를 독립 메모로 남김 | fallback을 보조 상태로 취급하는 실수를 막는다 |

---

## 3. B-track 채워진 예시

## 3줄 요약
- B-track은 `surface 1개 = manifest 1개` 원칙이 핵심이다.
- library / battle / reward-shop / ending은 모두 primer지만, 각 stage가 말하는 책임과 금지선이 다르다.
- 아래 예시는 owner summary가 capture 파일보다 먼저 surface 역할을 말하도록 고정한다.

## 한 줄 목표
UI/아트/QA/PM이 primer surface를 generic `온보딩 진행` 문장으로 묶지 못하게 만든다.

### 3-1. B-1 library — 채워진 예시

```md
- stage: b1-library
- status: archive-ready

## owner summary
- ui: `library.book-select` hero-primer 1면과 `openingPlan` / `caution` highlight 2곳 capture를 회수했다. 책 선택 CTA는 끝까지 비차단으로 유지했다.
- art: library 계열 badge/accent/token만 사용했고 battle/reward tone 혼입은 막았다.
- qa: B1-A1~A5 acceptance를 library stage로만 기록했고 battle helper 검수는 아직 열지 않았다.
- pm: B-1 green 문장은 library primer evidence까지만 말하고 B-2 battle은 next step에만 남긴다.

## boundary / non-change
- B-1에서는 battle entry / first-spell / auto-pass helper를 열지 않는다.
- 책 선택 결과를 바꾸는 로직이나 grimoire balance 카피를 이번 stage에서 수정하지 않는다.

## next allowed step
- B-1 archive-ready와 비차단 evidence 확인 뒤에만 B-2 battle을 연다.

## handoff line
> B-1 library archive-ready: docs/artifacts/20260318/b1-library/ manifest 기준 hero-primer evidence 완료, 다음 허용 단계는 B-2 battle뿐이다.
```

### 3-2. B-2 battle — 채워진 예시

```md
- stage: b2-battle
- status: archive-in-progress

## owner summary
- ui: `battle.entry` hero-primer와 `battle.first-spell` inline hint는 닫았고 `battle.auto-pass` helper는 최종 density 검수 중이다.
- art: combat 계열 token만 사용했고 reward/shop accent는 끌어오지 않았다.
- qa: B2-A1~A5는 확인했고 auto-pass recovery 2건은 archive-in-progress로 남긴다.
- pm: B-2는 battle primer in-progress로 기록하고 reward/shop green 문장은 금지한다.

## boundary / non-change
- B-2에서는 reward/shop primer, ending helper, overrun scene flavor를 열지 않는다.
- battle 조작은 비차단이며 주문 사용/자동 종료 로직 변경은 이번 stage 범위가 아니다.

## next allowed step
- auto-pass helper acceptance와 비차단 확인 뒤에만 B-3 reward/shop을 연다.

## handoff line
> B-2 battle archive-in-progress: docs/artifacts/20260318/b2-battle/ manifest 기준 entry/first-spell evidence 완료, auto-pass acceptance 확인 뒤에만 B-3를 연다.
```

### 3-3. B-3 reward/shop — 채워진 예시

```md
- stage: b3-reward-shop
- status: archive-ready

## owner summary
- ui: `reward.first` 1면과 `shop.first` 1면을 각각 분리 회수했고 두 surface의 목적 문구를 같은 카드처럼 보이지 않게 유지했다.
- art: reward는 즉시 보정, shop은 장기 빌드 tone으로 분리했고 boss draft 시각 요소는 기존 규칙을 존중했다.
- qa: B3-A1~A6 acceptance를 reward/shop 분리 evidence와 함께 닫았다.
- pm: B-3 green 문장은 reward/shop 목적 분리까지 포함해 기록하고 ending helper는 아직 next step으로만 남긴다.

## boundary / non-change
- B-3에서는 ending primer, overrun CTA helper, battle auto-pass hint를 열지 않는다.
- boss draft/boss reward baseline은 이번 stage에서 재설계하지 않는다.

## next allowed step
- B-3 archive-ready와 목적 분리 evidence 확인 뒤에만 B-4 ending을 연다.

## handoff line
> B-3 reward/shop archive-ready: docs/artifacts/20260318/b3-reward-shop/ manifest 기준 reward/shop 목적 분리 evidence 완료, 다음 허용 단계는 B-4 ending뿐이다.
```

### 3-4. B-4 ending — 채워진 예시

```md
- stage: b4-ending
- status: archive-ready

## owner summary
- ui: `ending.defeat`, `ending.victory`, `ending.overrun-cta` helper capture를 분리 회수했고 helper는 결과 의미 보조만 맡기고 장면 flavor는 넣지 않았다.
- art: neutral/result token만 사용했고 overrun scene accent 재사용은 금지선으로 유지했다.
- qa: B4-A1~A7 acceptance와 ending-vs-overrun boundary evidence를 같은 stage로 닫았다.
- pm: B-4 green은 ending helper boundary까지 포함해 기록하며 `온보딩 전체 완료` 같은 큰 문장은 쓰지 않는다.

## boundary / non-change
- B-4에서는 `overrunEvent` scene card 어휘, badge, accent를 끌어오지 않는다.
- ending helper는 결과/CTA 의미만 보조하고 다음 권 장면 전달 책임을 갖지 않는다.

## next allowed step
- B-4 archive-ready와 boundary evidence 확인 뒤에만 온보딩 전체 상태 문장을 별도로 검토한다.

## handoff line
> B-4 ending archive-ready: docs/artifacts/20260318/b4-ending/ manifest 기준 ending helper/boundary evidence 완료, overrun scene flavor는 여전히 ending 밖에 둔다.
```

---

## 4. 복붙/재사용 규칙

| 재사용 위치 | 그대로 써도 되는 것 | 조정해야 하는 것 | 이유 |
| --- | --- | --- | --- |
| tracker `최근 큰 변경` | handoff line 핵심 문장 | 배경 설명 1~2문장 추가 | tracker는 why/impact도 짧게 필요하다 |
| automation run log | handoff line, next allowed step | 수정 파일/검증/배포/git 결과 추가 | run log는 실행 증거 문서다 |
| git commit 본문/PR 노트 | stage명, evidence 묶음 이름 | 날짜/경로 세부값 | commit은 더 짧아야 한다 |
| PM 공유 메모 | status, next allowed step | required artifacts 상세 목록 생략 가능 | non-programmer 공유용 밀도 조정 |

### 한 줄 원칙
문장을 새로 발명하지 말고, **manifest exemplar에서 줄여 쓰되 넓혀 쓰지는 않는다.**

---

## 5. 금지 문장 / 금지 예시

| 금지 문장 | 왜 문제인가 | 대신 쓸 문장 |
| --- | --- | --- |
| `오버런 전환 거의 완료` | Commit 1/2와 B-track을 한 덩어리로 뭉갠다 | `Commit 1 floor archive-in-progress` 또는 `Commit 2 closing archive-ready` |
| `온보딩 완료` | B-1~B-4 surface 경계가 사라진다 | `B-3 reward/shop archive-ready`처럼 stage를 명시 |
| `UI도 대충 확인함` | owner summary가 증거 언어가 아니다 | `ui: theme/enemy/none scene capture 회수` |
| `문제 없음` | blocker/boundary가 가려진다 | `overrun flavor는 ending helper에 섞지 않음` |
| `다음은 아마 battle` | next allowed step이 흐려진다 | `B-1 green 뒤에만 B-2 battle을 연다` |

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| manifest fill 규칙은 있는데 완성본 예시가 없어 첫 실전 archive에서 문장 밀도가 다시 흔들리는 위험 | tracker/run log/git 단계 언어가 owner마다 달라질 수 있음 | stage별 채워진 exemplar와 복붙/재사용 규칙을 같은 문서에 고정 | 첫 실제 Commit 1 archive 작성 직전 |
| B-track이 `온보딩 진행` 같은 큰 문장으로 다시 합쳐지는 위험 | surface별 acceptance와 boundary가 무너짐 | B-1~B-4 handoff line을 각각 별도 예시로 분리 | B-1 착수 직전 |
| UI/아트가 capture 파일만 채우고 owner summary를 비워 두는 위험 | 시각 경계와 tone sanity가 파일명만 남고 문장 계약이 사라짐 | surface별 owner summary 예시를 구체화 | Commit 2 / B-4 handoff 직전 |
| PM/QA가 exemplar를 과하게 일반화해 stage 금지선을 누락하는 위험 | half-cutover가 green처럼 기록될 수 있음 | `boundary / non-change`와 `next allowed step`을 예시마다 필수로 포함 | tracker / run log 기록 직전 |

---

## 7. 즉시 다음 액션

1. 다음 실제 stage archive를 열 때는 템플릿만 복사하지 말고 이 문서의 대응 예시를 가장 가까운 stage 기준으로 함께 연다.
2. `docs/artifacts/examples/` 예시 파일은 복붙 출발점으로 사용하되, 날짜/경로/실제 evidence 파일명만 현재 run에 맞게 바꾼다.
3. tracker/run log/git에는 가능하면 `handoff line`과 `next allowed step`을 원문에 가깝게 재사용한다.
4. 새 후속 문서가 필요하다면 `sample capture bundle`처럼 더 좁은 보조만 추가하고, stage 언어 자체는 이 예시집과 manifest fill packet을 기준으로 고정한다.
5. 실제 Commit 1 floor 또는 Commit 2 closing evidence가 생기면, 그 turn의 manifest가 이 exemplar보다 느슨해지지 않았는지 먼저 self-check한 뒤 green 문장을 쓴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
- `docs/dicespell_overrun_onboarding_manifest_fill_summary_kr.md`
- `docs/dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- `docs/artifacts/examples/README.md`
