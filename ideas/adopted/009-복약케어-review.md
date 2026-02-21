---
id: "009"
name: "복약케어"
type: "review"
verdict: "pass"
final_decision: "adopted"
critical_count: 0
major_count: 0
minor_count: 0
date: "2026-02-02"
---
# 009-복약케어 PRD 정합성 판별 보고서

> **검증 일자**: 2026-02-02
> **대상 문서**: `ideas/009-복약케어-prd.md` (v1.2)
> **참조 문서**: `ideas/009-복약케어.md`, `research/competitors/009-경쟁분석.md`, `research/market-data/009-시장분석.md`, `research/market-data/009-트렌드메모.md`
> **비고**: v1.1 리뷰에서 Major 1건 + Minor 2건 → v1.2에서 반영 완료 확인. v1.2 재검증에서 미달 사항 없음.

---

## v1.1 미달 3건 수정 반영 확인

| # | 미달 사항 | 심각도 | v1.2 수정 내용 | 검증 결과 |
|---|-----------|--------|---------------|-----------|
| 1 | snooze 후 미복용 전환 로직 누락 | Major | `resolve_expired_snoozes()` RPC 신규 추가 — snooze_count < 2 → pending 복귀, >= 2 → missed 전환. check-missed-dose Edge Function에서 매분 resolve → get_missed_doses 순서 호출. force_missed 건 가족 알림 발송 + mark_doses_missed 호출. 전체 흐름(snooze → pending 복귀/missed 전환 → 가족 알림) 명세 완성. | ✅ 해소 |
| 2 | pg_cron 타임존 미명시 | Minor | `cron.schedule_in_timezone('Asia/Seoul')` 적용. `(now() AT TIME ZONE 'Asia/Seoul')::DATE`로 KST 날짜 정확 계산. 매분 cron은 타임존 무관 명시. | ✅ 해소 |
| 3 | 무료/유료 가족 인원 제한 DB 정합성 | Minor | accept_invitation RPC에 owner 구독 상태 기반 이중 방어 추가 (v_effective_max: 무료 2명, 프리미엄 max_members). generate-invite와 합쳐 2중 차단. PREMIUM_REQUIRED / FAMILY_FULL 에러 구분. families 테이블 주석 보강. | ✅ 해소 |

---

## A. 사업성 검증 — 통과

### A-1. 시장 규모·경쟁분석 일치성
- PRD §1, §2의 시장 수치(65세+ 1,084만 명, 만성질환 유병률 80%+, 다제약물 OR 1.52/3.17)가 시장분석 §1, §6과 정확히 일치
- PRD §9의 경쟁자별 강약점이 경쟁분석 문서의 세부 데이터(파프리카케어 23만/평점 3.6, Medisafe 700만/유료 전환, 네이버 3개월간 가족 연동 미추가)와 일관
- 트렌드메모의 DataLab 검색 트렌드(2025.8 2.2 → 11월 84.9 → 2026.1 32.9)가 PRD에 반영됨

### A-2. 차별점 방어 가능성
- "한국어 + 가족 원격 모니터링" 조합의 국내 부재는 경쟁분석에서 검증됨
- 기술적 해자가 낮음(기술 장벽 "낮음", 네트워크 효과 "낮음", 전환 비용 "낮음")을 PRD §9에서 인정하고, "빠른 선점 + 어르신 UX 깊이"로 대응하는 전략을 명시 — 현실적 판단
- 카카오톡 대체재 위협(경쟁분석: 위협도 "높음")에 대해 자동화·기록·대시보드로 차별화하는 전략을 명시

### A-3. 수익 모델
- PRD §10에서 월 1,900원 / 연 19,000원으로 구체화 (아이디어 문서의 3,900원에서 시장분석 반영 하향 조정)
- 시장분석 §4의 똑닥 사례(월 1,000원, 20% 전환율)와 Medisafe($4.99/월) 대비 포지셔닝 근거 제시
- 수익 예측(MAU 1만/전환율 5% → 월 95만 원)이 보수적이고 현실적

### A-4. TAM/SAM/SOM 논거
- TAM: 65세+ 1,084만 × 만성질환 80% + 관리 자녀 → 1,500~1,900만 — 인구통계 기반 산출, 논리적
- SAM: 스마트폰 보유율(60대 95%, 70대+ 75~80%) × 디지털 리터러시 보정 → 1,000~1,200만 — 합리적
- SOM: 파프리카케어 23만 대비 2년 5~10만 → 보수적이고 현실적

