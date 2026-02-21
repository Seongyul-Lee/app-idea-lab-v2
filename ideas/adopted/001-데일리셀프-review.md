---
id: "001"
name: "데일리셀프"
type: "review"
verdict: "pass"
final_decision: "adopted"
critical_count: 0
major_count: 0
minor_count: 0
date: "2026-02-02"
---

# 데일리셀프 (DailySelf) — 4단계 정합성 판별 리뷰

> 판별일: 2026-02-02
> 대상: `ideas/adopted/001-데일리셀프-prd.md` (v2.1 — 2차 리뷰 Minor 보완 + 섹션 16 추가)
> 검증자: Stage 4 비판적 리뷰어
> 리뷰 회차: 3차 (v2.0 2차 리뷰 Minor 4건 보완 확인 + 전면 재검증)

---

## v2.0 → v2.1 보완 현황

| # | 심각도 | v2.0 결함 | v2.1 보완 | 상태 |
|---|--------|----------|----------|------|
| A-m1 | Minor | 보수적 1년차 시나리오(4,500만)가 SOM 하한(1억) 미달, 시나리오별 범위 불명확 | 섹션 10-3에 SOM과 시나리오 관계 주석 추가 — "보수적"은 초기 성장 단계 반영이며 SOM 달성은 6~12개월 사이 안정화 예상 | ✅ 해소 |
| C-m1 | Minor | 체크인 수정(UPDATE) 시 기존 완료 기록 소실 경고 미정의 | 섹션 7-6 generate-plan 로직 3번에서 `has_completed_items` 플래그 반환 + 사용자 확인 다이얼로그 흐름 + `force` 파라미터 재호출 로직 상세 추가 | ✅ 해소 |
| C-m2 | Minor | op-sqlite local_daily_plans에 집계 필드(completed_count, total_count, completion_rate) 누락 | 섹션 7-3 local_daily_plans 테이블에 해당 필드 추가 완료 | ✅ 해소 |
| D-m1 | Minor | 자체 점검 결과의 버전 이력 미반영 | 섹션 7-9에 v1.0/v2.0/v2.1 컬럼 추가, 상단에 버전 이력 설명("v1.0에서 #8 미충족 → v2.0 해소 → v2.1 Minor 보완") 기재 | ✅ 해소 |

**v2.0 리뷰 Minor 4건 + 섹션 16(Glossary) 추가. 전부 해소됨.**

---

## 영역별 판정 요약

| 영역 | 판정 | Critical | Major | Minor |
|------|------|----------|-------|-------|
| A. 사업성 검증 | **통과** | 0 | 0 | 0 |
| B. 리스크 검증 | **통과** | 0 | 0 | 0 |
| C. 기술 검증 | **통과** | 0 | 0 | 0 |
| D. 문서 품질 검증 | **통과** | 0 | 0 | 0 |
| **합계** | — | **0** | **0** | **0** |

---

## A. 사업성 검증 — 통과

### A-1. 시장 규모·경쟁분석 정합성 ✅

PRD v2.1의 데이터와 2단계 산출물(시장분석·경쟁분석) 대조 검증:

| 항목 | PRD (섹션 2-2) | 2단계 산출물 | 일치 |
|------|---------------|------------|------|
| 직장인 야근 경험률 | 63% | 시장분석 §3-2: 63% | ✅ |
| 자기계발 실패 1위 | 정시퇴근 어려움 62.4% | 시장분석 §3-2: 62.4% | ✅ |
| 번아웃 경험률 | 69~93% | 시장분석 §3-2: 69~93% | ✅ |
| 습관 앱 이탈률 | 48% | 시장분석 §3-3: 48% | ✅ |
| TAM | 320억 원 | 시장분석 §2-1: 320억 원 (Sensor Tower 실측) | ✅ |
| SAM 인구 | 710만 명 | 시장분석 §2-2: 710만 명 | ✅ |
| SAM (PRD 확장: 대학생 포함) | 910만 명 | PRD 섹션 3에서 200만 추가 — 시장분석에는 710만만 있으나 대학생 확장은 합리적 추정 | ✅ |
| SOM 1년차 | 5~10만 / 1~3억 원 | 시장분석 §2-3: 동일 | ✅ |
| 경쟁사 "컨디션 적응" 부재 | 6개 앱 모두 미제공 | 경쟁분석 §2-1 표: 6개 모두 "X" | ✅ |
| 마이루틴 가격 | 월 3,900원 / 연 28,000원 | 경쟁분석 §4-3: 동일 | ✅ |

