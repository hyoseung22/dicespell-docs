# DiceSpell 고블린 테마 몬스터-스킬 연결 시트

## 목적

- 컨플루언스 반영용
- 프로그래머가 `고블린 테마에서 누가 무슨 스킬을 써야 하는지` 바로 연결할 수 있게 만드는 문서
- 현재 빌드의 `MONSTER_LIBRARY.goblin`과 `game-engine` intent 처리 규칙을 프로그래머용 시트 형태로 재정리

## 전제

- 현재 빌드는 몬스터용 별도 스킬 데이터 테이블이 없다.
- 실제 연결은 `monster.intents[] + monster.element + 공통 행동 로직`으로 구성된다.
- 아래 `문서용 Skill ID`는 컨플루언스/개발 소통용 권장 식별자다.
  - 즉, 현재 코드에 이미 존재하는 ID가 아니라 `누가 어떤 효과를 써야 하는지` 명확히 말하기 위한 문서용 이름이다.
- 이 문서는 `현재 JS 런타임 해석본`이다. Unity 구현 전달용 구조는 `goblin_theme_skill_assignment_unity_kr.md`를 기준으로 본다.

## 공통 스킬 카탈로그

| 문서용 Skill ID | 현재 런타임 연결 | 효과 | 비고 |
| --- | --- | --- | --- |
| `GBL_ATK_NONE` | `intent=attack`, `element=None` | 기본 공격 1회 | 상태 이상 없음 |
| `GBL_ATK_ICE_CHILL` | `intent=attack`, `element=Ice` | 기본 공격 1회 + `chill 1 / 2턴` | 플레이어 리롤 -1 |
| `GBL_ATK_ELEC_SHOCK` | `intent=attack`, `element=Electric` | 기본 공격 1회 + `shock 1 / 2턴` | 플레이어 받는 피해 +20% |
| `GBL_ATK_GROUND_FRACTURE` | `intent=attack`, `element=Ground` | 기본 공격 1회 + `fracture 1 / 2턴` | 플레이어 받는 피해 +10%, 턴 시작 Shield -2 |
| `GBL_GUARD_6` | `intent=defend` 또는 `intent=guard` | 자기 Shield +6 | `defend`와 `guard`는 현재 런타임에서 동일 처리 |
| `GBL_COLLAPSE_7` | `intent=collapse` | 플레이어 붕괴 게이지 +7 | 몬스터 intent 재계산 발생 |
| `GBL_RAGE_1T3` | `intent=buff` | 자신에게 `rage 1 / 3턴` | 실질 공격력 +2 |
| `GBL_HEAL_16` | `intent=heal` | 자신 HP +16 | HP 35% 이하 시 heal intent 보유 몬스터는 heal 우선 선택 |

## 몬스터 데이터 + 스킬 연결표

| 몬스터 ID | 이름 | 구분 | 역할 | 속성 | Base HP | HP 성장 | Base ATK | ATK 성장 | Gold | 처치 시 붕괴 감소 | 연결 스킬 | 패턴 가중치 / 조건 |
| --- | --- | --- | --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | --- | --- |
| `410003` | 고블린 스카웃 | Normal | 돌격 | None | 22 | 5 | 5 | 1 | 8 | 2 | `GBL_ATK_NONE`, `GBL_GUARD_6` | `attack:1`, `guard:1` |
| `410004` | 노말 고블린 | Normal | 전열 | None | 26 | 6 | 6 | 2 | 10 | 3 | `GBL_ATK_NONE`, `GBL_GUARD_6` | `attack:1`, `guard:1` |
| `410005` | 얼음 고블린 | Normal | 방벽 | Ice | 28 | 6 | 7 | 2 | 10 | 3 | `GBL_ATK_ICE_CHILL`, `GBL_GUARD_6`, `GBL_COLLAPSE_7` | `attack:1`, `guard:1`, `collapse:1` |
| `410007` | 전격 고블린 | Normal | 돌격 | Electric | 24 | 5 | 8 | 2 | 11 | 3 | `GBL_ATK_ELEC_SHOCK`, `GBL_COLLAPSE_7` | `attack:1`, `collapse:1` |
| `410020` | 고블린 대장 | Boss | 보스 | Ground | 250 | 22 | 14 | 3 | 65 | 8 | `GBL_ATK_GROUND_FRACTURE`, `GBL_RAGE_1T3`, `GBL_COLLAPSE_7` | `attack:1`, `buff:1`, `collapse:1` |
| `410030` | 보물 고블린 왕 | Final Boss | 최종보스 | Electric | 320 | 24 | 16 | 4 | 100 | 12 | `GBL_ATK_ELEC_SHOCK`, `GBL_RAGE_1T3`, `GBL_COLLAPSE_7`, `GBL_HEAL_16` | 기본 `attack:1`, `buff:1`, `collapse:1`, `heal:1`; 단, HP 35% 이하 시 heal 우선 |

