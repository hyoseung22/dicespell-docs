# DiceSpell `overrunEvent` / 첫 런 온보딩 아카이브 부트스트랩 패킷

## 3줄 요약
- 이 문서는 `artifact archive packet`가 정의한 stage archive 구조를 **실제 구현 턴 직전에 바로 생성 가능한 starter kit**으로 내리는 source-of-truth다.
- 핵심은 새 evidence 종류를 늘리는 것이 아니라, `어떤 폴더를 먼저 만들고`, `어떤 manifest 초안을 먼저 채우고`, `어떤 placeholder를 준비해야 live edit 중간에 archive discipline이 무너지지 않는지`를 stage별로 고정하는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `오늘 첫 폴더를 어떻게 열지`, `무엇이 아직 비어 있어도 되는지`, `무엇이 비어 있으면 green을 쓰면 안 되는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`docs/artifacts/YYYYMMDD/<stage>/`를 **착수 3분 안에 만들 수 있는 bootstrap bundle**로 표준화해, evidence archive가 계획 문서가 아니라 실제 작업 바닥이 되게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell은 `archive packet`, `evidence manifest`, `execution reporting packet`까지 있어서 **무엇을 제출하고 어디에 보관해야 하는지**는 이미 충분히 닫혀 있다.

그런데 실제 구현 직전 마지막 작은 공백은 여전히 남아 있다.

1. `좋아, stage archive를 만든다`는 말은 있는데 실제 첫 3분 안에 어떤 디렉터리와 어떤 파일을 먼저 만들어야 하는지 구현자가 다시 생각해야 한다.
2. `manifest 템플릿` 예시는 있어도 Commit 1/Commit 2/B-1~B-4별로 **초기 체크박스와 handoff 문장 초안**이 다르기 때문에, 결국 첫 턴마다 새로 복붙/수정하다가 구조가 흔들릴 수 있다.
3. stage 폴더는 있어도 `captures/`만 만들어 두고 `notes/`나 `logs/`가 비어 있으면, 다음 reviewer는 `아직 bootstrap도 안 끝났구나`와 `이미 half-green이구나`를 혼동할 수 있다.
4. archive root가 생겼어도 starter bundle이 없으면, live edit 중간에 evidence가 다시 데스크탑/임시 폴더로 튀고 tracker/run log green 문장만 먼저 남을 위험이 있다.

즉 지금 필요한 것은 새 설계가 아니라, **archive 규칙을 실제 첫 생성 동작까지 내린 부트스트랩 패킷**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. bootstrap 순서`, `2. stage별 starter bundle`, `3-1. Commit 1/2 starter` | `artifact archive packet`, `evidence manifest`, `runtime handoff scorecard` | 코드를 열기 전에 stage 폴더/manifest/log placeholder부터 만들고 들어가야 한다 |
| UI | `2. stage별 starter bundle`, `3-2. capture starter` | `A-3 UI packet`, `visual asset packet`, `B-1~B-4 packet` | scene/primer 캡처는 bootstrap 때부터 stage별로 분리돼야 한다 |
| 아트 | `2. stage별 starter bundle`, `3-2. capture starter`, `4. owner drop rule` | `visual asset packet`, `content deck` | token/accent sanity note와 scene capture를 같은 stage bundle 안에서 따로 남겨야 한다 |
| QA | `1. bootstrap 순서`, `4. owner drop rule`, `5. green 전 체크` | `execution reporting packet`, `fixture ledger` | archive bootstrap이 안 된 stage에는 green 문장을 쓰면 안 된다 |
| PM/기획 | `0. first screen 결론`, `5. green 전 체크`, `6. 리스크 & 후속과제` | `readiness board`, `owner handoff packet` | 착수 선언보다 먼저 archive root와 manifest 초안이 있어야 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `오늘 Commit 1을 시작한다면 첫 3분 안에 무엇을 먼저 만들어야 하지?`
- `stage archive가 있다는 말과 실제 starter bundle이 있다는 말은 어떻게 다르지?`
- `아직 evidence가 비어 있어도 되는 것`과 `비어 있으면 착수/green을 막아야 하는 것`은 무엇이지?