수치 정합성 완전 확인.

### A-2. 차별점 방어 가능성 ✅

- 경쟁분석 §2-3 포지셔닝 맵에서 "적응형 루틴" 영역이 공백임을 확인
- PRD 섹션 9-3에서 방어 전략을 기술적 해자(낮음)·데이터 해자(중간)·실행 속도 해자(높음)으로 분류
- 기술적 해자가 낮다는 솔직한 자기 평가가 있으며, 데이터 축적 + 선점이 현실적 방어 전략
- 가장 유사한 경쟁자(루빗: 습관+감정 통합, ChatGPT 도입)도 "컨디션 기반 난이도 자동 조절"은 미제공

### A-3. 수익 모델 ✅

- 월 3,900원 / 연 29,900원 — 마이루틴(월 3,900원/연 28,000원), 루티너리(월 3,500원/연 25,000원)와 동일 가격대
- Progressive Paywall 전략이 구체적 (무료: 체크인+플랜+주간리포트 / 유료: 무제한 루틴+AI 인사이트)
- 수익 예측 4개 시나리오 + SOM 관계 주석으로 보수적 시나리오와 SOM 간 관계가 명확
- LTV 추정(6개월 × 연 29,900원 ≈ 14,950원)이 습관 앱 이탈률 48% + churn rate 월 6~8%에 근거

### A-4. TAM/SAM/SOM 논리적 근거 ✅

- TAM 320억 원: Sensor Tower 실측 기반으로 보수적이고 신뢰 가능
- SAM 85억 원: 710만 × 4% × 연 29,900원 — 논리적 도출
- SOM 1년차 1~3억 원: 마이루틴 2년 50만 사례 참조, 하루콩 10개월 150만 DL 낙관 시나리오 참조

---

## B. 리스크 검증 — 통과

### B-1. 핵심 리스크 식별 ✅

섹션 11에서 10개 리스크를 유형(사업 5/기술 3/법률 1/운영 1)·발생확률·영향도로 분류:

| # | 리스크 | 대응 방안 적절성 |
|---|--------|---------------|
| R-01 | 치열한 경쟁 | ✅ 차별점 포지셔닝 + ASO |
| R-02 | 낮은 유료 전환율 | ✅ Progressive Paywall + AI 인사이트 가치 증명 |
| R-03 | 개념 교육 비용 | ✅ 온보딩 비포/애프터 시나리오 |
| R-04 | 대형 앱사 카피 | ✅ 빠른 실행 + 데이터 축적 선점 |
| R-05 | 높은 이탈률 | ✅ "실패하지 않는 구조" 자체가 이탈 방지 |
| R-06 | Supabase 장애 | ✅ Offline-First 설계 |
| R-07 | RevenueCat 호환 | ✅ P1이므로 MVP 핵심 미영향 |
| R-08 | Free Tier 한계 | ✅ 구체적 전환 트리거 명시 |
| R-09 | 법률/정책 변경 | ✅ 최소 데이터 수집 + 계정 삭제 |
| R-10 | 1인 개발 번아웃 | ✅ P0/P1/P2 엄격 적용 |

시장분석 §5-2의 5대 리스크(경쟁, 전환율, 교육비용, 카피, 이탈률)가 R-01~R-05와 정확히 매핑됨.

### B-2. 외부 의존성 리스크 ✅

5개 외부 서비스(Supabase, RevenueCat, OAuth 3사, Expo Push, Aptabase)에 대해 의존도·장애 시 영향·대응이 명시됨. Supabase 의존도 "높음"에 대한 Offline-First 대응이 구체적(op-sqlite 캐시, Sync Queue, 최대 24시간 독립 운영).

---

## C. 기술 검증 — 통과

### C-1. Expo + RN + TS + Supabase 구현 가능성 ✅

P0 기능(FR-01~FR-08) 모두 기술 스택 범위 내 구현 가능:

