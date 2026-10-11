# 처치 시 날아감 → 착지 분해 연출

> **후속 상태: 롤백됨.** 사용자 요청으로 이 문서의 시체 비행·착지 분해 기능은 TGS·테니스 활성 코드에서 제거했습니다. 현재 설정은 [롤백 기록](2026-10-11-death-flight-rollback.md)과 [CURRENT](../CURRENT.md)를 확인하세요. 아래는 당시 구현·검증 이력입니다.

2026-10-11 · TGS·테니스 Studio 편집본 적용. Roblox 게시는 수행하지 않았다.

## 동작

사망이 확정된 캐릭터가 공격 방향으로 떠오르며 날아가고, 하강 중 첫 바닥 접촉에서 몸통·팔다리와 작은 조각이 한 번 흩어진다. 조각은2.5초 후 정리된다. 살아 있는 캐릭터의 피해·이동·점수에는 새 힘을 적용하지 않는다.

- 플레이어·Bots·Dummies에 공통 사망 연결. 기존 즉시 분해는 기능 비활성 또는 신선한 공격 정보가 없는 사망의 대체 경로로 유지.
- 일반 처치보다 스나이퍼, 강화 스나이퍼 순서로 더 강하게 날아간다.
- 강화 여부는 탄 소모 전 캡처한 실제 발사 값으로 전달한다. 수류탄이 터질 때 손에 든 스나이퍼 때문에 강화 비행으로 오인하지 않는다.
- 벽 접촉에서는 벽 방향 속도를 제거·감쇠하며, 바닥으로 판단되는 표면에 하강 중 닿아야 분해한다.
- 테니스 기존3초 슬로모션 곡선을 비행과 조각에도 적용한다. 기존 시체 감속 서비스는 새 비행 처리와 중복 적용하지 않는다. TGS에는 슬로모션을 추가하지 않았다.
- 구형 Motor6D와 현재 R15의 AnimationConstraint를 모두 래그돌 관절로 변환한다.
- 몸체·조각의 충돌/터치/총알 조회를 끄고 별도의 환경 캐스트로 벽·바닥을 감지한다. 연출용 시체가 플레이어나 총알을 가로막지 않도록 구성했다.

## 조절 위치와 최종 설정

Studio의 **ReplicatedStorage → DeathLaunchConfig** ModuleScript에서 변경한다. 변경 후 새 Play 세션에서 확인한다.

| 항목 | 필드 | 값 |
|---|---|---|
| 전체 사용 | Enabled | true; false면 기존 즉시 분해 경로 |
| 일반 수평/상승 속도 | NormalSpeed / NormalLift | 62 / 38 |
| 스나이퍼 수평/상승 속도 | SniperSpeed / SniperLift | 82 / 44 |
| 강화 스나이퍼 수평/상승 속도 | EmpoweredSpeed / EmpoweredLift | 100 / 50 |
| 초기 합성 속도 상한 | MaxSpeed | 110 |
| 수평 이동 제한 기준 | MaxDistance | 72 studs; 도달 시 수평 속도 제거, 프레임 오차 가능 |
| 기존 이동 속도 반영 | InheritVelocity / MaxInheritedSpeed | 0.15 / 12 |
| 시체 중력 비율/회전 | GravityScale / Spin | 0.65 / 4 |
| 벽 검사 반경/바닥 검사 길이 | CollisionRadius / GroundReach | 1.15 / 2.7 |
| 바닥 법선 Y 기준/최소 비행시간 | GroundNormalY / MinFlightTime | 0.55 / 0.12 시뮬레이션초 |
| 벽 접촉 후 속도 비율 | WallRetention | 0.35 |
| 비행 정리 제한 | MaxFlightLifetime | 실제8초 |
| 착지 분해 | ShatterOnLanding | true; false면 착지 시 바로 정리 |
| 조각 수평/상승 속도 | ShardSpeed / ShardLift | 22 / 18 |
| 추가 작은 조각 | ExtraShards | 12개, 코드 상한24개 |
| 조각 수명 | ShardLifetime | 실제2.5초; 후반1.25초 페이드 |
| 동시 관리 몸체 | MaxActiveBodies | 10개; 초과 시 가장 오래된 것 정리 |

속도 단위는 studs/second다. 실제 거리는 높이·충돌·기존 이동·프레임에 따라 달라진다.

## 연결과 변경 범위

공통 새 모듈: ReplicatedStorage.DeathLaunchConfig, ServerScriptService.DeathLaunch.
공통 수정: DeathShatter, ShieldSystem, StarterPack.Sniper.ServerHandler.
테니스 추가 수정: TennisKillSlowMotionService.

