# DiceSpell `overrunEvent` / 첫 런 온보딩 매니페스트 작성 패킷

## 3줄 요약
- 이 문서는 `archive bootstrap packet`과 `docs/artifacts/templates/`가 정의한 starter manifest를 **실제 handoff-ready 문장**까지 채우는 방법을 owner별·stage별로 고정하는 source-of-truth다.
- 핵심은 템플릿을 더 예쁘게 만드는 것이 아니라, `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`을 어떤 밀도와 어떤 단계 언어로 써야 drift가 줄어드는지 못 박는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `지금 manifest에 무엇을 써야 착수 가능한지`, `무엇을 쓰면 아직 half-cutover인데 green처럼 보이는지`, `어떤 문장을 tracker/run log/git에 재사용할 수 있는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
starter manifest를 **archive-ready handoff 계약서 초안**으로 올려, 다음 구현자가 stage evidence를 모으면서도 같은 단계 언어와 같은 비변경선을 계속 재사용하게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell은 `artifact archive packet`, `archive bootstrap packet`, `document stack packet`, `execution reporting packet`까지 있어서 아래 질문에는 이미 대답하고 있다.

- stage archive는 어디에 두는가
- 어떤 폴더를 먼저 만드는가
- 어떤 starter file set을 예약해야 하는가
- source packet과 summary를 어떻게 구분하는가

그런데 실제 구현/검수 직전 마지막으로 남아 있는 공백은 아직 있다.

1. 템플릿이 있어도 `owner summary`에 무엇을 어느 문장 길이로 써야 하는지 기준이 없으면 stage마다 밀도가 흔들린다.
2. `boundary / non-change`와 `blockers / notes`가 섞이면, Commit 1의 금지 범위와 그냥 작업 메모가 같은 층위로 적혀 half-green처럼 보일 수 있다.
3. `next allowed step`과 `handoff line`이 느슨하면 tracker / run log / git에서 단계 언어가 다시 drift한다.
4. UI/아트/PM이 summary만 읽고 manifest를 채우면, stage 문장이 예뻐 보여도 실제 evidence gate가 빠질 수 있다.

즉 지금 필요한 것은 새 archive 규칙이 아니라, **manifest 필드 하나하나를 production handoff 문장으로 채우는 작성 패킷**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. first screen 판정`, `2. 필드별 작성 계약`, `3-1. Commit 1/2 예시` | `archive bootstrap packet`, `evidence manifest`, `commit1/commit2 surgery sheet` | state diff와 non-change line은 느슨한 메모가 아니라 stage 계약 문장으로 남겨야 한다 |
| UI | `2. 필드별 작성 계약`, `3-2. UI 예시`, `4. 금지 문장` | `A-3 UI packet`, `B-1~B-4 packet`, `boundary packet` | capture 파일명뿐 아니라 owner summary / handoff line도 surface boundary를 말해야 한다 |
| 아트 | `2. 필드별 작성 계약`, `3-2. UI/아트 예시` | `visual asset packet`, `content deck`, `boundary packet` | tone note는 예쁜 감상문이 아니라 token/boundary sanity를 기록해야 한다 |
| QA | `2. 필드별 작성 계약`, `3-3. QA 예시`, `4. 금지 문장` | `fixture ledger`, `scorecard`, `execution reporting packet` | acceptance log 존재 여부와 manifest 상태 문장을 같은 단계 언어로 맞춰야 한다 |
| PM/기획 | `1. first screen 판정`, `2. 필드별 작성 계약`, `3-4. PM 예시`, `5. 리스크 & 후속과제` | `document stack packet`, `execution reporting packet`, `gap ledger` | handoff line은 설명 문장이 아니라 tracker/run log/git에 재사용되는 단계 문장이라는 점을 고정한다 |

---

## 1. first screen 판정

### 이 문서를 왜 열어야 하나
- **프로그래머**: manifest를 `대충 메모 적는 곳`이 아니라 실제 stage contract로 쓰기 위해 연다.
- **UI/아트**: capture만 올리고 문장 계약은 비워 두는 실수를 막기 위해 연다.
- **QA/PM**: `archive-ready`를 주장할 때 어떤 문장까지 채워져 있어야 하는지 같은 기준으로 보기 위해 연다.

