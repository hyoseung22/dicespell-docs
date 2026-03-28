# DiceSpell `Run Table` Atlas Route Node Required Artifacts 요약

## 3줄 요약
- 이 문서는 `atlas-route-node` 첫 stage가 archive-ready candidate가 되기 전 **정확히 어떤 artifact가 어떤 역할을 해야 하는지**를 빠르게 읽기 위한 readable companion이다.
- 핵심은 문서를 더 늘리는 것이 아니라, atlas 첫 턴의 제출물을 `캡처 2장 + note/check/manifest 6종`의 **같은 stage 문장**으로 정렬하는 것이다.
- PM / 기획 / 프로그래머 / UI / 아트 / QA가 3~5분 안에 `무엇이 모여야 candidate인지`, `무엇이 아직 generic이면 보류인지`, `왜 battle unlock 전 마지막 점검이 artifact alignment인지`를 이해해야 한다.

## 한 줄 목표
atlas 첫 stage를 `파일 있음`이 아니라 **artifact alignment 있음**으로 판정하게 만든다.

## 왜 이 문서를 먼저 보나
stage packet과 queue packet이 있어도 실제 첫 atlas 턴 직전엔 보통 이런 drift가 생긴다.

- `캡처 두 장은 땄으니 거의 됐겠지`
- `manifest만 있으면 candidate라고 해도 되지 않을까`
- `starter note는 나중에 길게 쓰면 되겠지`
- `hook note는 개발자용이고 handoff line은 PM이 따로 쓰면 되겠지`

이 요약 문서는 그 drift를 막기 위해, atlas stage를 **무엇을 만들까**보다 **무엇이 정렬돼야 다음 단계가 열리나** 기준으로 먼저 읽히게 만든다.

---

## atlas required artifacts 한눈표

| 파일 | 무엇을 증명하나 | owner 핵심 역할 |
| --- | --- | --- |
| `manifest.md` | 지금 stage가 atlas-route-node이고 battle unlock 전 단계라는 사실 | stage boundary / next allowed step 고정 |
| `notes/hook-manifest.md` | root / state / threshold hook vocabulary | 프로그래머가 atlas-only hook 언어를 잠금 |
| `checks/dom-sanity.md` | hook 실제 장착 여부와 누락 없음 | 프로그래머 + QA가 atlas DOM sanity 확인 |
| `captures/atlas-1440-overview.png` | current node + next threshold first-glance | UI가 atlas 보드 첫 시선 증명 |
| `captures/atlas-1440-threshold-focus.png` | threshold 읽힘 집중 증명 | UI가 threshold 연결성 증명 |
| `notes/density-review.md` | overview/focus에 대한 짧은 verdict | UI + QA가 `유지 / 약함 / 과함` 판정 |
| `assets/starter-asset-list.md` | Route Node Starter 충분성 / 비범위 | 아트가 starter sanity와 금지 범위 고정 |
| `notes/handoff-line.md` | candidate/green 직전 복붙 문장 | QA + PM이 atlas stage unlock 문장 정렬 |

---

## 가장 중요한 판정 기준

### candidate로 볼 수 있는 경우
- artifact 8종이 모두 atlas stage archive 안에 있다.
- `manifest`, `hook-manifest`, `dom-sanity`, `handoff-line`이 모두 `atlas-route-node` 같은 stage 문장을 쓴다.
- `density-review`가 overview/focus 두 장을 모두 근거로 판단한다.
- `starter-asset-list`가 `이번 stage에서 아직 안 하는 것`을 분명히 적는다.

### 아직 candidate가 아닌 경우
- 파일은 다 있는데 wording이 generic하다.
- 캡처는 있는데 무엇을 증명하는지 note가 없다.
- starter note가 atlas를 넘어 battle/oath 자산까지 다룬다.
- handoff-line이 `Run Table green` 같은 큰 문장을 쓴다.

### 한 줄 판정
atlas 첫 stage는 **artifact completeness + artifact alignment**가 둘 다 있을 때만 candidate다.

---

## 역할별 결론

| 역할 | 이번 문서에서 바로 가져갈 결론 |
| --- | --- |
| 프로그래머 | hook/diff note는 atlas stage boundary부터 적고 battle/oath 계획은 섞지 않는다 |
| UI | overview/focus는 예쁜 샷이 아니라 다른 질문을 답하는 두 장 세트다 |
| 아트 | starter note는 key art backlog가 아니라 `Route Node Starter` 충분성 메모다 |
| QA | 파일 존재만으로는 부족하고 wording alignment까지 봐야 candidate다 |
| PM/기획 | atlas green은 battle unlock 직전 문장일 뿐 Run Table 전체 green이 아니다 |

---

## immediate next actions
1. `atlas route node stage packet` 다음에 이 요약을 읽고 artifact 8종을 먼저 확인한다.
2. `manifest`, `hook-manifest`, `dom-sanity`, `handoff-line`이 같은 atlas stage 문장을 쓰는지 먼저 본다.
3. overview/focus 캡처와 density-review를 같이 채운다.
4. starter note에는 충분성과 비범위를 함께 적는다.
5. QA는 wording alignment까지 확인한 뒤에만 `archive-ready candidate`를 적고, PM은 그 뒤에만 `atlas-route-node green`을 기록한다.

---

## 한 장 결론
이 문서의 역할은 단순하다. **`atlas 첫 stage evidence가 모였다`를 `atlas 첫 stage artifact가 같은 문장으로 정렬됐다`로 바꾸는 것**이다. 다음 refinement 턴은 이 기준으로 artifact 8종을 채우고, 그 뒤에만 battle unlock을 말해야 한다.