ShieldSystem은 실제 HP 피해를 주기 직전에 공격 방향 정보를 기록한다. Humanoid.Died 이후에만 비행을 시작하며, 같은 몸체의 중복 시작과 중복 착지 분해를 막는다. 사망 당시 원본 캐릭터에만 적용하고, 새로 부활한 캐릭터에는 옛 비행 상태를 전달하지 않는다. HP·탄약·처치 판정 공식은 유지한다.

## 실제 수행한 검증

- 분리12개 검사: 방향/상승, 일반·스나이퍼·강화 강도 순서, 속도·이동 관성 상한, 잘못된 벡터, 생존 상태 거절, 공격 정보 없는 사망 거절, 비활성, 수류탄/강화탄 메타데이터 구분.
- 테니스 Play에서 격리 R6 시험5종: 일반·스나이퍼·강화탄·벽·슬로모션. 모두 첫 착지에서1회 분해, 이후 파트0개로 정리.
- 같은 평지 시험의 측정값(근삿값):

| 시나리오 | 착지까지 수평 거리 | 최고 상승 높이 | 착지 실제 시간 |
|---|---:|---:|---:|
| 일반 | 37.1 | 5.5 | 0.59초 |
| 스나이퍼 | 57.1 | 7.5 | 0.66초 |
| 강화 스나이퍼 | 72.7 | 9.3 | 0.77초 |
| 앞쪽 벽 | 9.7 | 4.5 | 0.49초 |
| 테니스 슬로모션 일반 | 37.9 | 5.6 | 1.81초 |

- 현재 R15 시험: 래그돌 관절14개, 중력 처리15개 어셈블리, 충돌/조회가 켜진 파트0개, 착지 분해1회, 최종 파트0개.
- **테니스와 TGS 양쪽 실제 서버 Script**에서 자동 Humanoid.Died 연결 확인: 관절 보존 준비 → 자동 비행 → R15 관절14개 → 착지 분해1회.
- 최초 R15 시험에서 새 AnimationConstraint 미처리와 사망 Humanoid의 충돌 재설정을 발견했다. 관절 호환 및 사망 후 상태 기계 비활성 처리 후 재검증 통과.
- 도구의 단독 모듈 호출에서는 자동 사망 연결 시험이 실패했다. 실행 컨텍스트의 모듈 상태 분리가 원인으로 추정되어, 실제 서버 Script 안에서 공격 기록과 사망을 함께 실행하는 방식으로 재검증했고 양쪽 자동 연결을 확인했다.
- 최종11개 소스 컴파일·적용 내용 및 수정 전7개 소스 백업을 대조했다. 마지막 메타데이터 보완2소스는 컴파일/적용 재확인 및 분리2개 검사 통과.
- 시험용 캐릭터·바닥·서버 Script를 제거하고 양쪽 Studio를 Edit 모드로 돌렸다. 실제 플레이어에게 가짜 명중·처치 패킷을 보내지 않았다.

미확인: 실제 사용자 발사/온라인 두 계정 관찰, 모바일, 경사·계단·낮은 천장·고공 낙하의 전체 조합, 캐릭터 외형별 시각적 품질, 다수 동시 처치의 성능/체감. 시험 중 기존 Workspace NPC 스크립트 오류와 애셋 권한 경고도 관찰했으나 이번 범위에서 수정하지 않았다. 로그 전체가 깨끗하다고 보고하지 않는다.

## 복구

수정 전에 각 맵 ServerStorage.DeathLaunchLandingBackup_20261011과 로컬 outputs에 원본을 보관했다. 백업은 테니스4소스/TGS3소스이며 새로 추가한 모듈2개를 별도로 기록했다.

- 빠르게 끄기: DeathLaunchConfig.Enabled=false 후 새 Play.
- 완전 원복: Edit 모드에서 보관된 원본을 함께 복구한 뒤 새 DeathLaunch/DeathLaunchConfig 모듈을 제거.
- 로컬 death-launch-landing-shatter-20261011에 before/after, 설정/모듈, 시험 및 결과, rollback-studio와 맵별 자체 포함 restore-from-local 스크립트 보관.
- 공개 저장소에는 이 개발 문서만 반영한다. 게임 소스·원시 로그·백업은 올리지 않는다.

## 확인한 Roblox API 자료

[BasePart.CanQuery](https://create.roblox.com/docs/reference/engine/classes/BasePart#CanQuery), [AnimationConstraint](https://create.roblox.com/docs/reference/engine/classes/AnimationConstraint), [Humanoid.EvaluateStateMachine](https://create.roblox.com/docs/reference/engine/classes/Humanoid#EvaluateStateMachine). 충돌/조회 분리와 현재 캐릭터 관절 구조를 확인하는 데 사용했다.
