# DiceSpell 고블린 테마 몬스터-스킬 연결 시트 (Unity 구현용)

## 목적

- Unity 프로그래머가 고블린 테마 몬스터를 구현할 때 바로 사용할 수 있는 데이터 명세 문서
- `누가 무슨 스킬을 쓰는지`, `어떤 조건에서 그 스킬을 우선 쓰는지`, `스킬 효과를 어떻게 분리해야 하는지`를 명확히 정의
- 현재 JS 빌드의 동작을 Unity 구조로 번역한 문서

## 권장 데이터 구조

### 1. MonsterDefinition

각 몬스터는 아래 필드를 가진다.

- `MonsterId`
- `DisplayName`
- `Theme`
- `Tier`
- `Role`
- `Element`
- `BaseHp`
- `HpGrowth`
- `BaseAttack`
- `AttackGrowth`
- `GoldReward`
- `CollapseReduceOnKill`
- `AiSkillSlots`

### 2. MonsterSkillDefinition

각 몬스터 스킬은 아래 필드를 가진다.

- `SkillId`
- `DisplayName`
- `IntentType`
- `TargetType`
- `Power`
- `AppliedStatuses`
- `Condition`
- `PriorityRule`
- `Notes`

### 3. MonsterAiSkillSlot

각 몬스터는 스킬 슬롯 목록을 가진다.

- `SkillId`
- `Weight`
- `ConditionType`
- `ConditionValue`
- `Priority`

권장 해석:

- `Priority`가 낮을수록 우선 판정
- 조건을 만족하는 스킬만 후보에 넣고
- 같은 Priority 안에서는 `Weight` 기반 랜덤 선택

---

## 공통 스킬 정의

| SkillId | DisplayName | IntentType | TargetType | 효과 | 조건 | 비고 |
| --- | --- | --- | --- | --- | --- | --- |
| `GBL_ATK_NONE` | 기본 공격 | Attack | PlayerSingle | 기본 공격 1회 | 없음 | 무속성 |
| `GBL_ATK_ICE_CHILL` | 냉기 공격 | Attack | PlayerSingle | 기본 공격 1회 + `Chill 1 / 2턴` | 없음 | 플레이어 리롤 -1 |
| `GBL_ATK_ELEC_SHOCK` | 전격 공격 | Attack | PlayerSingle | 기본 공격 1회 + `Shock 1 / 2턴` | 없음 | 플레이어 받는 피해 +20% |
| `GBL_ATK_GROUND_FRACTURE` | 대지 공격 | Attack | PlayerSingle | 기본 공격 1회 + `Fracture 1 / 2턴` | 없음 | 플레이어 받는 피해 +10%, 턴 시작 Shield -2 |
| `GBL_GUARD_6` | 방어 태세 | Guard | Self | Self Shield +6 | 없음 | JS 기준 `defend`/`guard` 통합 |
| `GBL_COLLAPSE_7` | 붕괴 유도 | Collapse | Player | 플레이어 붕괴 게이지 +7 | 없음 | 붕괴 관련 UI 재갱신 필요 |
| `GBL_RAGE_1T3` | 격노 | Buff | Self | `Rage 1 / 3턴` | 없음 | 실질 공격력 +2 |
| `GBL_HEAL_16` | 재생 | Heal | Self | Self HP +16 | `SelfHpRatio <= 0.35`일 때 우선 사용 | 조건부 우선 행동 |

---

## 상태 이상 정의

| StatusId | 이름 | 대상 | 효과 | 권장 구현 메모 |
| --- | --- | --- | --- | --- |
| `Chill` | 빙결 | Player | 리롤 -1 | 플레이어 전용 디버프 |
| `Shock` | 감전 | Player | 받는 피해 +20% | 최종 피해 배율에 반영 |
| `Fracture` | 쇄약 | Player | 받는 피해 +10%, 턴 시작 Shield -2 | 턴 시작 처리 필요 |
| `Rage` | 격노 | Monster | 공격력 +2 | monster attack 계산 시 반영 |

