# DiceSpell `Run Table` Follow-up Reopen Summary

## 3줄 요약
- 이 문서는 `queue closing` 뒤 follow-up이 생겨도 **기존 atlas / battle / oath green을 지우지 않고 새 bounded packet 하나만 연다**는 운영 규칙을 빠르게 공유하기 위한 readable companion이다.
- 핵심은 reopen을 `다시 미완료`나 `전면 재오픈`으로 적지 않고, **packet 이름 / 이번 범위 / 아직 안 여는 범위 / 유지되는 green / 새 archive 경로**를 같이 적는 것이다.
- 첫 화면만 읽어도 PM/QA/프로그래머/UI/아트가 `언제 reopen을 열 수 있는지`, `무슨 이름으로 열어야 하는지`, `무슨 문장은 금지인지`를 바로 판단할 수 있어야 한다.

## 한 줄 목표
queue closing 뒤 follow-up이 필요해도 **기존 signoff는 유지하고 새 packet만 작게 연다.**

## 왜 이 문서를 먼저 보나
`queue closing packet`과 `queue signoff ladder`는 이미 마지막 운영선까지 고정했지만, 실제 follow-up이 생기면 아래 질문이 다시 생긴다.

- 이건 기존 green 취소인가, 새 packet reopen인가?
- 정확히 어떤 이름으로 열어야 하나?
- 무엇은 그대로 green이고 무엇만 다시 열리나?
- tracker / run log / portal / git에는 어떤 문장을 그대로 써야 하나?

이 문서는 그 질문에 **빠르게 답하는 요약판**이다. 실제 계약 문장은 `docs/dicespell_game_flow_token_followup_reopen_packet_kr.md`를 기준으로 본다.

---

## 가장 짧은 결론

### reopen은 이렇게 읽는다
- `follow-up reopen`은 **기존 queue closing 취소가 아니다**.
- `follow-up reopen`은 **새 bounded packet 하나만 여는 것**이다.
- reopen을 열 때는 반드시 아래 5개를 같이 적는다.
  1. packet 이름
  2. 이번에 여는 범위
  3. 이번에도 안 여는 범위
  4. 유지되는 기존 green
  5. 새 archive 경로

### 금지되는 표현
- `Run Table closing 취소`
- `다시 미완료`
- `전면 재오픈`
- `final polish`
- `misc follow-up`

---

## reopen을 열 수 있는 경우

| 상황 | reopen 가능? | 이유 |
| --- | --- | --- |
| `oath banner` CTA copy drift 1건 | 가능 | stage와 범위가 좁다 |
| `battle stakes density` 보정 1건 | 가능 | 새 packet 이름으로 분리 가능하다 |
| queue wording만 다시 정리 | 가능 | stage edit 없이 wording-only reopen으로 좁힐 수 있다 |
| `Run Table 전체 polish` | 불가 | bounded packet이 아니다 |
| `ending helper도 같이`, `reward/shop도 같이` | 불가 | 비범위가 명확하지 않다 |
| 기존 green을 지우고 다시 시작 | 불가 | reopen이 아니라 rollback처럼 읽힌다 |

---

## 좋은 packet 이름 / 나쁜 packet 이름

### 좋은 이름
- `run-table-atlas-threshold-focus-followup`
- `run-table-battle-stakes-density-followup`
- `run-table-oath-banner-cta-copy-followup`
- `run-table-queue-wording-followup`

### 나쁜 이름
- `run-table-final-polish`
- `run-table-followup`
- `ending-cleanup`
- `misc-refine`

### 기준
좋은 이름은 **stage + 다시 여는 이유 + bounded 범위**가 보인다.

---

## 역할별로 기억할 것

### 프로그래머
- reopen은 새 큰 작업이 아니다.
- 문제를 stage 1개 / packet 1개로 좁혀 적는다.
- 기존 green은 그대로 둔다.

### UI / 아트
- `전반적인 톤 정리`는 reopen 이름이 아니다.
- capture / density / starter sanity drift처럼 작은 범위로 적는다.
- 새 archive 경로를 별도로 만든다.

### QA
- `follow-up needed` 메모는 가능하지만 `green 취소` 문장은 금지다.
- `reopen proposal`과 `reopen opened`를 같은 줄에 쓰지 않는다.

### PM/기획
- 마지막 reopen opened 문장은 PM이 쓴다.
- packet 이름 / 범위 / 비범위 / 유지 green / archive path를 같이 적는다.
- queue closing history는 남긴다.

---

## 복붙용 문장

- `Run Table token follow-up reopen: <packet-name> only, previous atlas-route-node / battle-stakes-plaque / oath-banner green 유지.`
- `Follow-up scope: <what-opens>. Still locked: <what-stays-locked>.`
- `Archive: docs/artifacts/run-table-token-delivery/followups/<packet-name>/`

---

## immediate next actions

1. follow-up이 보이면 먼저 `queue closing packet -> queue signoff ladder -> follow-up reopen packet` 순서로 본다.
2. packet 이름이 없으면 reopen을 열지 않는다.
3. 기존 atlas / battle / oath / queue closing entry는 수정하지 않고 새 timestamped note만 append한다.
4. 새 archive bundle은 기존 stage archive와 분리한다.
5. 실제 계약 문장과 금지 문장은 `docs/dicespell_game_flow_token_followup_reopen_packet_kr.md`를 기준으로 본다.

---

## 한 장 결론
follow-up reopen의 핵심은 **`무엇을 다시 여는가`보다 `무엇이 그대로 green으로 남는가`를 먼저 적는 것**이다. 이 규칙만 지켜도 마지막 운영선에서 follow-up이 rollback처럼 보이지 않고, 새 bounded packet 하나만 clean하게 열 수 있다.
