# DiceSpell `overrunEvent` / 첫 런 온보딩 증거 스텁 킷 패킷

## 3줄 요약
- 이 문서는 `artifact archive`, `archive bootstrap`, `manifest fill`, `filled manifest examples`까지 이미 닫힌 상태에서도 마지막으로 남아 있던 작은 실전 공백, 즉 **`logs/`와 `notes/`의 첫 파일 안에 정확히 어떤 바닥 문장을 먼저 써 두어야 하는가**를 고정하는 source-of-truth다.
- 지금 DiceSpell 문서는 폴더 구조, manifest 문장, stage exemplar까지 충분하지만, 실제 첫 live edit 직전에는 여전히 `좋아, manifest는 만들었어. 그런데 state diff 초안과 boundary note 초안은 어디까지를 빈칸으로 두고 어디까지를 먼저 적어야 green 과장을 막지?`가 흔들릴 수 있다.
- 목표는 새 규칙을 더 늘리는 것이 아니라, 이미 닫힌 stage contract를 **복붙 가능한 text stub + 역할별 첫 문장 + stage별 금지 placeholder** 수준까지 내려 실제 implementer/reviewer가 첫 3분 안에 같은 글감으로 evidence 바닥을 깔게 만드는 것이다.

**한 줄 목표:** `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`의 `logs/`·`notes/` starter file을 **빈 파일이 아니라 honest stub**로 맞춰, 이후 evidence가 채워져도 단계 문장과 boundary가 다시 흔들리지 않게 만든다.

---

## 왜 이 문서가 필요한가
- 지금까지는 아래가 이미 충분히 닫혔다.
  - `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
  - `docs/dicespell_overrun_onboarding_archive_bootstrap_packet_kr.md`
  - `docs/dicespell_overrun_onboarding_manifest_fill_packet_kr.md`
  - `docs/dicespell_overrun_onboarding_filled_manifest_examples_kr.md`
  - `docs/artifacts/templates/`
  - `docs/artifacts/examples/`
- 그런데 실제 첫 라이브 턴 직전에는 문서가 충분한 상태에서 오히려 더 작은 공백이 남는다.
  1. `logs/`와 `notes/` 파일은 이름만 만들고 본문이 비어 있어, 첫 증거가 들어오기 전까지는 owner가 다시 generic 메모를 쓰기 쉽다.
  2. `TODO`, `later`, `final` 같은 느슨한 placeholder가 stage contract보다 먼저 자리 잡으면, manifest는 좋아 보여도 실제 note/log가 같은 단계 언어를 못 따른다.
  3. Commit 1의 `non-change note`, Commit 2의 `boundary note`, B-1의 `guidance state`, B-2의 `trigger note`는 서로 다른 역할인데도, 빈 파일에서 시작하면 다시 `notes.md` 하나로 뭉개질 수 있다.
  4. 아카이브 규칙은 있어도 실제 첫 입력 문장이 없으면 reviewer가 `이건 준비만 된 건가, 아니면 일부 evidence가 이미 채워진 건가?`를 헷갈릴 수 있다.
- 즉 이번 문서는 새 stage를 추가하는 문서가 아니라, **이미 닫힌 stage를 실제 파일 바닥 글감까지 정렬하는 마지막 handoff 문서**다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. stub layer`, `2. Commit 1/2 stub` | `commit1/commit2 surgery sheet`, `filled manifest examples` | state diff / resolve diff / non-change note를 빈 파일이 아닌 stage-specific 초안으로 시작한다 |
| UI | `3. capture reservation rule`, `4. B-track stub` | `visual asset packet`, `B-1~B-4 packet` | 캡처는 manifest에 예약하고, hierarchy/boundary 메모는 text stub로 먼저 깐다 |
| 아트 | `4. B-track stub`, `5. tone/boundary rule` | `visual asset packet`, `content deck` | tone note와 boundary note를 감상문이 아니라 token/role sanity로 시작한다 |
| QA | `1. stub layer`, `6. QA log stub` | `evidence manifest`, `execution reporting packet` | smoke/acceptance log도 pass/fail 결과 전용 빈 텍스트가 아니라 fixture/범위 선언이 먼저 있어야 한다 |
| PM/기획 | `0. first screen 결론`, `7. handoff line guard` | `manifest fill packet`, `filled manifest examples` | `archive-ready` 문장은 manifest에 두고, note/log stub에는 단계 바닥만 적는다 |
| 자동화 writer | `8. append-only 기록 규칙`, `9. starter kit 파일` | `automation run log`, `docs/artifacts/stubs/README.md` | 새 run은 기존 note를 덮어쓰지 않고 새 stub 또는 새 버전 파일 위에만 evidence를 쌓는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `notes/20260318-commit1-programmer-state-diff.md`는 첫 줄에 무엇부터 써야 하지?
- `logs/...smoke-log.txt`가 아직 비어 있어도 generic placeholder 대신 어떤 바닥 문장을 남겨야 reviewer가 stage를 오해하지 않지?
- B-1/B-2/B-3/B-4에서 note 파일은 각각 무엇을 절대 쓰지 말아야 하지?

