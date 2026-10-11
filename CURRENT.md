# 현재 설정과 편집 위치

[처음으로](README.md) · [최신 변경 상세](updates/2026-10-10-11.md)

2026-10-11 Studio 편집본과 최종 작업 기록 기준. 서비스 게시본의 상태를 보증하지 않는다. 이전 CHANGELOG는 당시 값이며 아래 최신 요약을 우선한다.

## 그래플링 훅

| 설정 | 값 |
|---|---|
| 슬롯/입력 | 투척 슬롯, G 장비, 왼클릭 발사/해제 |
| 사거리/쿨다운 | 85 studs / 연결 종료 후20초 |
| 최대 연결 시간/속도 | 6초 / 95 studs/s |
| 당김/감기/조향 | 175 / 38 / 65 |
| 연결 중 중력 | 54 |
| 수동 점프 보정 | 일반32, 지면56 |
| 자동 지상 도약 | 상승속도 최소22, 아래 당김 완화0.22초 |
| 초기 당김 증가 | 0.15초 |
| 시선 자동 해제 | 100도 초과를0.08초 유지, 연결 초기0.12초 유예 |
| 쿨다운 초기화 | 부활·라운드 종료 |
| 조준 UI | 훅을 투척 슬롯에 선택하면 다른 총을 들어도 표시, 쿨다운 중 숨김 |
| 조준색 | 가능: 민트 / 불가: 회색 |

편집: `ReplicatedStorage.GrapplePhysics`, `StarterPlayerScripts.GrappleClient`. 해제 후 전진/하강 연계는 GravityTrialController와 SlideScript에서도 관리한다. Apex 엔진 상수를 복제한 값이 아니다.

## 스나이퍼

| 설정 | 값 |
|---|---|
| 탄창/예비 | 5 / 30 |
| 몸통·사지/머리 | 75 / 90 |
| 발사 간격/재장전 | 1.2초 / 2.5초 |
| 사거리 | 1000 studs |
| 서버 보조 판정 반경 | 0.1 studs |
| 머리 보너스 | 중심선이 동일 Head에 명중했을 때 |
| 스코프 | 우클릭 토글, 진입 대기0.15초, 휠1~4배; 첫1배, 이후 같은 접속 내 마지막 배율 기억 |
| 수직/수평 반동 | 5.5도 / 0.45도, 조준 배율0.8 → 수직4.4도 |
| 수직 복귀 | 상승0.06초 + 유지0.04초 + 복귀0.55초, 아래 입력과 중복 복귀 방지. [구현 상세](updates/2026-10-11-sniper-scope-recovery.md) |
| 명중음 | 기존 실드/체력 음원, 스나이퍼 최소 볼륨0.9; 실드 파괴에도 명중음+환호, 로컬 발사/볼트음 잠깐 감쇠. [상세](updates/2026-10-11-sniper-hit-sound.md) |
| 장착 | 0.85초; 장착 중 재장전/줌 입력을 유지하고 준비 후 실행 |
| 입력 대기 | 줌 토글 재클릭 취소, 재장전 중 요청한 줌은 완료 후 실행. 그래플 복귀의 짧은 우클릭 유지. [상세](updates/2026-10-11-sniper-input-buffer.md) |
| 강화탄 | R0.4초 유지 후3초 합치기, 2/3/4/5발→100/125/150/175 기본 피해 |
| 관통 | 캐릭터 관통, 대상당1회, 벽에서 중단, 추가 관통 감쇠 없음 |

편집: `GunSystem.GunConfig.Sniper`, `StarterPack.Sniper`, `GunSystem.SniperScope`, `GunSystem.SniperTrace`, `GunSystem.Recoil`, `StarterPlayerScripts.CustomFPCamera`. 기존 목발과 별도 무기. 휠 소리는 실제 배율 변경 시 재생하고 입력 중단 후0.12초에 정지.

## 이미지 교체

