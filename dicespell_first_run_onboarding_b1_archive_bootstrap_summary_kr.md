# DiceSpell 첫 런 온보딩 `B-1 library` archive bootstrap summary

## 3줄 요약
- 이 문서는 `B-1 library` 첫 live turn에서 **archive를 실제로 어떻게 열지**만 빠르게 확인하는 readable companion이다.
- 핵심은 `manifest만 있으면 시작`이 아니라, `manifest + acceptance log + note 4종 + capture 2종 경로`가 먼저 생겨야 B-1 stage가 제대로 시작된다고 못 박는 것이다.
- 목적은 B-1 evidence가 임시 폴더나 generic 파일명으로 새지 않게 하고, `bootstrap-ready -> archive-in-progress -> review-ready`를 같은 문장으로 기록하게 만드는 데 있다.

**한 줄 목표:** B-1 archive를 `docs/artifacts/YYYYMMDD/b1-library/` 아래의 starter bundle로 먼저 열고, 그 뒤에만 semantic anchor와 live edit를 시작한다.

---

## 왜 지금 필요한가
이미 B-1에는 library packet, live kickoff, artifact stub, manifest, semantic anchor, required artifacts, review packet이 있다.

그런데 실제 첫 3분 안에는 여전히 이 질문이 남는다.

> 좋아, 그럼 archive를 진짜 어디에 어떤 파일들로 먼저 만들지?

이 공백이 남아 있으면 다음 문제가 생긴다.
- manifest만 있고 실제 log/capture/note가 비어 있는 반쪽 archive가 남는다.
- `first-open` / `return-state` 캡처가 generic 이름으로 저장된다.
- PM/QA가 `bootstrap-ready`와 `review-ready`를 같은 의미처럼 기록한다.
- B-1 evidence가 다른 surface와 섞인다.

---

## B-1 starter bundle

| 묶음 | canonical path | starter 최소선 |
| --- | --- | --- |
| manifest | `b1-library/manifest.md` | `status / owner summary / boundary / next allowed step / handoff line` 초안 |
| log | `b1-library/logs/YYYYMMDD-b1-qa-acceptance-log.md` | B1-A1~A5 / recovery 3종 예약 문장 |
| capture 1 | `b1-library/captures/YYYYMMDD-b1-library-first-open.png` | 파일명 예약 또는 실제 캡처 |
| capture 2 | `b1-library/captures/YYYYMMDD-b1-library-return-state.png` | 파일명 예약 또는 실제 캡처 |
| note 1 | `b1-library/notes/YYYYMMDD-b1-programmer-guidance-state.md` | `seenBookSelectPrimer` / library-only state starter sentence |
| note 2 | `b1-library/notes/YYYYMMDD-b1-ui-hierarchy-note.md` | hero-primer / highlight / CTA 비차단 starter sentence |
| note 3 | `b1-library/notes/YYYYMMDD-b1-art-token-sanity.md` | `primer-library` / `openingPlan/caution` starter sentence |
| note 4 | `b1-library/notes/YYYYMMDD-b1-pm-boundary-note.md` | `battle/reward/shop/ending 미오픈` starter sentence |

### 핵심
B-1 archive는 **manifest 1 + log 1 + capture 2 + note 4**가 같은 stage 언어로 묶여야 시작된 것으로 본다.

---

## bootstrap 순서

1. `docs/artifacts/YYYYMMDD/b1-library/` 생성
2. `logs/`, `captures/`, `notes/` 생성
3. `manifest.md` starter 초안 작성
4. acceptance log 1개, note 4개 생성
5. `first-open`, `return-state` capture 파일명 예약 또는 첫 캡처 저장
6. 그 뒤에만 semantic anchor 재확인과 live edit 시작

### 금지
- 코드부터 열고 archive를 나중에 정리하기
- `final.png`, `latest-note` 같은 generic 파일명 쓰기
- PM green/B-2 unlock 문장을 bootstrap 단계에서 미리 적기

---

## 상태 사다리

| 상태 | 뜻 | 아직 금지 |
| --- | --- | --- |
| 없음 | stage archive가 아직 없다 | kickoff/green |
| bootstrap-ready | 경로와 starter bundle이 생겼다 | review-ready, B-2 unlock |
| archive-in-progress | starter file에 실제 초안/캡처/로그가 들어오기 시작했다 | green |
| review-ready | required artifacts와 wording alignment가 거의 닫혔다 | PM green 전 B-2 unlock |

### 가장 중요한 판정
`manifest만 있음`은 아직 부족하다.  
`manifest + log + note4 + capture 예약`이 있어야 비로소 **bootstrap-ready**다.

---

## 역할별 빠른 handoff

- **프로그래머:** archive starter bundle 없이 code diff를 시작하지 않는다.
- **UI:** `first-open`, `return-state` 두 장을 분리 저장한다.
- **아트:** token sanity note를 capture와 따로 시작한다.
- **QA:** acceptance log가 없는 상태에서 pass/fail 메모를 다른 곳에 남기지 않는다.
- **PM:** `bootstrap-ready`, `archive-in-progress`, `review-ready`만 기록하고 green/unlock은 나중에 쓴다.

---

## 리스크 & 후속과제

| 리스크 | 대응 |
| --- | --- |
| manifest만 있고 실제 starter file set이 비는 위험 | B-1 starter bundle을 file path 기준으로 고정 |
| capture가 generic 이름이나 archive 밖에 저장되는 위험 | `first-open` / `return-state` canonical file name 고정 |
| note 4종이 없어 semantic/boundary 판단이 캡처만 남는 위험 | guidance/hierarchy/token/boundary note를 bootstrap 필수로 지정 |
| bootstrap-ready와 review-ready가 같은 뜻처럼 기록되는 위험 | verdict ladder를 별도 표로 고정 |

---

## 즉시 다음 액션
- `readiness board -> B-1 live kickoff -> B-1 archive bootstrap -> B-1 manifest -> B-1 semantic anchor` 순서로 읽는다.
- archive를 먼저 만들고 starter bundle을 채운다.
- 그 뒤에만 live edit를 시작한다.
- B-2 unlock은 여전히 review/green 뒤에만 쓴다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_first_run_onboarding_b1_live_kickoff_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_manifest_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_semantic_anchor_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_required_artifacts_packet_kr.md`
- `docs/dicespell_first_run_onboarding_b1_review_packet_kr.md`
