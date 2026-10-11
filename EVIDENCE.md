# 검증 근거

[처음으로](README.md)

초판은 2026-10-07, 최근 대조는 2026-10-11입니다. [전체 자료 점검표](AUDIT.md)와 [최신 상세](updates/2026-10-10-11.md)에 후속 근거/백업 식별자/검증 범위를 추가했습니다. 아래는 보관 자료의 상대 식별자입니다. **원시 파일은 이 공개 저장소에 포함하지 않습니다.**

| 자료 | 확인 내용 / 한계 |
|---|---|
| tennis-duel/README.md | 1대1 Studio2클라이언트. 초기 더미 설명은 후속 CleanUp으로 대체 |
| dustpan-lock-20261003/README.md | 양쪽 적용, 테니스 이동 대상 실입력 피해1회/접근1회 |
| dustpan-range-9-20261003/verification.json | 클라이언트 범위8→9, 서버/보조 유지 |
| tgs-tennis-common-20261004/source-verification.json | TGS 공통 이식, 코어260개 유지 |
| tgs-tennis-common-20261004/verification-final.json | 슬라이딩20+FOV36+멘틀음성29+벽점프음성36+속도선32+기타19=172. 분리 검사 |
| tennis-cleanup-20261004/verification.json | 규칙25/생성21, 4무기, HP100/SH150, 실기 관찰 |
| switch-fix-20261004/verification.json | 12컴파일, 클라이언트30/서버34. Play 미실행 |
| tennis-teleport-20261004/final-verification.json | 안전 복구 확인, 최초 원인 미재현 |
| crowd-audio-20261004/verification.json | 음량/페이드 읽기·컴파일, Play 미시작 |
| tennis-kill-celebration-20261004/README.txt | 킬 이벤트, B/C 배치, 편집 방향 표시 |
| firework-burst-speed2-20261004/verification.json | 크기0.5/속도2/복사 속성 |
| firecracker-audio-20261004/verification.json | 음량0.2, 타이밍 확인. 실제 청취 아님 |
| lobby-integration-20261004/STATUS.txt | 별도 테스트 게임/원본 유지, 앱 AI입장 관찰 |
| lobby-return-loading-20261004/STATUS.txt | Studio 종료2회, 실기 이동 재검증 별도 |
| lobby-party-20261004/STATUS.txt | 파티/관전, 조정기20/이동8, 실제 다계정 서버 이동 미실행 |
| lobby-party-20261004/party-button-position-fix.txt | 파티UI 위치 변경 |
| studio-recovery-20261005/verification.json 및 RECOVERY.txt | 백업/스크립트 일치. 클라우드 저장 증명 아님 |

## 충돌하는 기록 처리

1. 날짜가 늦고 적용 대상이 일치하는 읽기/검증 기록 우선.
2. 사용자 요청값과 실제 확인값 구분.
3. 사본 수정을 원본 적용으로 추정하지 않기.
4. 소스 일치/컴파일/분리 검사/Studio/실제 앱/게시를 각각 기록.