---

## 고블린 테마 몬스터 데이터

| MonsterId | 이름 | Tier | Role | Element | BaseHp | HpGrowth | BaseAttack | AttackGrowth | GoldReward | CollapseReduceOnKill |
| --- | --- | --- | --- | --- | ---: | ---: | ---: | ---: | ---: | ---: |
| `410003` | 고블린 스카웃 | Normal | Assault | None | 22 | 5 | 5 | 1 | 8 | 2 |
| `410004` | 노말 고블린 | Normal | Frontline | None | 26 | 6 | 6 | 2 | 10 | 3 |
| `410005` | 얼음 고블린 | Normal | Anchor | Ice | 28 | 6 | 7 | 2 | 10 | 3 |
| `410007` | 전격 고블린 | Normal | Assault | Electric | 24 | 5 | 8 | 2 | 11 | 3 |
| `410020` | 고블린 대장 | Boss | Boss | Ground | 250 | 22 | 14 | 3 | 65 | 8 |
| `410030` | 보물 고블린 왕 | FinalBoss | FinalBoss | Electric | 320 | 24 | 16 | 4 | 100 | 12 |

---

## 몬스터별 스킬 슬롯 연결

### 410003 고블린 스카웃

| Slot | SkillId | Weight | ConditionType | ConditionValue | Priority |
| --- | --- | ---: | --- | --- | ---: |
| 1 | `GBL_ATK_NONE` | 1 | None | - | 10 |
| 2 | `GBL_GUARD_6` | 1 | None | - | 10 |

의도:
- 초반 돌격형 기본 몬스터
- 복잡한 판정 없이 공격/방어 2택만 사용

### 410004 노말 고블린

| Slot | SkillId | Weight | ConditionType | ConditionValue | Priority |
| --- | --- | ---: | --- | --- | ---: |
| 1 | `GBL_ATK_NONE` | 1 | None | - | 10 |
| 2 | `GBL_GUARD_6` | 1 | None | - | 10 |

의도:
- 고블린 전열 표준형
- 스카웃보다 높은 체력과 성장치로 버티는 기본형

### 410005 얼음 고블린

| Slot | SkillId | Weight | ConditionType | ConditionValue | Priority |
| --- | --- | ---: | --- | --- | ---: |
| 1 | `GBL_ATK_ICE_CHILL` | 1 | None | - | 10 |
| 2 | `GBL_GUARD_6` | 1 | None | - | 10 |
| 3 | `GBL_COLLAPSE_7` | 1 | None | - | 10 |

의도:
- 방벽형 변주 몬스터
- 리롤 압박, 방어, 붕괴를 함께 사용

### 410007 전격 고블린

| Slot | SkillId | Weight | ConditionType | ConditionValue | Priority |
| --- | --- | ---: | --- | --- | ---: |
| 1 | `GBL_ATK_ELEC_SHOCK` | 1 | None | - | 10 |
| 2 | `GBL_COLLAPSE_7` | 1 | None | - | 10 |

의도:
- 공격 압박형 돌격 몬스터
- 감전으로 후속 피해를 키우고 붕괴도 누적

### 410020 고블린 대장

| Slot | SkillId | Weight | ConditionType | ConditionValue | Priority |
| --- | --- | ---: | --- | --- | ---: |
| 1 | `GBL_ATK_GROUND_FRACTURE` | 1 | None | - | 10 |
| 2 | `GBL_RAGE_1T3` | 1 | None | - | 10 |
| 3 | `GBL_COLLAPSE_7` | 1 | None | - | 10 |

의도:
- 중간 보스형 압박 몬스터
- 쇄약으로 피해 증폭을 걸고, 격노와 붕괴를 섞음

### 410030 보물 고블린 왕

| Slot | SkillId | Weight | ConditionType | ConditionValue | Priority |
| --- | --- | ---: | --- | --- | ---: |
| 1 | `GBL_HEAL_16` | 1 | SelfHpRatioLessOrEqual | `0.35` | 0 |
| 2 | `GBL_ATK_ELEC_SHOCK` | 1 | None | - | 10 |
| 3 | `GBL_RAGE_1T3` | 1 | None | - | 10 |
| 4 | `GBL_COLLAPSE_7` | 1 | None | - | 10 |