### 가장 짧은 답
- `status`는 현재 기분이 아니라 **evidence 상태**를 써야 한다.
- `owner summary`는 작업 의지나 일반 코멘트가 아니라 **이번 stage에서 각 owner가 실제로 닫았거나 아직 못 닫은 것**을 적어야 한다.
- `boundary / non-change`는 `이번 stage에서 절대 넓히면 안 되는 선`만 적고, 일반 메모는 `blockers / notes`로 내린다.
- `next allowed step`은 문학적 표현이 아니라 `다음 stage를 열 수 있는 정확한 조건`을 적는다.
- `handoff line`은 tracker / run log / git 기록에 재사용 가능한 한 문장이어야 한다.

### 한 줄 판정 규칙
manifest는 evidence를 보관하는 부록이 아니라, **이번 stage가 어디까지 닫혔는지 말로 잠그는 첫 계약서**다.

---

## 2. 필드별 작성 계약

## 3줄 요약
- 아래 표는 starter manifest의 각 필드가 무엇을 말해야 하고 무엇을 말하면 안 되는지를 정리한다.
- 목적은 같은 템플릿을 써도 stage와 owner에 따라 문장 밀도가 흔들리는 위험을 줄이는 것이다.
- 특히 `status`, `boundary / non-change`, `next allowed step`, `handoff line`은 tracker/run log와 직접 맞물리므로 가장 엄격하게 쓴다.

### 한 줄 목표
필드별로 `무엇을 써야 하는지`와 `무엇은 절대 쓰지 말아야 하는지`를 분리한다.

| 필드 | 반드시 들어가야 할 것 | 쓰면 안 되는 것 | 좋은 기준 문장 |
| --- | --- | --- | --- |
| `status` | `bootstrap-ready`, `archive-in-progress`, `archive-ready` 중 실제 evidence 상태 | `almost done`, `green-ish`, `대체로 완료` 같은 감각 문장 | `archive-in-progress (smoke log/drop 완료, scene capture 미회수)` |
| `owner summary` | owner별 닫힌 범위 / 남은 범위 / 금지선 | 일반 감상, 막연한 칭찬, 역할 밖 판단 | `programmer: Commit 1 floor snapshot 필드 추가 완료, resolve/apply는 여전히 금지.` |
| `required artifacts` | 실제 파일명 또는 체크 상태 | `대충 캡처`, `나중에 로그` 같은 추상어 | `notes/20260318-commit1-programmer-state-diff.md` |
| `boundary / non-change` | 이번 stage에서 유지해야 할 비변경선 2~4개 | 작업 희망사항, 다음 stage 아이디어 | `Commit 1에서는 reward apply / page advance / render shell 확장 금지.` |
| `blockers / notes` | 실제 blocker, 확인 필요점, superseded 메모 | 비변경선과 섞인 금지 조항 | `scene capture는 Commit 2 전까지 예약만 유지.` |
| `next allowed step` | 다음 stage 개방 조건 한 줄 | 이미 끝난 것 재설명, 희망사항 | `Commit 1 review packet 확인 뒤에만 Commit 2 closing을 연다.` |
| `handoff line` | archive 경로 + stage 언어 + evidence 묶음 | vague green, owner 없는 미사여구 | `Commit 1 floor archive-ready: docs/artifacts/YYYYMMDD/commit1-floor/ manifest 기준 floor evidence 완료.` |

### 상태 문장 규칙

| 상태 | 써도 되는 조건 | 써선 안 되는 조건 |
| --- | --- | --- |
| `bootstrap-ready` | 폴더/manifest/starter file set만 준비됨 | smoke/capture 없이 green처럼 쓰기 |
| `archive-in-progress` | starter file 중 일부 evidence가 채워짐 | required artifacts가 다 안 찼는데 완료처럼 쓰기 |
| `archive-ready` | required artifacts + handoff line + next allowed step까지 채워짐 | 리뷰 대기 상태를 green처럼 표현하기 |

