# DiceSpell `Run Table` Battle Candidate Review 요약

- **이 문서는 왜 있나:** battle second stage에서 artifact 8종이 모인 뒤, QA candidate와 PM green을 어떤 순서/질문/문장으로 분리할지 고정하려고 만든 review 요약이다.
- **누가 먼저 읽나:** QA/PM이 먼저 읽고, 프로그래머/UI/아트는 자기 artifact가 어떤 review 질문에 걸리는지 확인할 때 본다.
- **첫 화면 결론:** battle 두 번째 stage는 `candidate = review pass`, `green = oath unlock 기록`으로 분리한다. 두 초록불을 같은 뜻으로 쓰지 않는다.

## 한 줄 목표
`battle-stakes-plaque` 두 번째 stage를 제출물 수집이 아니라 **candidate review -> green/unlock** 절차로 닫는다.

## 왜 이게 중요한가
- artifact가 8종 있어도 QA와 PM이 다른 기준으로 읽으면 `전투 UI 거의 완료`, `사실상 green` 같은 문장이 다시 나온다.
- battle green이 커지면 oath가 너무 빨리 열리고 queue discipline이 무너진다.
- 그래서 battle second stage는 `무엇이 모였는가` 다음 단계인 `누가 어떤 질문으로 review를 끝낼 것인가`까지 같은 문장으로 고정해야 한다.

## review 핵심 순서
1. **문장 정렬 확인**  
   `manifest`, `hook-manifest`, `dom-sanity`, `handoff-line`이 모두 `battle-stakes-plaque` / `current-next-grimoire` / `stakes` / `oath unlock only after battle green` 같은 뜻을 말해야 한다.
2. **stakes first-glance 확인**  
   `overview`, `stakes-focus`, `density-review`가 `current stakes와 next/grimoire가 같은 위계로 읽히는가`를 같이 증명해야 한다.
3. **starter sanity 확인**  
   `starter-asset-list`는 `Stakes Plaque Starter` 충분성 + 이번 stage 비범위를 같이 적어야 한다.
4. **QA candidate 기록**  
   위 1~3이 맞을 때만 `battle-stakes-plaque archive-ready candidate`를 쓴다.
5. **PM green / oath unlock 기록**  
   QA candidate 뒤에만 `battle-stakes-plaque green, oath-banner unlock`을 쓴다.

## 역할별 handoff 포인트

### 프로그래머
- artifact 제출이 끝이어도 green이 아니다.
- wording drift fail이 오면 battle-only 범위에서 note와 handoff-line을 다시 맞춘다.

### UI
- overview/focus는 예쁜 전투 샷이 아니라 stakes first-glance 증거다.
- density-review는 캡처 두 장을 직접 근거로 써야 한다.

### 아트
- starter note는 battle closing 근거다.
- 공통 frame, combat shell 전체 리뉴얼, oath 확장 제안은 이번 review 범위 밖이다.

### QA
- candidate는 artifact alignment + battle-only verdict가 있을 때만 쓴다.
- capture 존재만으로는 candidate를 올리지 않는다.

### PM/기획
- green은 QA candidate 뒤의 운영 문장이다.
- battle green을 `Battle Table green`처럼 크게 쓰지 않는다.

## fail이면 어떻게 보류하나
- `handoff-line`이 큰 총괄 문장으로 커지면 **candidate 보류**
- `density-review`가 overview/focus를 직접 참조하지 않으면 **candidate 보류**
- `starter-asset-list`가 backlog 확장 문서처럼 읽히면 **candidate 보류**
- PM green 문장이 `oath unlock` 없이 vague하면 **green 보류**

## 복붙용 문장
- **QA candidate:** `battle-stakes-plaque archive-ready candidate: current / next / grimoire slot hook, stakes first-glance, Stakes Plaque Starter sanity bundle 정렬 확인, oath-banner는 battle green 뒤에만 unlock.`
- **PM green:** `battle-stakes-plaque green, oath-banner unlock: battle second-stage evidence bundle과 handoff line 정렬 확인.`

## 지금 당장 다음 액션
1. `battle stakes stage packet -> battle required artifacts packet -> battle candidate review packet` 순서로 review 범위를 다시 잠근다.
2. QA는 문장 정렬부터 확인한다.
3. UI/QA는 overview/focus/density verdict를 확인한다.
4. 아트/QA는 starter verdict와 비범위를 확인한다.
5. QA candidate 뒤에만 PM green과 oath unlock을 기록한다.

## 한 장 결론
battle second stage의 마지막 공백은 `artifact가 있나`가 아니라 **`그 artifact를 누가 어떤 질문으로 읽고, 언제 candidate와 green을 각각 말하나`**였다. 이 요약은 그 review 절차를 한 화면으로 줄여, battle 턴이 다시 감상형 closing이나 큰 총괄 green 문장으로 흐르지 않게 만든다.
