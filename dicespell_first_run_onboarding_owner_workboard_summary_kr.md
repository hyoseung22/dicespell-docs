# DiceSpell 첫 런 온보딩 오너 워크보드 요약

## 3줄 요약
- 이 문서는 첫 런 온보딩 B-track을 실제 구현/검수 턴에서 **오너별로 무엇을 넘기면 되는지** 빠르게 읽게 만드는 readable companion이다.
- 핵심은 `B-1 library`, `B-2 battle`, `B-3 reward/shop`, `B-4 ending`을 기술 단계가 아니라 **프로그래머 / UI / 아트 / QA / PM handoff 단계**로 다시 읽게 만드는 것이다.
- B-4가 green이 되기 전에는 `온보딩 전체 polish`나 `overrunEvent flavor 보강`을 새 목표로 열지 않는다.

## 한 줄 목표
`이번 surface 커밋에서 각 오너가 무엇을 제출하면 끝인가`를 3~5분 안에 이해하게 만든다.

## 누가 봐야 하나
- **프로그래머**: 이번 턴이 어떤 surface handoff인지, 누구에게 무엇을 넘기면 끝나는지 빠르게 확인할 때
- **UI/아트**: 지금 턴이 실제로 primer readable sanity를 마감하는 턴인지, 아직 대기/검수 턴인지 확인할 때
- **QA/PM**: tracker/run log에 어떤 handoff 문장을 남겨야 하는지 surface별로 빠르게 확인할 때

## B-1 — library primer floor
### 왜 존재하나
첫 책 선택 surface를 `첫 진입 guidance`로만 닫아 다음 오너가 같은 shelf 상태를 보게 만들기 위한 커밋이다.

### 이번 턴 각 오너가 하는 일
- **프로그래머**: `seenBookSelectPrimer` 저장/복구, shelf hero-primer 주입, primer 비차단 처리
- **UI**: hero-primer와 책 카드 hierarchy, highlight weight sanity 확인
- **아트**: `library.book-select` token/accent sanity 메모만 남김
- **QA**: 첫 진입 / 재진입 미노출 / 선택 비차단 / reload 복구 판정
- **PM**: `library primer green` 문장으로만 기록

### 이번 턴 하면 안 되는 일
- battle/reward/shop/ending primer 선반영
- shelf 전면 리뉴얼
- `온보딩 추가` 같은 포괄 완료 보고

### 남길 문장
> 이번 커밋은 library primer의 첫 진입 guidance와 seen 제어만 닫아, 다음 오너가 같은 shelf 상태와 같은 종료 문장으로 B-2를 시작할 수 있게 만들었다.

## B-2 — battle trigger handoff
### 왜 존재하나
첫 전투에서 `무엇을 해야 하는지`와 `왜 자동 종료되었는지`를 같은 battle surface 안에서 handoff 가능하게 만들기 위한 커밋이다.

### 이번 턴 각 오너가 하는 일
- **프로그래머**: `battle.entry`, `battle.first-spell`, `battle.auto-pass` trigger와 seen flag 저장/복구 연결
- **UI**: hero vs inline hint 우선순위, 과밀 중첩 방지 sanity 확인
- **아트**: battle token/accent drift 체크
- **QA**: entry / first-spell / auto-pass trigger 증거와 비차단성 판정
- **PM**: `battle primer green` 문장으로 기록

### 이번 턴 하면 안 되는 일
- reward/shop/ending primer 선반영
- 전투 메카닉 변경
- 로그/버튼 전면 리뉴얼

### 남길 문장
> 이번 커밋은 battle primer의 entry/first-spell/auto-pass trigger와 비차단성을 닫아, 다음 오너가 같은 전투 trigger 언어와 같은 종료 문장으로 B-3를 시작할 수 있게 만들었다.