| 대상 | 최종 ImageId | 참조 파일 |
|---|---|---|
| Compass | 127243424545779 | compass-flat-v4.png |
| LegCrutch | 132303350204515 | legcrutch-flat-v1.png |
| SiliconGun | 126197140100099 | silicongun-flat-v1.png |
| Toaster | 134401985026824 | toaster-flat-v1.png |
| CAN | 129816693229974 | can-flat-v1.png |
| Cup | 102141275077266 | cup-flat-v1.png |
| 회복 책 | 131119176651222 | book-flat-v2.png |

무기/투척 이미지: `ReplicatedStorage.WeaponPresentation.<이름>`의 `ImageId` 속성, `rbxassetid://숫자`. 비어 있으면 기존 3D 대체 표시. Sniper/GrapplingHook 이미지와 사용 영상은 미등록. 책은 `StarterGui.GameHUD.HealBox.ItemImage.Image` 직접 편집. 생성 PNG 원본과 프롬프트는 로컬 weapon-icons 보관.

## HUD 배치

하단 공통 패널: `GameHUD.StudentIDCard`(기존 이름의 체력 패널), `WeaponBox`, `HealBox`, `ThrowBox`.
- 검정 반투명 배경: RGB12/14/18, BackgroundTransparency0.35.
- 두 맵 데스크톱 DesktopHUDScale1.65. Position/Size 수정 가능, 모바일은 별도 배치.
- WeaponBox 폭300/높이88 기준, AmmoMag 위치198/1, 크기82/44. AmmoReserve 위치198/45, 크기82/22.
- ShieldSegments: SegmentValue25, ReferenceSegments4, SegmentGap4. 최대 실드 변화 시 칸 수 조정. 숫자 숨김.
- 회복 개수는 Player.BookCount, 중앙 탄약은 서버 AmmoUpdateEvent 기반 표시. 실제 탄 소비/회복 판정과 별도.
- 중앙 탄약 아크는 토스터 기본 반경으로 공통 배치하며 스나이퍼 스코프 중에도 표시·갱신. 스코프 마스크 DisplayOrder=-1로 체력·실드·아이템·장비 HUD 아래에 배치하고, 일반 조준점만 숨긴다. [상세](updates/2026-10-11-sniper-scope-hud.md).

목발은 자동만 허용하며 B 전환 없음. 목발/컴퍼스 전용 근접 공격 비활성. 컴퍼스 기본3점사는 유지.

## 이동 표적 판정 보정 — 시험 적용

TGS·테니스 다섯 총기 공통. ReplicatedStorage.GunSystem의 LagCompensationEnabled=true, LagCompensationViewDelay=0.05. 최대 되감기0.2초, 기록0.35초/약30Hz. 플레이어만 과거 위치를 검사하고 벽·NPC는 현재 위치. false로 끄면 기존 판정으로 복귀한다. 실제2인 체감 미검증.

ServerScriptService.HitRegistrationHistory 및 각 무기의 ClientHandler/ServerHandler에서 연결한다. ServerStorage.HitRegistrationBackup_20261011에 검증된 전체 원복 자료 보관. [구현 범위·원복 절차·검증](updates/2026-10-11-hit-registration.md).

## 테니스 처치 슬로모션

`ReplicatedStorage.TennisDuelConfig`: KillSlowMotionEnabled=true, Duration=0.65초, Scale=0.4, Hold=0.30초(정확한 필드명은 모두 KillSlowMotion 접두어). 남은0.35초는 정상 속도로 부드럽게 복귀한다.

확인된 처치에만 참가자 이동/애니메이션 및 사망 몸체 속도·낙하 적용. 생존자는 동시 사망 판정 구간 종료 후 적용하며, 결과 카메라 전환·점수·타이머·월드 중력·명중음은 유지한다. TGS 미적용. [검증·복구·적용 범위](updates/2026-10-11-tennis-kill-slow-motion.md).
