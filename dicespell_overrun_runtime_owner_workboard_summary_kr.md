# DiceSpell `overrunEvent` 런타임 오너 워크보드 요약

## 3줄 요약
- 이 문서는 `overrunEvent` A-track을 실제 구현/검수 턴에서 **오너별로 무엇을 넘기면 되는지** 빠르게 읽게 만드는 readable companion이다.
- 핵심은 Commit 1과 Commit 2를 기술 단계가 아니라 **프로그래머 / UI / 아트 / QA / PM handoff 단계**로 다시 읽게 만드는 것이다.
- Commit 2가 green이 되기 전에는 onboarding B-track을 열지 않는다.

## 한 줄 목표
`이번 커밋에서 각 오너가 무엇을 제출하면 끝인가`를 3~5분 안에 이해하게 만든다.

## 누가 봐야 하나
- **프로그래머**: 이번 턴이 엔진 floor handoff인지 runtime closing handoff인지 먼저 구분해야 할 때
- **UI/아트**: 지금 턴에 실제 장면 카드를 닫는지, 아직 payload/tone sanity만 보는지 확인할 때
- **QA/PM**: tracker/run log에 어떤 handoff 문장을 남겨야 하는지 빠르게 확인할 때

## Commit 1 — enter / snapshot floor
### 왜 존재하나
엔진과 상태 바닥을 닫아 다음 오너가 **같은 snapshot 언어**를 보게 만들기 위한 커밋이다.

### 이번 턴 각 오너가 하는 일
- **프로그래머**: `continueOverrun()` enter wrapper화, canonical field set 저장, normalize/recovery, non-change line 유지
- **UI**: `contentKey` / `badgeLabel` / `accentKey` / `artTokenSet`이 직접 내려오는지 확인
- **아트**: `artTokenSet` / accent drift 메모만 남김
- **QA**: enter/recovery/non-change smoke 판정
- **PM**: `floor green` 문장으로만 기록

### 이번 턴 하면 안 되는 일
- reward apply
- page advance
- cleanup
- 장면 카드 shell 마감
- onboarding primer 착수

### 남길 문장
> 이번 커밋은 `overrunEvent`의 enter 분리와 canonical snapshot floor만 닫아, 다음 오너가 같은 payload 언어로 Commit 2를 시작할 수 있게 만들었다.

## Commit 2 — scene / resolve closing
### 왜 존재하나
Commit 1 payload를 실제 장면 카드와 적용 시점으로 닫아 **runtime-ready handoff**를 만들기 위한 커밋이다.

### 이번 턴 각 오너가 하는 일
- **프로그래머**: `renderOverrunEvent(summary)`, `confirm-overrun-event`, `resolved` guard, old baseline 제거
- **UI**: `badge -> header -> flavor -> reward summary -> CTA -> footnote` 위계와 ending과 다른 shell/tone 정리
- **아트**: `themeTrophy` / `enemyTrophy` / `none` 3축 token/accent sanity 확인
- **QA**: full smoke, confirm 전/후 diff, reload/reclick no-op 판정
- **PM**: `runtime-ready green` 문장으로 기록하고 다음은 B-1만 허용

### 이번 턴 하면 안 되는 일
- onboarding primer 주입
- 새 reward 타입 추가
- 전역 카드 시스템 리디자인

### 남길 문장
> 이번 커밋은 `overrunEvent`의 scene card와 confirm resolve를 닫아, 프로그래머/UI/아트/QA/PM이 같은 장면·같은 적용 시점·같은 closing 문장으로 runtime-ready handoff를 남길 수 있게 만들었다.

## 오너 handoff 순서
### Commit 1
1. 프로그래머 → QA
2. 프로그래머 → UI/아트
3. QA → PM
4. PM → 전체 (`Commit 2만 연다`)

### Commit 2
1. 프로그래머 → UI
2. UI ↔ 아트
3. 프로그래머 → QA
4. QA → PM
5. PM → 전체 (`다음은 B-1만 연다`)

## 공통 evidence bundle
1. smoke 결과
2. 상태 diff 메모
3. UI/아트 evidence
4. 코드 diff 요약
5. handoff 문장

## 리스크 & 후속과제
- Commit 1에서 UI/아트가 너무 일찍 마감하면 중간 상태와 closing 상태가 섞일 수 있다.
- Commit 2에서 오너별 evidence가 따로 놀면 runtime-ready 의미가 사람마다 달라질 수 있다.
- PM이 floor/closing을 같은 green 문장으로 기록하면 half-cutover가 완성처럼 보일 수 있다.

## 바로 다음 액션
1. `runtime patch map -> first runtime delivery -> runtime commit stack -> runtime handoff scorecard -> owner workboard` 순서로 Commit 1 handoff를 먼저 본다.
2. Commit 1 green 뒤에만 `second runtime slice -> second runtime review -> owner workboard` 순서로 Commit 2 handoff를 연다.
3. Commit 2 green 뒤에만 onboarding B-1을 연다.