### 가장 짧은 답
- `manifest.md`가 stage contract라면, `logs/`와 `notes/`는 **그 계약을 깨지 않는 첫 문장**이 있어야 한다.
- stub는 `완료 문장`이 아니라 `이번 파일이 무엇을 증명할 예정인지`, `이번 단계에서 무엇을 아직 열지 않았는지`, `다음 evidence가 어떤 형식으로 들어올지`만 말한다.
- 캡처 파일은 fake PNG를 만들지 않는다. 대신 manifest의 required artifacts와 text stub의 `capture reservation` 문장으로 연결한다.
- `final`, `done`, `거의 끝`, `정상 같음` 같은 감정형 placeholder는 금지한다.

---

## 1. stub layer: 세 가지 상태만 허용한다

| 상태 | 의미 | stub에 적어도 되는 것 | stub에 적으면 안 되는 것 |
| --- | --- | --- | --- |
| `bootstrap-ready` | 파일을 막 만들었고 아직 실제 evidence는 안 들어옴 | stage명, owner, 예정 evidence 종류, 금지 범위, 다음 업데이트 위치 | pass/fail, green, 완료 선언 |
| `archive-in-progress` | 첫 evidence 일부가 들어왔지만 stage가 아직 안 닫힘 | 현재 채워진 증거, 아직 비어 있는 항목, 다음 확인 포인트 | `runtime-ready`, `archive-ready`, 다음 stage 오픈 선언 |
| `archive-ready` | manifest 기준 필수 evidence가 모두 찼음 | 이 상태는 manifest와 run log가 말함 | 개별 stub 파일에서 단독 green 선언 |

### 운영 원칙
- stub 파일은 `빈칸`을 줄이기 위한 것이지 `별도 보고서`가 아니다.
- green 문장은 여전히 `manifest.md`와 tracker/run log에만 남긴다.
- stub는 `이번 파일이 무엇을 담아야 하는지`와 `무엇은 아직 담지 말아야 하는지`를 먼저 잠그는 역할만 한다.

---

## 2. Commit 1 / Commit 2 source stub

## 2-1. Commit 1 floor

### 어떤 stub가 필요한가
| 파일 | 왜 필요한가 | 첫 문장 최소선 | 금지 문장 |
| --- | --- | --- | --- |
| `commit1-qa-smoke-log` | S1/S2/S3/S7/S8/S9/S10/S11 범위를 먼저 잠그기 위해 | `이 로그는 Commit 1 floor smoke 범위만 기록한다.` | `전체 smoke pass 예정`, `오버런 완료` |
| `commit1-programmer-state-diff` | `phase='overrunEvent'`와 canonical snapshot floor를 바닥으로 고정하기 위해 | `이번 diff는 enter/snapshot floor만 기록하고 reward apply/page advance는 아직 열지 않는다.` | `scene도 같이 봄`, `거의 다 끝남` |
| `commit1-pm-nonchange-note` | 이번 턴에 열지 않은 범위를 reviewer가 즉시 읽게 하기 위해 | `이번 note는 Commit 1 비변경선만 기록한다.` | `다음에 알아서`, `나머지는 추후` |
| `commit1-programmer-payload-sanity` | UI/아트가 읽을 snapshot field sanity를 글로 먼저 고정하기 위해 | `contentKey/badgeLabel/accentKey/artTokenSet payload shape만 확인한다.` | `레이아웃 확정`, `scene polish` |

### Commit 1 handoff 포인트
- **프로그래머에게 넘길 것:** state diff stub에 `phase`, `resolved`, `selectedRewardId`, `currentPage` 비변경선 칸을 먼저 만든다.
- **UI에게 넘길 것:** payload sanity는 디자인 확정 메모가 아니라, `scene-card 미오픈`을 포함한 data-shape 메모로 시작한다.
- **QA에게 넘길 것:** smoke log 첫 줄에 `Commit 1 floor 전용`을 적어 Commit 2 fixture가 혼입되지 않게 만든다.

## 2-2. Commit 2 closing