- 소셜 로그인: Supabase Auth + OAuth 연동 — 표준적 구현
- 체크인 UI: 슬라이더(react-native-paper), 숫자 스텝퍼, SegmentedButtons — 표준 컴포넌트
- 적응형 플랜: Edge Function(Deno)에서 규칙 기반 알고리즘 — 외부 AI API 불필요
- 주간 리포트: PostgreSQL Function `get_weekly_stats` + Victory Native(Skia 기반) 차트
- 오프라인: MMKV + op-sqlite + Sync Queue — 검증된 패턴

### C-2. project-init 고정 기술 스택 준수 ✅

| 기술 스택 | PRD 반영 | 준수 |
|----------|---------|------|
| Expo Managed Workflow | 섹션 7-1: SDK 52+ | ✅ |
| Expo Router | 섹션 7-1: v4+ 파일 기반 | ✅ |
| TypeScript (strict) | 섹션 7-1 | ✅ |
| Zustand v5+ | 섹션 7-1, 7-7 | ✅ |
| react-native-mmkv v3+ | 섹션 7-1, 7-3 | ✅ |
| op-sqlite | 섹션 7-1, 7-3 | ✅ |
| Supabase | 섹션 7-1 | ✅ |
| React Native Paper v5+ | 섹션 7-1 | ✅ |

RevenueCat은 project-init 조건부 매핑에 미지원이므로 수동 설치 필요 — 섹션 7-1 및 12에서 이미 명시.

### C-3. Windows 11 + EAS Build 환경 호환 ✅

- Expo Managed Workflow: Windows 개발 완전 지원
- iOS 빌드: EAS Build(클라우드)로 macOS 불필요 — 섹션 7-1에서 명시
- iOS 전용 API 과의존: Apple Auth는 iOS 필수(App Store 정책)이나 Supabase Auth가 래핑하므로 네이티브 코드 직접 수정 불필요
- HealthKit/ARKit 등 플랫폼 전용 API 의존: 없음

### C-4. 1인 개발 규모 적정성 ✅

- 테이블 8개 + routine_categories(시스템 정의)
- Edge Function 2개(generate-plan, delete-account)
- DB Function 5개(get_weekly_stats, get_user_routine_count, get_recent_avg_condition, seed_default_routines, handle_new_user)
- REST 엔드포인트 14개 + RPC 4개 + Edge Function 2개 = 20개
- Zustand Store 8개
- 화면 10개 + 모달 6개
- 12주 로드맵으로 주차별 작업이 분할되어 현실적

### C-5. 3개월 MVP 범위 현실성 ✅

- P0 8개 기능이 1~8주차에 배치, P1 기능 10주, 결제 11주, QA/출시 12주
- 핵심 루프(체크인→플랜→달성)가 6주차 완성 — 7주차부터는 보조 기능
- 규칙 기반 알고리즘이므로 ML 파이프라인/학습 데이터 불필요
- 9주차 오프라인/동기화가 가장 복잡한 기술적 과제이나, Sync Queue 패턴이 상세히 정의되어 있어 구현 가이드로 충분

### C-6. DB 스키마·API 설계 정합성 ✅

- 8개 테이블의 FK 관계: profiles → routines/checkins/daily_plans/reflections/subscriptions, daily_plans → plan_items/checkins, routines → plan_items
- RLS 정책: CRUD 권한 매트릭스와 일치. plan_items는 서브쿼리 기반 RLS(daily_plans.user_id 확인)
- API 20개 엔드포인트가 P0/P1 기능 요구사항 모두 커버
- 기능-테이블-API 매핑표(섹션 7-8)에서 FR-01~FR-08 전체 매핑 확인

### C-7. 장애 시나리오 대응 ✅

| 장애 시나리오 | 대응 | PRD 근거 |
|-------------|------|---------|
| 네트워크 오류 | Sync Queue(재시도 5회, 지수 백오프 1s→16s, 배치 10개) | 섹션 7-3 |
| 서버 다운 | op-sqlite 로컬 캐시에서 핵심 기능 유지 | 섹션 7-3, 7-4 |
| 체크인 스킵 | get_recent_avg_condition RPC로 최근 7일 평균 기반 기본 플랜 | AC-03-5, 섹션 7-6 |
| 체크인 수정 후 플랜 재생성 시 기존 완료 기록 | has_completed_items 플래그 + 사용자 확인 다이얼로그 + force 재호출 | 섹션 7-6 (v2.1 보완) |
| Sync Queue 5회 실패 | 사용자 에러 알림, 해당 항목 플래그 처리 | 섹션 7-3 |