### owner summary 밀도 규칙
- owner summary는 owner당 **1~2문장**이면 충분하다.
- 첫 문장은 `무엇을 닫았는지`, 둘째 문장은 `무엇을 아직 열지 않았는지`를 적는다.
- owner summary 안에서 다른 owner의 판단을 대신 쓰지 않는다.

예시:
- 좋음: `ui: Commit 2 scene shell capture 2종 회수, ending helper 혼입은 여전히 금지.`
- 나쁨: `ui: 거의 다 끝났고 QA만 보면 된다.`

---

## 3. stage별 작성 예시

## 3-1. 프로그래머 기준 — Commit 1 / Commit 2

### 3줄 요약
- Commit 1과 Commit 2는 둘 다 programmer note가 필요하지만, 문장 목적이 다르다.
- Commit 1은 `floor와 비변경선`, Commit 2는 `closing과 제거선`이 중심이다.
- 같은 `state diff`여도 Commit 1에는 `무엇을 아직 안 열었는지`, Commit 2에는 `무엇을 제거하고 어떤 confirm 1회성을 닫았는지`가 들어가야 한다.

**한 줄 목표:** Commit 1 note와 Commit 2 note가 서로 다른 stage 문장을 말하게 만든다.

### Commit 1 좋은 작성 예시

```md
- status: archive-in-progress

## owner summary
- programmer: `phase='overrunEvent'` 진입 wrapper와 canonical snapshot field floor를 닫았다. reward apply / page advance / confirm resolve는 여전히 열지 않았다.
- qa: S1/S2/S3/S7 smoke log 생성 완료, S4~S6 closing smoke는 아직 금지.
- pm: Commit 1 기록 문장은 floor 언어만 허용하고 closing green은 쓰지 않는다.

## boundary / non-change
- Commit 1에서는 reward apply / page advance / `resolved` confirm chain을 열지 않는다.
- Commit 1에서는 scene card shell과 ending boundary visual closing을 열지 않는다.
- legacy immediate baseline 제거는 Commit 2까지 대기한다.

## blockers / notes
- semantic locator drift가 있으면 `continueOverrun`, `pagePlan.push`, `segmentTrophyTemplateIds` 기준으로 다시 찾는다.

## next allowed step
- Commit 1 review packet과 smoke evidence 확인 뒤에만 Commit 2 closing을 연다.

## handoff line
> Commit 1 floor archive-in-progress: docs/artifacts/YYYYMMDD/commit1-floor/ snapshot floor evidence drop 완료, closing 항목은 아직 금지.
```

### Commit 2 좋은 작성 예시

```md
- status: archive-in-progress

## owner summary
- programmer: confirm 1회 resolve와 `resolved` guard를 닫았고 old immediate baseline 제거 diff를 notes에 남겼다. B-track primer helper는 아직 열지 않았다.
- ui: `theme` / `enemy` / `none` scene capture 회수 완료, ending helper와의 경계는 boundary note로 분리했다.
- qa: S4/S5/S6/S10/S12 closing smoke log 회수 완료, Commit 1 floor evidence와 stage를 분리 유지했다.

## boundary / non-change
- Commit 2에서는 first-run onboarding helper를 열지 않는다.
- ending result helper는 overrun flavor를 먹지 않는다.
- reward/shop primer 문장은 여전히 B-track 전용이다.

## blockers / notes
- `none` branch capture는 neutral card 밀도 확인 뒤 scene capture 1장 추가 가능.

## next allowed step
- Commit 2 closing green과 boundary evidence 확인 뒤에만 B-1 library를 연다.

## handoff line
> Commit 2 closing archive-in-progress: docs/artifacts/YYYYMMDD/commit2-closing/ resolve/scene/boundary evidence drop 완료, onboarding primer는 아직 미개방.
```

## 3-2. UI / 아트 기준 — B-1~B-4