### 가장 짧은 답
- 착수 직전에는 먼저 **날짜 루트 + stage 폴더 + 4개 하위 폴더 + manifest 초안**을 만든다.
- `logs/`, `captures/`, `notes/` 안에는 starter placeholder 또는 첫 evidence 파일 이름 계획이 있어야 한다.
- `manifest.md`에는 최소한 `stage`, `date`, `required artifacts`, `next allowed step`, `handoff line 초안`이 들어가야 한다.
- stage bootstrap이 끝나기 전에는 tracker/run log에 stage green 문장을 쓰지 않는다.

### 한 줄 판정 규칙
archive 규칙을 읽는 것과 archive starter bundle을 만든 것은 다르다. **착수는 bundle 생성 이후**부터다.

---

## 1. bootstrap 순서

## 3줄 요약
- 아래 순서는 `Commit 1 floor`, `Commit 2 closing`, `B-1`, `B-2`, `B-3`, `B-4` 전부에 공통으로 적용된다.
- 목적은 live edit 전에 evidence 경로를 먼저 고정해, 구현 도중 새 파일이 임시 위치로 새지 않게 만드는 것이다.
- `폴더 생성 -> manifest 초안 -> owner별 drop placeholder -> 첫 evidence 생성` 순서를 벗어나지 않는다.

### 한 줄 목표
archive를 결과 정리 장소가 아니라 **착수 직전 작업 바닥**으로 만든다.

| 순서 | 해야 할 일 | 완료 기준 | 금지 |
| --- | --- | --- | --- |
| 1 | 날짜 루트 생성 | `docs/artifacts/YYYYMMDD/` 존재 | stage 없이 loose file 생성 |
| 2 | stage 폴더 생성 | `commit1-floor` 또는 해당 stage 폴더 존재 | `commit1`, `floor`, `tmp`처럼 임의 이름 사용 |
| 3 | 기본 하위 폴더 생성 | `logs/`, `captures/`, `notes/` 존재 | `misc/`, `images/`, `etc/` 임의 폴더 사용 |
| 4 | manifest 초안 생성 | `manifest.md`에 stage/date/required artifacts/next step/handoff line 초안 존재 | evidence가 생길 때까지 manifest 미생성 |
| 5 | starter placeholder 또는 파일명 계획 기록 | 각 owner가 넣을 첫 artifact 이름이 manifest 또는 notes에 적혀 있음 | `나중에 넣지 뭐`로 비워 두기 |
| 6 | 첫 evidence 생성 후만 구현 계속 | smoke log 또는 state note 또는 capture 1개라도 stage에 들어감 | 코드 수정 뒤에 archive 정리 |

### bootstrap 완료 최소선
아래 5개가 모두 있으면 `bootstrap-ready`로 본다.
1. 날짜 루트
2. stage 폴더
3. `logs/`, `captures/`, `notes/`
4. `manifest.md`
5. 첫 artifact 이름 계획 또는 첫 artifact 1개

---

## 2. stage별 starter bundle

## 3줄 요약
- stage마다 evidence 종류는 이미 정의돼 있지만, bootstrap 시점에는 **어떤 파일을 먼저 예약해야 하는지**가 다르다.
- Commit 1/2는 smoke/state/scene 경계가 핵심이고, B-track은 surface capture/primer note/handoff line이 핵심이다.
- 아래 표는 `오늘 막 시작하는 stage`에서 최소 어떤 starter file set을 먼저 준비해야 하는지 고정한다.

### 한 줄 목표
stage마다 `첫 파일 3~5개`를 미리 예약해 half-empty archive를 줄인다.

| stage | 먼저 만들 starter 파일 | 왜 이것부터 필요한가 |
| --- | --- | --- |
| `commit1-floor` | `manifest.md`, `logs/YYYYMMDD-commit1-qa-smoke-log.txt`, `notes/YYYYMMDD-commit1-programmer-state-diff.md`, `notes/YYYYMMDD-commit1-pm-nonchange-note.md` | Commit 1은 scene보다 smoke/state/non-change 증명이 먼저다 |
| `commit2-closing` | `manifest.md`, `logs/YYYYMMDD-commit2-qa-smoke-log.txt`, `captures/YYYYMMDD-commit2-ui-theme-scene.png`, `notes/YYYYMMDD-commit2-pm-boundary-note.md` | Commit 2는 scene/resolve/boundary closing을 같이 봐야 한다 |
| `b1-library` | `manifest.md`, `captures/YYYYMMDD-b1-ui-library-primer.png`, `notes/YYYYMMDD-b1-programmer-guidance-state.md` | library는 첫 surface capture와 seen flag note가 핵심이다 |
| `b2-battle` | `manifest.md`, `captures/YYYYMMDD-b2-ui-battle-entry.png`, `notes/YYYYMMDD-b2-programmer-trigger-note.md` | battle은 trigger/hint 타이밍 note가 없으면 capture만으로 판단이 어렵다 |
| `b3-reward-shop` | `manifest.md`, `captures/YYYYMMDD-b3-ui-reward-primer.png`, `captures/YYYYMMDD-b3-ui-shop-primer.png`, `notes/YYYYMMDD-b3-pm-purpose-boundary.md` | reward와 shop은 bootstrap 때부터 따로 분리해야 한다 |
| `b4-ending` | `manifest.md`, `captures/YYYYMMDD-b4-ui-ending-primer.png`, `notes/YYYYMMDD-b4-pm-boundary-note.md`, `notes/YYYYMMDD-b4-art-tone-note.md` | ending은 boundary note가 starter 단계부터 꼭 있어야 overrun flavor 혼입을 막는다 |

