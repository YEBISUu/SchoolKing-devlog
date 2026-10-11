# 실드 없는 피격 이펙트 농도 검토

2026-10-11. 사용자 요청은 실드가 없는 상태의 피격 이펙트를 더 진하게 만들 수 있는지 확인하는 것. 이번에는 조사만 수행했으며 게임 설정/소스는 수정하지 않았다.

## 확인한 현재 상태

TGS·테니스의 `ReplicatedStorage.HealthImpactTemplates`에 세 종류의 Hit1 ParticleEmitter가 있으며, 모양 텍스처/초기 크기를 제외한 아래 설정은 동일하다.

| 항목 | 현재 |
|---|---|
| Color | RGB255/175/65 → RGB255/85/35 |
| Transparency | 수명의0~65% 구간0.1, 끝에서1 |
| LightEmission / Brightness / LightInfluence | 1 / 2 / 0 |
| Lifetime | 0.5초 |
| BurstCount | 1 |
| 크기 | 종류별 약0.36/1.23/1.30에서2.7까지 확대 |
| 방향 / 텍스처 | 카메라를 향하는 애니메이션 이미지 |
| 런타임 정리 | 생성 후0.6초 |

테니스 ShieldSystem에서 health_hit일 때 세 종류 중 하나를 고르고, 양쪽 ShieldImpactVFX가 템플릿 설정을 그대로 복제/Emit한다. 같은 대상/같은 종류/가까운 지점의0.06초 이내 중복 효과는 제한한다. 실드를 깨뜨리는 타격은 shield_break, 실드가 없는 뒤의 타격은 health_hit 경로다.

## 해석과 우선 조정안 — 미적용

[Roblox 공식 설명](https://create.roblox.com/docs/effects/particle-emitters#lightemission)에 따르면 LightEmission=1은 배경 색과 더하는 방식, 0은 일반 혼합 방식이다. 따라서 현재 최대 발광과 Brightness2가 밝은 배경에서 색의 대비를 낮출 가능성이 있다. 실제 화면에서 원인을 확정한 것은 아니다.

우선 후보는 LightEmission1→0.3, Brightness2→1.2, 초기 Transparency0.1→0, 색은 더 진한 주황 RGB255/120/35 → RGB235/55/20. 현재 투명도는 이미 낮으므로 투명도만0으로 바꿔서는 차이가 작을 수 있다. 크기·수명·입자 수는 먼저 유지하고 농도/대비를 비교하는 것이 적절하다.

입자 수를 단순히 늘리면 형상이 겹치므로 우선순위가 낮다. 텍스처 자체의 알파/어두운 배경 포함 여부는 확인하지 않았으므로 발광을 낮출 때 네모난 바탕 등이 드러나지 않는지도 비교해야 한다. 필요하면 텍스처 보완이나 낮은 발광의 윤곽 레이어를 별도로 검토한다.

## 수행한 확인 / 미확인

- 두 맵의 세 템플릿(총6개) 속성과 공통 생성 코드 읽기.
- 테니스 서버의 health_hit / shield_hit / shield_break 분기 확인.
- Roblox 공식 Color/Transparency/LightEmission/Brightness 설명 대조.
- 수정, 신규 백업, Play 테스트, 실제 화면 A/B 비교, Roblox 게시 없음. 원시 속성/소스 조회 결과는 로컬에만 보관.