### 3줄 요약
- B-track manifest는 capture만 체크하면 끝나는 문서가 아니다.
- UI는 surface density, 아트는 token/boundary sanity를 owner summary와 note에서 말해야 한다.
- 특히 B-4는 ending primer가 overrun flavor를 먹지 않는다는 문장이 반드시 남아야 한다.

**한 줄 목표:** capture 파일명과 owner summary가 같은 surface boundary를 반복하게 만든다.

| stage | UI owner summary 좋은 예시 | 아트 note 좋은 예시 | handoff line 핵심 |
| --- | --- | --- | --- |
| B-1 | `library hero-primer capture 회수, grimoire 선택 비차단 유지.` | `hero-primer badge/accent는 library 계열만 사용.` | `library primer evidence 완료` |
| B-2 | `battle entry/first-spell capture 분리, auto-pass helper는 note로 구분.` | `battle hint token은 combat 계열 유지, reward/shop tone 혼입 금지.` | `battle primer evidence 완료` |
| B-3 | `reward-primer`와 `shop-primer`를 각각 회수하고 strip 목적을 분리.` | `reward는 즉시 보정, shop은 장기 빌드 tone 유지.` | `reward/shop evidence 완료` |
| B-4 | `ending primer capture 회수, CTA 의미 보조만 남기고 장면 flavor는 제거.` | `ending helper note에 overrun accent/token 비침범 명시.` | `ending boundary evidence 완료` |

### B-4 ending 좋은 작성 예시

```md
## owner summary
- ui: ending primer capture와 CTA helper capture를 분리 회수했다. helper는 결과 의미 보조만 담당하고 장면 flavor는 추가하지 않았다.
- art: ending helper note에 neutral/result token만 허용하고 overrun accent 재사용은 금지라고 명시했다.
- pm: B-4 green 전까지 온보딩 전체 green 문장은 금지한다.

## boundary / non-change
- B-4에서는 `overrunEvent` scene card 어휘와 accent를 끌어오지 않는다.
- ending helper는 결과/CTA 의미만 보조하고 장면 전달 책임을 갖지 않는다.