---

## 3. 역할별 handoff 포인트

## 3-1. 프로그래머에게 넘길 것

### 3줄 요약
- 프로그래머는 `코드를 열기 전에 archive를 만든다`는 원칙을 실제 파일 생성까지 내려야 한다.
- Commit 1/2는 smoke/state/resolve 파일 이름까지 먼저 고정해야 evidence drift가 줄어든다.
- B-track도 surface helper/state note를 먼저 예약한 뒤 render 주입을 시작한다.

**한 줄 목표:** 구현 시작 전에 `오늘 stage의 첫 diff note가 어디에 남을지`를 먼저 고정한다.

| stage | 프로그래머 starter 파일 | 구현 전 마지막 확인 |
| --- | --- | --- |
| Commit 1 | `state-diff.md`, `nonchange-note.md` | reward apply/page advance를 아직 안 열었는지 manifest에 적기 |
| Commit 2 | `resolve-diff.md`, `boundary-note.md` | old immediate baseline 제거가 같은 stage에 포함되는지 적기 |
| B-1 | `guidance-state.md` | `seenBookSelectPrimer`만 열고 다른 surface flag는 금지라고 적기 |
| B-2 | `trigger-note.md` | `entry/first-spell/auto-pass`만 허용이라고 적기 |
| B-3 | `purpose-boundary.md` | reward/shop 혼합 금지 문장을 적기 |
| B-4 | `ending-boundary.md` | overrun flavor 비침범을 note에 먼저 적기 |

## 3-2. UI/아트에게 넘길 것

### 3줄 요약
- UI/아트는 bootstrap 때부터 capture 파일 이름이 stage 책임을 말하게 만들어야 한다.
- `scene`, `helper`, `reward`, `shop`, `ending`을 starter 단계부터 분리하면 closing review에서 다시 섞일 가능성이 줄어든다.
- 새 대형 자산보다 먼저 필요한 것은 **올바른 이름의 첫 캡처와 tone note**다.

**한 줄 목표:** 첫 캡처 파일명부터 boundary를 말하게 만든다.

| stage | UI/아트 starter 파일 | 금지 |
| --- | --- | --- |
| Commit 2 | `theme-scene.png`, `enemy-scene.png`, `none-scene.png` 중 필요한 것 예약 | `scene.png`, `final.png`처럼 generic 이름 |
| B-1 | `library-primer.png` | `primer.png` 단독 이름 |
| B-2 | `battle-entry.png`, 필요 시 `battle-first-spell.png` | `battle.png` 한 장으로 뭉개기 |
| B-3 | `reward-primer.png`, `shop-primer.png` | `reward-shop.png` 하나로 합치기 |
| B-4 | `ending-primer.png`, `ending-cta.png` | `ending-overrun.png` 같은 혼합 이름 |

## 3-3. QA/PM에게 넘길 것

### 3줄 요약
- QA/PM은 archive bootstrap 자체를 stage readiness의 첫 gate로 봐야 한다.
- `증거가 아직 비었다`와 `증거를 둘 경로조차 없다`는 다른 상태다.
- green 문장은 evidence 품질 이전에 **archive starter bundle 존재 여부**부터 확인한 뒤 써야 한다.

**한 줄 목표:** archive starter bundle이 없으면 리뷰를 시작하지 않는다.