**판정: 통과**

---

## B. 리스크 검증 — 통과

### B-1. 핵심 리스크 식별 및 대응
PRD §11에서 10개 리스크를 유형(사업/기술/운영/법률)별로 분류하고, 각각 확률·영향도·대응 방안을 명시:
- **R-01 네이버 가족 연동 추가**: 확률 낮음(3개월간 미추가 확인, B2B 집중), 빠른 선점으로 대응
- **R-03 어르신 앱 설치 장벽**: 확률 높음(현실적 인정), 자녀 원격 설정 + 최소 인터랙션 설계로 대응
- **R-05 푸시 알림 미수신**: 로컬+원격 이중화, 배터리 최적화 예외 안내
- **R-06 건강정보 개인정보보호**: 복약 알림은 의료기기 비해당, 최소 수집 원칙
- **R-08 pg_cron/pg_net 미지원**: 3개 대안 옵션(pg_net, GitHub Actions cron, Database Webhooks) 명시
- **R-10 카카오톡 대체재**: 확률 높음(현실적 인정), 자동화·기록·대시보드로 차별화

### B-2. 외부 의존성 관리
PRD §12에서 5개 외부 의존성을 영향도·대안과 함께 정리. 핵심 경로에 대해 모두 대안 확보.

**판정: 통과**

---

## C. 기술 검증 — 통과

### C-1. Expo + RN + TS + Supabase 구현 가능성 — 통과
핵심은 CRUD + 알림 스케줄링 + 가족 그룹 로직으로, 기술 스택으로 충분히 구현 가능.

### C-2. project-init 기술 스택 준수 — 통과
Expo Router, Zustand, MMKV, op-sqlite, React Native Paper 등 고정 기술 스택 모두 준수. Aptabase(분석), RevenueCat(결제)도 조건부 의존성 매핑 범위 내.

### C-3. Windows 11 + EAS Build 환경 — 통과
Android 에뮬레이터 개발, iOS EAS Build 클라우드 빌드, 실기기 최종 검증 명시. macOS 필수 도구 의존 없음.

### C-4. 1인 개발 규모·복잡성 — 통과
React Native Paper 기본 컴포넌트 활용, 커스텀 디자인 최소화. 12주 로드맵이 현실적.

### C-5. 3개월 MVP 범위 — 통과
W1~W12 주별 마일스톤, 의존 관계 명시. P0 기능만 3개월 내 구현, P1/P2는 이후 단계.

### C-6. DB 스키마·API 정합성 — 통과
9개 테이블, FK 관계, 인덱스, RLS 정책이 요구사항과 정합. 기능-테이블-API 매핑표(§7-8)에서 모든 P0 기능 매핑 확인.

### C-7. 장애 시나리오 대응 — 통과
- 네트워크 오류: 오프라인 우선 설계(§7-3), sync_queue, 지수 백오프 재시도
- 식약처 API 장애: 24시간 캐시 + 직접 입력 대체 경로
- Expo Push 장애: 로컬 알림으로 핵심 기능 유지
- Supabase Realtime 지연: 풀링 폴백(30초)

### C-8. 오프라인 우선 설계 — 통과
저장소별 역할 분담(MMKV: 설정/토큰, op-sqlite: 복약기록/약 캐시/동기화 큐), 동기화 큐 로직(status 기반 처리, 지수 백오프, 5회 재시도), 충돌 해결 정책(Server Wins) 모두 상세 기술.

### C-9. DB Function SQL 본문 — 통과
7개 함수 완전한 SQL 본문 제공:
1. `generate_daily_dose_logs` — 일별 dose_logs 생성
2. `resolve_expired_snoozes` — snooze 만료 처리 (v1.2 신규)
3. `get_missed_doses` — 미복용 체크
4. `mark_doses_missed` — 미복용 상태 업데이트
5. `get_adherence_stats` — 복약 순응도 통계
6. `accept_invitation` — 초대 수락 (v1.2 이중 방어 추가)
7. `handle_new_user` — 신규 사용자 트리거

### C-10. TypeScript 인터페이스 — 통과
17개 인터페이스(공통 타입 3개, 엔티티 7개, 요청/응답 7개) 정의. 모든 핵심 API의 요청·응답 구조 명세.