## B-3 — reward / shop intent handoff
### 왜 존재하나
reward와 shop이 둘 다 짧은 안내로 뭉개지지 않게, `보상`과 `자원 소비 판단`의 목적 차이를 same-source handoff로 고정하기 위한 커밋이다.

### 이번 턴 각 오너가 하는 일
- **프로그래머**: `seenRewardPrimer` / `seenShopPrimer`, reward/shop primer 주입, boss draft 공존, CTA 비차단 처리
- **UI**: reward vs shop accent/배지/카피 hierarchy 확인
- **아트**: reward/shop/neutral token 경계 sanity 확인
- **QA**: reward 첫 노출 / shop 첫 노출 / boss draft 공존 / reload 복구 판정
- **PM**: `reward/shop primer green` 문장으로 기록

### 이번 턴 하면 안 되는 일
- ending primer 선반영
- reward/shop 규칙 변경
- 둘을 같은 작은 strip으로 뭉개기

### 남길 문장
> 이번 커밋은 reward와 shop primer의 목적 분리와 비차단성을 닫아, 다음 오너가 같은 보상/상점 분기 의미와 같은 종료 문장으로 B-4를 시작할 수 있게 만들었다.

## B-4 — ending boundary closing
### 왜 존재하나
ending primer를 결과/CTA 의미 layer로만 닫고, `themeTrophy` / `enemyTrophy` / `none` flavor는 끝까지 overrunEvent에 남기기 위한 커밋이다.

### 이번 턴 각 오너가 하는 일
- **프로그래머**: defeat/victory/overrun CTA primer, seen flag 저장/복구, CTA 비차단, boundary 유지
- **UI**: 결과 요약 vs CTA helper hierarchy, ending과 overrunEvent 분리 sanity 정리
- **아트**: defeat/victory/CTA token/accent sanity, overrun bridge tone 메모
- **QA**: defeat / victory / CTA 노출, 비차단, overrun flavor 비침범, reload 복구 판정
- **PM**: `ending primer green` 문장으로 기록

### 이번 턴 하면 안 되는 일
- overrunEvent flavor를 ending 본문/helper에 넣기
- overrun scene shell 선행 수정
- ending과 overrunEvent를 같은 green 문장으로 기록하기

### 남길 문장
> 이번 커밋은 ending primer의 결과/CTA 의미와 overrunEvent 비침범 경계를 닫아, 프로그래머/UI/아트/QA/PM이 같은 ending 종료선과 같은 closing 문장으로 온보딩 B-track을 마감할 수 있게 만들었다.

## 오너 handoff 순서
- **B-1**: 프로그래머 → QA → UI/아트 → PM
- **B-2**: 프로그래머 → UI → QA → PM
- **B-3**: 프로그래머 → UI/아트 → QA → PM
- **B-4**: 프로그래머 → UI ↔ 아트 → QA → PM

## 공통 evidence bundle
1. surface primer 화면 증거
2. seen flag 또는 동등 상태 메모
3. 비차단 / reload 복구 메모
4. UI/아트 sanity 메모
5. 코드 diff 요약
6. handoff 문장

## 리스크 & 후속과제
- UI/아트가 너무 일찍 다음 surface tone까지 확정하면 B-track의 `surface 1개 = commit 1개` 원칙이 무너질 수 있다.
- B-3에서 reward와 shop을 같은 strip으로 처리하면 목적 분리 handoff가 흐려질 수 있다.
- B-4에서 ending green과 overrunEvent green을 같은 문장으로 기록하면 boundary closing이 다시 섞일 수 있다.

## 바로 다음 액션
1. A-track green 뒤에만 `runtime stack -> handoff scorecard -> owner workboard` 순서로 B-1 handoff를 연다.
2. B-1/B-2/B-3/B-4는 각각 별도 evidence bundle과 별도 handoff 문장으로만 닫는다.
3. B-4 green 뒤에도 ending flavor 보강이 아니라 boundary 유지 확인만 추가 점검한다.