| 역할 | 먼저 볼 것 | 여기서 얻어야 하는 결론 |
| --- | --- | --- |
| QA | `manifest.md`, `logs/`, `notes/` starter file 존재 | smoke/acceptance를 어디에 회수할지 이미 정해졌는지 확인 |
| PM | `manifest.md`의 `next allowed step`, `handoff line 초안` | 단계 언어와 archive 경로가 먼저 잠겼는지 확인 |

---

## 4. owner drop rule

| 역할 | bootstrap 시점 최소 drop | 비고 |
| --- | --- | --- |
| 프로그래머 | `notes/` 안 첫 diff note 파일 생성 | 내용이 한 줄이어도 파일은 먼저 만든다 |
| UI | `captures/` 안 첫 capture 파일명 예약 또는 실제 캡처 1개 | capture가 아직 없으면 manifest에 예약명을 적는다 |
| 아트 | `notes/` 안 tone/token note 파일 생성 | scene capture와 분리한다 |
| QA | `logs/` 안 smoke log 파일 생성 또는 예약 | `smoke.txt` generic 이름 금지 |
| PM | `manifest.md`의 handoff line/next allowed step 초안 | green 전에는 초안 상태로 둔다 |

### 한 줄 원칙
stage starter bundle은 `누가 뭘 넣을지`가 아니라 **누가 어디에 넣을지부터** 고정돼야 한다.

---

## 5. green 전 체크

## 3줄 요약
- archive가 존재한다고 바로 green이 아니다.
- bootstrap-ready와 archive-ready는 다른 상태다.
- 아래 체크를 통과해야 tracker/run log에서 stage 문장을 쓸 수 있다.

### 한 줄 목표
stage 기록이 evidence보다 앞서지 않게 만든다.

| 상태 | 뜻 | 허용 행동 |
| --- | --- | --- |
| `bootstrap-ready` | 폴더/manifest/starter file이 준비됨 | 구현 시작 가능, green 기록 금지 |
| `archive-in-progress` | 첫 evidence가 들어오고 starter file이 채워지는 중 | 중간 리뷰 가능, green 기록 금지 |
| `archive-ready` | required artifacts와 handoff line이 manifest 기준으로 채워짐 | tracker/run log green 기록 가능 |

### green 직전 필수 체크
1. `manifest.md`에 체크박스와 `next allowed step`이 채워져 있다.
2. `logs/`, `captures/`, `notes/`가 모두 존재하고 최소 1개 artifact 또는 superseded note가 있다.
3. stage 문장과 archive 경로가 일치한다.
4. 다음 허용 단계가 readiness board / scorecard와 같은 문장이다.
5. generic 파일명(`final`, `latest`, `misc`)이 없다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| archive 규칙은 있는데 starter bundle이 없어 첫 구현 턴에서 다시 임시 폴더가 생기는 위험 | evidence가 저장소 밖으로 새고 tracker/run log green만 남을 수 있음 | stage별 starter file set과 bootstrap 순서를 packet + template으로 고정 | 다음 Commit 1 착수 직전 |
| manifest 예시는 있는데 stage별 초안이 달라 첫 턴마다 복붙 drift가 생기는 위험 | required artifacts와 next step 문장이 stage마다 흔들릴 수 있음 | templates 폴더에 stage별 starter manifest를 같이 둔다 | 다음 Commit 2/B-track 착수 직전 |
| UI/아트 capture 이름이 generic해서 stage boundary가 파일명부터 흐려지는 위험 | ending/helper/scene capture가 review 때 다시 섞임 | capture starter 파일명을 stage별로 고정 | 첫 Commit 2 캡처 회수 시 |

---

## 7. 즉시 다음 액션

1. 실제 구현 턴을 열기 전에 `docs/artifacts/templates/`에서 해당 stage starter manifest를 복사해 날짜 루트에 먼저 놓는다.
2. Commit 1은 `state-diff` / `nonchange-note`부터, Commit 2는 `scene capture` / `boundary-note`부터 예약한다.
3. B-track은 `surface 1개 = stage bundle 1개` 원칙을 bootstrap 단계부터 그대로 유지한다.
4. tracker/run log에는 green 문장을 쓰기 전에 `bootstrap-ready`인지 `archive-ready`인지 먼저 판정한다.
5. 필요하면 후속 문서화는 `manifest 생성 스크립트`처럼 더 좁은 자동화 보조로만 연다. archive 체계 자체를 다시 크게 흔들지 않는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_onboarding_artifact_archive_packet_kr.md`
- `docs/dicespell_overrun_onboarding_evidence_manifest_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`
- `docs/artifacts/templates/README.md`