### C-11. 상태 관리 구조 — 통과
7개 Zustand Store(Auth, Medication, Schedule, DoseLog, Family, Sync, Settings) 각각 필드·타입·역할 정의.

**판정: 통과**

---

## D. 문서 품질 검증 — 통과

### D-1. 목표·기능·기술 명세 간 모순 — 없음
- Executive Summary의 핵심 메시지가 기능(§5), 기술(§7), 차별화(§9) 전반에 걸쳐 일관
- 수익 모델 변경(3,900원→1,900원)은 시장분석 반영 의도적 조정, 아이디어 문서에서 확인
- snooze 후 미복용 전환 흐름이 FR-03-3(나중에 먹기) → resolve_expired_snoozes → get_missed_doses → check-missed-dose 순서로 일관 연결

### D-2. 16개 섹션 전체 존재 — 확인 완료
1. Executive Summary ✅
2. Problem Statement ✅
3. Target Users & Personas ✅
4. User Stories ✅
5. Functional Requirements ✅
6. Non-functional Requirements ✅
7. Technical Architecture ✅
8. Screen Map & UI 명세 ✅
9. Competitive Differentiation ✅
10. Monetization Strategy ✅
11. Risk Matrix ✅
12. Assumptions & Constraints ✅
13. Out of Scope ✅
14. MVP Roadmap ✅
15. Success Metrics ✅
16. Glossary ✅

### D-3. KPI 구체성·측정 가능성 — 통과
3개 카테고리(사용자/비즈니스/제품) 12개 지표, 모두 정량적 목표치와 측정 방법 명시.

### D-4. P0 기능 수용 기준 (AC-XX-X) — 통과
FR-01~FR-05 모든 P0 기능에 AC 부여. 검증 가능한 조건문 형태:
- AC-01-1~3: 인증 (3초 이내 진입, 초대 코드 유효성)
- AC-02-1~6: 약 관리 (500ms 검색, 1초 저장, 1분 내 동기화)
- AC-03-1~3: 알림 (±1분 정확도, 60×60dp 버튼, 30초 내 동기화)
- AC-04-1~4: 가족 (5초 내 참여, 30분 미복용 알림, 실시간 대시보드)
- AC-05-1~2: 기록 (색상 구분, 날짜별 이력)

### D-5. User Story 커버리지 — 통과
7개 Epic, 27개 US로 전체 기능 영역 커버:
- Epic 1: 인증 및 온보딩 (4개 US)
- Epic 2: 약 등록 및 관리 (5개 US)
- Epic 3: 복약 알림 및 확인 (4개 US)
- Epic 4: 가족 연동 및 모니터링 (4개 US)
- Epic 5: 복약 기록 및 리포트 (3개 US)
- Epic 6: 설정 및 프로필 (4개 US)
- Epic 7: 구독 및 결제 (3개 US)

### D-6. Screen Map + 핵심 화면 UI — 통과
Expo Router 파일 기반 네비게이션 구조 트리(§8-1), 12개 핵심 화면별 영역·UI 구성·데이터·API 매핑(§8-2) 완비.

### D-7. 기능-테이블-API 매핑표 — 통과
§7-8에서 모든 P0 기능(FR-01-1 ~ FR-05-1)이 테이블·API·비고와 함께 매핑됨.

**판정: 통과**

---

## 미달 목록

**미달 사항 없음.**

---

## 최종 판정

### 판정: **통과**

4개 영역(사업성·리스크·기술·문서 품질) 모두 통과. v1.1 리뷰에서 발견된 Major 1건 + Minor 2건이 v1.2에서 모두 해소됨. Critical/Major 미달 사항 없음.

### 판정 근거

| 영역 | 판정 | 요약 |
|------|------|------|
| A. 사업성 | 통과 | TAM/SAM/SOM 논거 충실, 수익 모델 구체적, 2단계 산출물과 일치 |
| B. 리스크 | 통과 | 10개 리스크 체계적 분류, 외부 의존성 대안 확보 |
| C. 기술 | 통과 | v1.2에서 snooze 전환 로직·타임존·인원 제한 이중 방어 모두 해소. 7개 DB Function SQL 완성, 17개 TS 인터페이스, 7개 Zustand Store 정의 |
| D. 문서 품질 | 통과 | 16개 섹션 완비, AC/US/Screen Map/매핑표 완전, 내부 모순 없음 |