### C-8. 오프라인 우선 설계 ✅

- **저장소별 역할 분담**: MMKV(설정/토큰/플래그), op-sqlite(구조화 캐시/동기화 큐), Supabase(원본 SSOT) — 명확
- **op-sqlite 로컬 스키마**: sync_queue, local_checkins, local_daily_plans(집계 필드 포함), local_plan_items, local_routines — 5개 테이블 정의 완료
- **동기화 큐 로직**: created_at ASC 순서, 배치 10개, 재시도 5회, 지수 백오프(1s→2s→4s→8s→16s)
- **동기화 트리거**: 앱 포그라운드 진입, 네트워크 상태 변경, 수동 새로고침
- **충돌 해결**: Server Wins 정책, 시나리오 3개(체크인 중복, 플랜 변경, 루틴 수정)별 처리 방식 명시
- **근거**: "1인 사용자 앱에서 멀티 디바이스 충돌 가능성 낮음, 서버 SSOT로 로직 단순화" — 합리적

### C-9. 핵심 DB Function SQL 본문 ✅

5개 DB Function 모두 실행 가능한 완성 SQL:

| Function | 검증 | 비고 |
|----------|------|------|
| `get_weekly_stats` | ✅ | JSON 응답, daily_data + summary + insights(에너지-달성 상관, 수면-에너지 상관) |
| `get_user_routine_count` | ✅ | 구독 상태별 한도(무료 5 / 유료 무제한), can_create 플래그 |
| `get_recent_avg_condition` | ✅ | 최근 N일 평균 에너지/수면 + 최빈 mood, COALESCE 기본값(에너지 3, 수면 7.0, mood neutral) |
| `seed_default_routines` | ✅ | 5개 카테고리별 템플릿 루틴 INSERT, FOREACH + CASE 패턴 |
| `handle_new_user` | ✅ | profiles + subscriptions 자동 생성, SECURITY DEFINER, auth.users 트리거 |

`update_updated_at_column()` 트리거 함수 + 6개 트리거도 작성됨.

### C-10. TypeScript 인터페이스 ✅

18개 인터페이스 + 공통 타입 3개:

- 데이터 모델: Profile, Routine, Checkin, DailyPlan, PlanItem, Reflection (6개)
- 요청: UpdateProfileRequest, CreateRoutineRequest, UpdateRoutineRequest, CreateCheckinRequest, UpdatePlanItemRequest, GeneratePlanRequest, DeleteAccountRequest, WeeklyStatsRequest, CreateReflectionRequest (9개)
- 응답: GeneratePlanResponse, DeleteAccountResponse, WeeklyStatsResponse, RoutineCountResponse (4개, 1개는 요청과 통합)
- 공통: Mood, PlanType, SubscriptionStatus (3개)

GeneratePlanResponse에 `has_completed_items` 플래그와 `algorithm_info` 상세 구조가 포함되어 클라이언트 구현 가이드로 충분.

### C-11. 상태 관리 구조 ✅

Zustand Store 8개(섹션 7-7):

| Store | 역할 | persist | 검증 |
|-------|------|---------|------|
| useAuthStore | 인증/프로필/온보딩 | MMKV | ✅ |
| useCheckinStore | 오늘 체크인/최근 7일 | MMKV | ✅ |
| useRoutineStore | 루틴 풀/카테고리/개수 | op-sqlite | ✅ |
| usePlanStore | 오늘 플랜/아이템/달성률 | op-sqlite | ✅ |
| useReportStore | 주간 리포트 | 없음(매번 fetch) | ✅ |
| useSubscriptionStore | 구독 상태 | MMKV | ✅ |
| useSettingsStore | 알림/테마 설정 | MMKV | ✅ |
| useSyncStore | 동기화 큐 상태 | MMKV | ✅ |

---

## D. 문서 품질 검증 — 통과