### 어떤 stub가 필요한가
| 파일 | 왜 필요한가 | 첫 문장 최소선 | 금지 문장 |
| --- | --- | --- | --- |
| `commit2-qa-closing-smoke-log` | S4/S5/S6/S10/S12 closing 범위를 먼저 잠그기 위해 | `이 로그는 Commit 2 closing smoke만 기록한다.` | `모든 overrun smoke`, `UI 점검 전체` |
| `commit2-programmer-resolve-diff` | confirm 1회 적용과 `resolved` guard를 글로 먼저 못 박기 위해 | `이 diff는 resolve/no-op/old baseline 제거만 기록한다.` | `onboarding도 같이`, `마감 전반` |
| `commit2-pm-boundary-note` | ending-vs-overrun 경계를 실증 전에도 같은 문장으로 유지하기 위해 | `이 note는 결과 화면과 scene 카드 경계만 확인한다.` | `ending 개선`, `분위기 좋음` |

### Commit 2 handoff 포인트
- **프로그래머에게 넘길 것:** resolve diff stub에는 `confirm 1회`, `none branch`, `reload guard`, `old immediate baseline 제거`의 4칸을 먼저 만든다.
- **UI/아트에게 넘길 것:** scene capture 자체는 PNG로 나중에 들어오더라도 boundary note는 text stub로 먼저 연다.
- **PM/QA에게 넘길 것:** Commit 2 boundary note에 `B-track 미오픈` 문장을 먼저 적어 closing과 onboarding이 합쳐지지 않게 한다.

---

## 3. capture reservation rule

- fake screenshot, 빈 PNG, `placeholder.png`는 만들지 않는다.
- 대신 아래 둘 중 하나로 예약한다.
  1. `manifest.md`의 required artifacts 체크박스에 파일명을 적는다.
  2. 대응 text stub에 `capture reservation` 줄을 만든다.

예시
```md
## capture reservation
- expected file: captures/20260318-commit2-ui-theme-scene.png
- expected file: captures/20260318-commit2-ui-none-scene.png
- note: ending helper capture는 이 stage에 포함하지 않는다.
```

### 왜 fake capture를 금지하나
- 텍스트 stub는 honest placeholder가 되지만, 빈 PNG는 evidence처럼 보이기 쉽다.
- stage가 아직 안 닫혔는데 캡처 폴더가 채워진 것처럼 보이면 reviewer가 `archive-ready`와 `reservation-only`를 혼동할 수 있다.

---

## 4. B-track surface stub

| 단계 | 필수 text stub | 이 stub가 먼저 말해야 하는 것 | 금지 문장 |
| --- | --- | --- | --- |
| `B-1 library` | `guidance-state note` | `seenBookSelectPrimer`, `openingPlan/caution highlight`, 책 선택 비차단 | `튜토리얼 완료`, `library polish` |
| `B-2 battle` | `trigger note` | `battle.entry`, `first-spell`, `auto-pass` 중 이번 턴에 여는 trigger와 미오픈 trigger | `전투 도움말 전체`, `힌트 다 붙임` |
| `B-3 reward-shop` | `purpose-boundary note` | reward와 shop을 왜 따로 다루는지, 어떤 primer가 아직 비어 있는지 | `reward/shop 통합 완료` |
| `B-4 ending` | `boundary note`, `tone note` | ending helper가 어디까지 말하고 overrun flavor를 어디까지 넘기는지 | `ending 전체 정리`, `오버런 장면까지 포함` |

### 역할별 handoff
- **프로그래머:** B-1/B-2 note는 seen flag/trigger hook 바닥부터 적는다.
- **UI:** B-3/B-4 note는 hierarchy와 역할 경계를 먼저 적고 spacing 감상문은 뒤로 민다.
- **아트:** B-4 tone note는 `warm`, `dark` 같은 형용사보다 `ending helper는 neutral accent`, `overrun flavor token 미포함`처럼 토큰 문장으로 시작한다.

---

## 5. note 문장 규칙

### 허용되는 문장 패턴
- `이 파일은 Commit 1 floor state diff만 기록한다.`
- `현재 미오픈 범위: reward apply / page advance / onboarding.`
- `예정 evidence: S1/S2/S3/S7/S8/S9/S10/S11 smoke 결과.`
- `capture reservation: ...`
- `다음 업데이트는 resolve diff 추가 후 수행.`

### 금지되는 문장 패턴
- `거의 끝남`
- `정상 같음`
- `마감 느낌 좋음`
- `나중에 정리`
- `전체 green 예정`
- `온보딩 진행`

### 왜 이렇게 좁게 쓰나
- stub가 감상문이 되는 순간 manifest/source packet의 stage contract가 다시 약해진다.
- stub는 구현 아이디어를 늘리는 칸이 아니라 **증거 파일의 책임을 줄 단위로 고정하는 칸**이어야 한다.

---

## 6. QA log stub 규칙

### QA가 첫 줄에 반드시 적을 것
1. stage명
2. 범위 fixture 또는 acceptance 묶음
3. 이번 로그가 다루지 않는 범위
4. 다음 append 위치

