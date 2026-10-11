# 시체 비행 연출 롤백 — 2026-10-11

[현재 설정](../CURRENT.md) · [변경 기록](../CHANGELOG.md)

## 최종 요청과 적용 결과

사용자가 시체가 날아가는 연출을 제거하고 추가하기 전 상태로 돌아가도록 요청했다. TGS와 테니스의 시체 비행·착지 분해를 원복했다. 테니스의 후속 시험 변경인 최대2초/착지 즉시 슬로모션 해제도 함께 원복했다.

현재 처치 연출은 기존 DeathShatter의 즉시 분해 방식이다. 비행 거리/무기별 발사 강도/바닥 착지 대기/추가 파편 기능은 활성화되어 있지 않다.

## 원복 범위

- TGS·테니스: `ServerScriptService.DeathShatter`, `ServerScriptService.ShieldSystem`, `StarterPack.Sniper.ServerHandler`를 비행 추가 직전 백업으로 복구.
- 테니스: `ServerScriptService.TennisKillSlowMotionService`를 비행 추가 직전으로 복구.
- 테니스: `ReplicatedStorage.TennisDuelConfig`, `ReplicatedStorage.TennisKillSlowMotion`를2초 시험 변경 전으로 복구.
- 양쪽 활성 위치에서 `ServerScriptService.DeathLaunch`, `ReplicatedStorage.DeathLaunchConfig` 제거.
- 기존 피격 색상, 머리 이펙트 빨강/일반 헤드샷 숫자 노랑/막타 숫자 빨강 변경은 복구 기준에 이미 포함되어 있다.

## 테니스 최종 설정

| 항목 | 값 |
|---|---|
| KillSlowMotionEnabled | true |
| KillSlowMotionDuration | 3초 |
| KillSlowMotionScale | 0.1 |
| KillSlowMotionHold | 0 |
| ResultCameraDelay | 3초 |
| RoundResultSeconds | 6.5초 |

확인된 처치 후10% 속도에서3초에 걸쳐 정상 속도로 돌아오는 기존 흐름이다. 착지 신호로 조기 해제하는 변경은 제거했다. TGS에는 처치 슬로모션을 추가하지 않았다.

## 실제 검증

- 적용 직전 활성 소스가 방금까지 작업한 버전과 일치하는지 확인했다.
- Studio ServerStorage와 로컬 outputs에 원복 직전13개 소스를 별도로 백업했다.
- 복구한 기존 소스9개(TGS3개/테니스6개)의 백업 원본 일치와 Luau 컴파일을 확인했다.
- 양쪽 추가 모듈 총4개가 활성 위치에서 제거되었음을 확인했다.
- 활성 서비스의 소스 검색에서 DeathLaunch 및 TennisKillSlowEnded 연결이0건인 것을 확인했다.
- 테니스 설정을 다시 읽어3초/0.1/결과 카메라3초/결과6.5초를 확인했다.

2초/착지 해제 버전의 중간 분리·물리 검사는 최종 원복 버전의 새 플레이 검증으로 간주하지 않는다. 원복 후 새 Play, 실제 다인전 체감은 아직 확인하지 않았다.

## 복구와 게시 상태

원복 전 비행 버전은 양쪽 `ServerStorage.DeathFlightRollbackBackup_20261011`과 로컬 복구 파일에 보관했다. 비행 추가 직전 백업도 유지했다.

변경은 Studio 편집본에 적용했다. Roblox 게시는 수행하지 않았으며 사용자가 진행한다. 공개 저장소에는 개발 문서만 기록했다.