### D-1. 목표·기능·기술 명세 간 모순 검사 ✅

주요 교차 검증 결과:

| 확인 항목 | 결과 |
|----------|------|
| FR-03 수면 "0.5시간 단위" ↔ DB `NUMERIC(3,1)` ↔ TS `sleep_hours: number` | ✅ 일치 |
| FR-04 무료 5개 한도 ↔ `get_user_routine_count` max_custom=5 ↔ AC-04-2 | ✅ 일치 |
| FR-05 에너지 1~2 → 최대 2개 ↔ generate-plan max_routines ↔ AC-05-2 | ✅ 일치 |
| FR-07 달성률 계산 ↔ `daily_plans.completion_rate` ↔ AC-07-2 계산식 | ✅ 일치 |
| FR-08 최소 3일 데이터 ↔ `get_weekly_stats` HAVING COUNT(*) >= 1/3 ↔ AC-08-3 | ✅ 일치 |
| 섹션 10 가격 ↔ 섹션 5 FR-11 설명 ↔ 화면 10 구독 관리 UI | ✅ 일치 |
| 섹션 14 로드맵 ↔ P0/P1/P2 우선순위 순서 | ✅ 일치 |
| 섹션 7-6 TS 인터페이스 ↔ 섹션 7-5 DB 스키마 컬럼 | ✅ 일치 |
| 섹션 8 화면-데이터-API 매핑 ↔ 섹션 7-8 기능-테이블-API 매핑 | ✅ 일치 |

### D-2. 섹션 존재 여부 (16개 전체) ✅

| # | 필수 섹션 | PRD 위치 | 상태 |
|---|----------|---------|------|
| 1 | Executive Summary | 섹션 1 | ✅ |
| 2 | Problem Statement | 섹션 2 | ✅ |
| 3 | Target Users & Personas | 섹션 3 | ✅ |
| 4 | User Stories | 섹션 4 | ✅ |
| 5 | Functional Requirements | 섹션 5 | ✅ |
| 6 | Non-functional Requirements | 섹션 6 | ✅ |
| 7 | Technical Architecture | 섹션 7 | ✅ |
| 8 | Screen Map & UI 명세 | 섹션 8 | ✅ |
| 9 | Competitive Differentiation | 섹션 9 | ✅ |
| 10 | Monetization Strategy | 섹션 10 | ✅ |
| 11 | Risk Matrix | 섹션 11 | ✅ |
| 12 | Assumptions & Constraints | 섹션 12 | ✅ |
| 13 | Out of Scope | 섹션 13 | ✅ |
| 14 | MVP Roadmap | 섹션 14 | ✅ |
| 15 | Success Metrics | 섹션 15 | ✅ |
| 16 | Glossary | 섹션 16 | ✅ **(v2.1 추가)** |

**16개 섹션 전체 존재.**

### D-3. 수용 기준(AC) 검증 ✅

모든 P0 기능(FR-01~FR-08)에 AC-XX-X 형식의 수용 기준이 존재:

| P0 기능 | AC 개수 | 검증 가능성 |
|---------|---------|-----------|
| FR-01 소셜 로그인 | AC-01-1~3 (3개) | ✅ 조건문 형태 |
| FR-02 온보딩 | AC-02-1~3 (3개) | ✅ 조건문 형태 |
| FR-03 체크인 | AC-03-1~5 (5개) | ✅ 조건문 형태 |
| FR-04 루틴 CRUD | AC-04-1~4 (4개) | ✅ 조건문 형태 |
| FR-05 적응형 플랜 | AC-05-1~5 (5개) | ✅ 조건문 형태 |
| FR-06 플랜 수동 조정 | AC-06-1~3 (3개) | ✅ 조건문 형태 |
| FR-07 완료 체크 | AC-07-1~3 (3개) | ✅ 조건문 형태 |
| FR-08 주간 리포트 | AC-08-1~3 (3개) | ✅ 조건문 형태 |

총 29개 AC, 모두 검증 가능한 조건문 형태.

### D-4. User Story 커버리지 ✅

27개 User Story, 9개 Epic(온보딩 3 / 인증 3 / 체크인 3 / 루틴 3 / 플랜 4 / 회고 2 / 리포트 3 / 설정 3 / 결제 3). 27개 ≥ 15개 최소 요건 충족.