의도:
- 최종보스
- 평소엔 감전/격노/붕괴를 섞고
- HP 35% 이하에선 회복 스킬을 우선 사용

---

## Unity 구현 규칙

### 1. Attack 스킬 처리

- 공격 스킬은 `BaseAttack` 자체를 직접 들고 있지 않아도 된다.
- 실제 피해는 몬스터 인스턴스의 현재 공격력에서 계산한다.
- 권장 구조:
  - `MonsterRuntime.AttackPower`
  - `SkillDefinition.DamageMode = UseMonsterAttack`
  - `SkillDefinition.FlatBonusDamage = 0`

예외:
- 현재 고블린 테마에는 `burn intent` 기반 `공격력 +2` 변형이 없다.
- 따라서 고블린은 전부 기본 공격력 기준으로 처리하면 된다.

### 2. Guard 스킬 처리

- `GBL_GUARD_6`은 항상 Self Shield +6
- `defend`와 `guard`는 Unity 쪽에선 하나의 스킬로 통합 권장

### 3. Collapse 스킬 처리

- `GBL_COLLAPSE_7`은 플레이어 붕괴 게이지 +7
- UI와 전투 상태 표시가 즉시 갱신되어야 함

### 4. Buff 스킬 처리

- `GBL_RAGE_1T3`은 `Rage stacks=1 duration=3`
- Rage는 monster attack 계산 시 `+2 * stacks`

### 5. Heal 스킬 처리

- `GBL_HEAL_16`은 Self HP +16
- 단, 보물 고블린 왕은 `SelfHpRatio <= 0.35`에서 Priority 0으로 먼저 판정

---

## 스폰 수치 계산식

현재 빌드 기준 의사코드:

```csharp
hpScale =
  role == Normal ? floor((page - 1) / 2) :
  role == Boss ? max(2, floor(page * 0.55)) :
  max(4, floor(page * 0.65));

attackScale =
  role == Normal ? floor((page - 1) / 3) :
  floor(page / 2);

rawHp = BaseHp + hpScale * HpGrowth + grimoireAddMonsterHp;
rawAttack = BaseAttack + attackScale * AttackGrowth + grimoireAddMonsterAttack;
```

이후 role/page 구간 multiplier와 overrun multiplier를 곱한다.

실무 권장:
- Unity에선 이 계산을 `MonsterSpawnCalculator` 같은 별도 모듈로 분리
- `MonsterDefinition`은 base 데이터만 들고 있게 유지

---

## 구현 시 수정/주의 포인트

### JS 문서에서 바꿔야 하는 부분

- `intent=attack`, `element=Ice` 같은 표현은 Unity 구현 문서에선 직접 사용하지 않는 편이 좋다.
- Unity 문서에서는 반드시 `SkillId` 단위로 명시한다.

### 추천 방식

- JS 해석 기반 문서:
  - 현재 동작 복원용
- Unity 구현 문서:
  - `MonsterDefinition`
  - `MonsterSkillDefinition`
  - `MonsterAiSkillSlot`
  - `StatusEffectDefinition`
  - `MonsterSpawnCalculator`

즉, Unity 프로그래머는 이 문서를 그대로 보고 ScriptableObject 혹은 DataTable을 설계하면 된다.

---

## 최종 한 줄 연결 요약

- `410003 고블린 스카웃` -> 기본 공격 / 방어
- `410004 노말 고블린` -> 기본 공격 / 방어
- `410005 얼음 고블린` -> 냉기 공격 / 방어 / 붕괴
- `410007 전격 고블린` -> 전격 공격 / 붕괴
- `410020 고블린 대장` -> 대지 공격 / 격노 / 붕괴
- `410030 보물 고블린 왕` -> 전격 공격 / 격노 / 붕괴 / 조건부 회복