예시
```md
# Commit 2 closing smoke log

- stage: commit2-closing
- scope: S4 / S5 / S6 / S10 / S12
- not in scope: B-1 / B-2 / B-3 / B-4, balance sim, visual polish sign-off
- next append: resolve click evidence 추가 후 pass/fail 업데이트
```

### 금지 패턴
- `pass 예정`
- `정상으로 보임`
- fixture 번호 없는 `close flow ok`

---

## 7. handoff line guard

- handoff line은 manifest 전용이다.
- stub 파일에서는 아래처럼 `manifest handoff 예정 문장`까지만 적는다.

예시
```md
## manifest handoff candidate
- Commit 1 floor archive-ready 문장은 manifest.md에서만 확정한다.
- 이 note는 non-change line 근거만 유지한다.
```

### 왜 manifest 전용인가
- handoff line이 note/log 곳곳에 흩어지면 stage 문장이 다시 두세 버전으로 갈라진다.
- filled manifest example이 이미 있으므로, stub는 evidence 바닥만 말하고 완료 문장은 manifest에 맡기는 편이 안전하다.

---

## 8. append-only 기록 규칙

- stub 파일은 나중에 `덮어쓰기 보고서`로 바꾸지 않는다.
- evidence가 들어오면 아래 둘 중 하나만 허용한다.
  1. 같은 파일 안에 `## update YYYY-MM-DD HH:MM` 섹션을 append
  2. `-v2`, `-1430` suffix의 새 파일을 만들고 manifest에 superseded note 추가
- 자동화 run log와 tracker는 stage 완료를 append한다. stub 내용 정리를 위해 과거 green entry를 대규모 수정하지 않는다.

---

## 9. 실제 starter kit 파일

이번 슬롯에서 함께 추가하는 텍스트 stub starter kit:
- `docs/artifacts/stubs/stage_stub_commit1_floor_smoke_log.md`
- `docs/artifacts/stubs/stage_stub_commit1_floor_state_diff.md`
- `docs/artifacts/stubs/stage_stub_commit1_floor_nonchange_note.md`
- `docs/artifacts/stubs/stage_stub_commit2_closing_smoke_log.md`
- `docs/artifacts/stubs/stage_stub_commit2_closing_resolve_diff.md`
- `docs/artifacts/stubs/stage_stub_commit2_closing_boundary_note.md`
- `docs/artifacts/stubs/stage_stub_b1_guidance_state.md`
- `docs/artifacts/stubs/stage_stub_b2_trigger_note.md`
- `docs/artifacts/stubs/stage_stub_b3_purpose_boundary.md`
- `docs/artifacts/stubs/stage_stub_b4_boundary_note.md`
- `docs/artifacts/stubs/stage_stub_b4_tone_note.md`
- `docs/artifacts/stubs/README.md`

### 사용 순서
1. 대응 `manifest.md` 템플릿 또는 exemplar를 먼저 연다.
2. stage에 맞는 stub 파일을 복사한다.
3. `YYYYMMDD`, stage명, owner, expected capture 예약명을 바꾼다.
4. 실제 evidence가 들어오면 append하거나 `v2`를 만든다.
5. `archive-ready` 문장은 stub가 아니라 manifest와 run log에만 남긴다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 starter manifest와 exemplar가 있어도 실제 note/log 본문은 다시 `TODO`, `later`, `final` 같은 빈약한 placeholder로 시작해 stage 언어가 드리프트하는 것이다.
- 두 번째 리스크는 fake capture를 만들어 `captures/`가 채워진 것처럼 보이게 하거나, 반대로 capture 예약을 어디에도 적지 않아 reviewer가 evidence 누락과 예약 상태를 구분하지 못하는 것이다.
- 세 번째 리스크는 stub 파일 안에 handoff line까지 중복 기록해 manifest/source packet이 이미 닫아 둔 완료 문장이 다시 두 버전으로 갈라지는 것이다.
- 따라서 다음 실제 Commit 1/Commit 2/B-track 착수 전에는 `filled manifest examples -> artifact stub packet -> docs/artifacts/stubs/README.md` 순서로 먼저 읽고, 빈 note/log를 generic 메모로 시작하지 말아야 한다.

## immediate next actions
1. 다음 Commit 1 착수 전 `stage_manifest_commit1_floor.md`와 `stage_stub_commit1_floor_*` 3종을 같이 열어 state diff / non-change / smoke log 바닥 문장을 먼저 채운다.
2. Commit 2 착수 전 `stage_stub_commit2_closing_boundary_note.md`에 `ending-vs-overrun` 경계를 먼저 적고 그 다음 scene capture를 모은다.
3. B-track은 각 surface별 packet과 대응 stub 1종만 먼저 열고, 여러 surface note를 하나로 합치지 않는다.
4. run log/tracker green 문장은 여전히 manifest 기준으로만 남기고, stub 파일에는 근거와 비변경선만 유지한다.