## 프로그래머 연결 규칙

### 1. 현재 구조 그대로 구현할 경우

- `몬스터별 스킬 연결`은 별도 스킬 테이블을 새로 만들지 않아도 된다.
- 현재 런타임 기준 최소 연결은 아래와 같다.
  1. 몬스터 템플릿에 `intents[]`를 넣는다.
  2. 공격 계열 추가 효과는 `element`로 연결한다.
  3. 보스 강화는 `buff -> rage 1 / 3턴` 공통 처리로 연결한다.
  4. 회복은 `heal -> HP +16` 공통 처리로 연결한다.

### 2. 별도 스킬 테이블로 분리하고 싶다면

- 고블린 테마는 아래 8개만 공통 스킬 asset으로 만들면 충분하다.
  - `GBL_ATK_NONE`
  - `GBL_ATK_ICE_CHILL`
  - `GBL_ATK_ELEC_SHOCK`
  - `GBL_ATK_GROUND_FRACTURE`
  - `GBL_GUARD_6`
  - `GBL_COLLAPSE_7`
  - `GBL_RAGE_1T3`
  - `GBL_HEAL_16`
- 이후 각 몬스터는 위 스킬 ID 목록만 참조하게 만들면 된다.

## 몬스터별 한 줄 지시

- `410003 고블린 스카웃`: 기본 공격 + 방어만 쓰는 초반 돌격형
- `410004 노말 고블린`: 기본 공격 + 방어만 쓰는 표준 전열형
- `410005 얼음 고블린`: 냉기 공격 + 방어 + 붕괴를 균등하게 섞는 방벽형
- `410007 전격 고블린`: 전격 공격 + 붕괴를 섞는 공격 압박형
- `410020 고블린 대장`: 대지 공격 + 격노 + 붕괴를 쓰는 중간 보스형
- `410030 보물 고블린 왕`: 전격 공격 + 격노 + 붕괴 + 회복을 쓰며, 저체력에서는 회복 우선

## 구현 메모

- `defend`와 `guard`는 현재 엔진에서 같은 행동이다. 문서에서는 하나의 `GBL_GUARD_6`로 통합해도 무방하다.
- 보물 고블린 왕의 `heal`만 조건부 우선 행동이다. 나머지 고블린은 intent 배열 빈도대로만 랜덤 선택된다.
- 현재 고블린 테마에는 별도 passive field 기반 일반 몬스터가 없다. 즉 다른 테마 리치 몬스터들처럼 고유 패시브를 따로 구현할 필요는 없다.

## 소스 기준

- `/Users/jeonghyoseung/Documents/codex/dicespell-standalone/dice-spell-standalone/src/data.js`
  - `MONSTER_LIBRARY.goblin`
- `/Users/jeonghyoseung/Documents/codex/dicespell-standalone/dice-spell-standalone/src/game-engine.js`
  - `determineIntent`
  - `monsterAct`
  - `ELEMENT_STATUS_MAP`
  - `createMonster`