## next allowed step
- B-4 archive-ready와 boundary evidence 확인 뒤에만 온보딩 전체 green 문장을 검토한다.
```

## 3-3. QA 기준 — acceptance / smoke 문장

### 3줄 요약
- QA는 manifest를 보고 단순히 로그 파일 유무만 확인하면 안 된다.
- 로그가 어떤 stage 언어와 연결되는지, 그리고 아직 금지된 항목이 무엇인지 같이 확인해야 한다.
- `archive-ready`는 테스트가 있었다는 뜻이 아니라, 테스트가 올바른 stage 문장에 묶였다는 뜻이다.

**한 줄 목표:** QA log와 manifest 상태 문장이 같은 단계명으로 움직이게 만든다.

좋은 QA summary 예시:
- `qa: S1/S2/S3/S7/S8 smoke는 Commit 1 floor evidence로만 기록했고, confirm resolve smoke는 아직 금지.`
- `qa: B-3 acceptance는 reward/shop 분리 evidence로만 기록했고, ending helper 회수는 아직 대상 아님.`

나쁜 QA summary 예시:
- `qa: 테스트 대부분 통과.`
- `qa: 큰 문제는 없어 보임.`

### QA 체크 포인트
1. status가 실제 artifact 상태와 맞는가.
2. required artifacts와 log 파일명이 같은 stage를 가리키는가.
3. handoff line이 summary식 감탄문이 아니라 archive 경로 + stage 언어를 포함하는가.
4. `boundary / non-change`가 실제 금지선으로 적혀 있는가.
5. `next allowed step`이 readiness board / scorecard와 충돌하지 않는가.

## 3-4. PM/기획 기준 — tracker / run log / git 재사용 문장

### 3줄 요약
- PM은 manifest의 handoff line을 그냥 읽는 용도가 아니라, tracker / run log / git에 재사용할 stage 문장으로 봐야 한다.
- 따라서 handoff line은 `무엇이 좋아 보였다`가 아니라 `어느 경로에서 어떤 evidence가 닫혔다`를 말해야 한다.
- stage 언어 drift를 막으려면 `archive-ready`, `floor`, `closing`, `B-1~B-4` 같은 정해진 말만 써야 한다.

**한 줄 목표:** handoff line을 기록 문장 원본으로 쓸 수 있게 만든다.

좋은 handoff line 예시:
- `Commit 1 floor archive-ready: docs/artifacts/20260318/commit1-floor/ manifest 기준 floor evidence 완료.`
- `B-3 reward/shop archive-ready: docs/artifacts/20260318/b3-reward-shop/ reward/shop evidence 완료.`

나쁜 handoff line 예시:
- `Commit 1 거의 완료.`
- `B-3도 이제 감이 잡혔다.`
- `온보딩이 많이 진행됐다.`

---

## 4. 금지 문장 / 금지 패턴

| 금지 패턴 | 왜 문제인가 | 대신 쓸 문장 |
| --- | --- | --- |
| `거의 완료`, `사실상 끝남` | archive 상태를 감각 문장으로 흐린다 | `archive-in-progress`, `archive-ready` |
| `다음은 아마 B-2` | 단계 개방 조건이 흐려진다 | `B-1 green 뒤에만 B-2 battle을 연다` |
| `UI도 같이 봐야 함` | 역할별 실제 제출물이 안 보인다 | `ui: battle-entry capture 회수, auto-pass helper는 아직 금지` |
| `문제 없음` | non-change와 blocker가 사라진다 | `reward/shop primer는 아직 미개방` |
| `온보딩 진행` | surface별 stage가 뭉개진다 | `B-2 battle archive-in-progress` |

### 한 줄 원칙
manifest는 분위기 보고서가 아니라 **stage 책임을 축약한 계약 문서**여야 한다.

---

## 5. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| starter manifest는 있는데 필드 문장 규칙이 없어 owner마다 밀도가 다시 흔들리는 위험 | archive가 있어도 tracker/run log 단계 문장이 drift할 수 있음 | 필드별 작성 계약과 좋은/나쁜 예시를 packet으로 고정 | 다음 Commit 1 archive 생성 직전 |
| `boundary / non-change`와 일반 메모가 섞여 금지선이 다시 흐려지는 위험 | Commit 1/2, B-1~B-4의 bounded execution이 넓어질 수 있음 | 금지선과 blocker note를 필드 단위로 분리 규정 | 다음 stage manifest 작성 시 |
| handoff line이 vague하면 summary가 source처럼 소비되는 위험 | PM/QA가 half-cutover를 green처럼 기록할 수 있음 | archive 경로 + stage 언어 + evidence 묶음만 허용하는 예시 추가 | tracker / run log 기록 직전 |
| UI/아트가 capture는 넣었지만 owner summary를 비워 두는 위험 | visual boundary와 tone sanity가 evidence 파일명만으로는 전달되지 않음 | surface별 UI/아트 summary 예시와 B-4 ending 예시 추가 | Commit 2, B-4 handoff 직전 |

---

## 6. 즉시 다음 액션

1. 다음 stage archive를 열 때는 템플릿만 복사하지 말고, 이 문서의 필드별 작성 계약에 맞춰 `status`, `owner summary`, `boundary / non-change`, `next allowed step`, `handoff line`부터 채운다.
2. tracker / run log / git 기록 문장은 가능하면 manifest `handoff line`을 원문에 가깝게 재사용한다.
3. Commit 1/Commit 2/B-1~B-4는 각각 다른 금지선을 갖는 stage이므로, `boundary / non-change`를 복붙하지 말고 stage별 예시를 기준으로 다시 쓴다.
4. UI/아트/QA/PM handoff 때는 capture 파일만 전달하지 말고, owner summary와 handoff line까지 함께 확인한다.
5. 실제 첫 archive drop이 생기면 후속 문서는 `sample filled manifest`처럼 더 좁은 보조로만 추가하고, stage 언어 체계 자체는 다시 흔들지 않는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_overrun_onboarding_document_stack_packet_kr.md`