### D-5. Screen Map ✅

- **네비게이션 구조 트리**: Expo Router 파일 기반 — `(auth)`, `(tabs)`, `(modal)` 3개 그룹, 하위 파일 구조 완전 정의
- **핵심 화면 10개**: 로그인, 온보딩 환영, 온보딩 카테고리, 홈(플랜), 체크인 모달, 루틴 관리, 루틴 생성 모달, 주간 리포트, 설정, 구독 관리 — 각 화면별 영역·구성 상세 기술
- **네비게이션 흐름**: 앱 시작 → 인증 분기 → 각 화면 이동 도식화
- **화면-데이터-API 매핑**: 10개 화면별 Zustand Store + API 호출 + 데이터 흐름

### D-6. 기능-테이블-API 매핑표 ✅

섹션 7-8 매핑표에서 P0 기능 FR-01~FR-08 전체 매핑 완료. FR-07에 "클라이언트가 집계 필드 재계산" 명시.

### D-7. KPI ✅

- **North Star Metric**: "Weekly Active Checkin Rate" — 선정 근거 제시
- **핵심 KPI 11개**: 사용자(4개) + 제품(4개) + 비즈니스(3개), 1/3/6개월 목표치
- **Aptabase 이벤트 15개**: 트리거 시점 + properties 정의
- **모니터링 대시보드 4개**: Aptabase, RevenueCat, Supabase Dashboard, 앱스토어

### D-8. Glossary ✅ (v2.1 추가)

섹션 16에 14개 용어 정의. PRD 전반에서 사용되는 도메인 용어(체크인, 에너지 레벨, 적응형 플랜, 스코어링 알고리즘 등)와 기술 용어(Server Wins, Sync Queue, RLS, Edge Function 등)를 포함.

---

## 미달 목록 종합

**해당 없음.** Critical/Major/Minor 모두 0건.

---

## 최종 판정

### 판정: **통과**

**근거:**

1. **3회 반복 리뷰를 통한 품질 수렴 확인**
   - v1.0: Critical 1건(화면 구조 누락) + Major 1건(KPI 누락) + Minor 5건 → 3단계 회귀
   - v2.0: 7건 전부 해소, 신규 Minor 4건 발견 → 통과 (Minor만)
   - v2.1: Minor 4건 전부 해소, 신규 결함 0건 → **최종 통과**

2. **4개 영역 전부 통과**
   - A. 사업성: 시장 데이터 정합, 차별점 유효, 수익 모델 현실적
   - B. 리스크: 10개 리스크 식별 + 대응, 외부 의존성 관리 가능
   - C. 기술: 기술 스택 준수, 1인 개발 규모 적정, 3개월 MVP 현실적, 오프라인 설계 구체적
   - D. 문서: 16개 섹션 완비, 29개 AC, 27개 User Story, 매핑표·Screen Map 완전

3. **CLAUDE.md Task Master 투입 요건 전체 충족**
   - ✅ 기능 명세: P0(8개)/P1(6개)/P2(6개) 우선순위, 입력/출력/제한사항
   - ✅ 수용 기준: 29개 AC-XX-X 형식 검증 가능 조건문
   - ✅ DB 스키마: CREATE TABLE 8개 + routine_categories, FK, 인덱스 12개, RLS 정책 18개, DB Function SQL 5개 완성 + 트리거 6개
   - ✅ API 설계: 20개 엔드포인트, TypeScript 인터페이스 18개 + 공통 타입 3개
   - ✅ 아키텍처 상세도: 기술 스택 16개 항목, 시스템 구조도, 데이터 흐름 4개, Zustand Store 8개, 기능-테이블-API 매핑표
   - ✅ 화면 구조 / UI 명세: 네비게이션 트리(3개 그룹), 핵심 화면 10개 UI 구성, 화면-데이터-API 매핑 10개
   - ✅ 오프라인/동기화 설계: MMKV/op-sqlite 역할 분담, 로컬 스키마 5개 테이블, Sync Queue 로직(재시도 5회, 지수 백오프, 배치 10개), Server Wins 충돌 해결(시나리오 3개)
