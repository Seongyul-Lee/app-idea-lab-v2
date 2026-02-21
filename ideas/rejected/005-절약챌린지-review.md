# 005-절약챌린지 PRD 정합성 판별 보고서

> **판별일**: 2026-02-02 (2차 검증)
> **대상 문서**: `ideas/005-절약챌린지-prd.md` (v1.1, 2026-02-02 수정본)
> **참조 문서**: `ideas/005-절약챌린지.md`, `research/competitors/005-경쟁분석.md`, `research/market-data/005-시장분석.md`
> **이전 판별**: 2026-02-02 1차 검증 → 3단계 회귀 판정 (Major 2건, Minor 2건)

---

## 1차 미달 사항 수정 확인

| # | 이전 판정 | 수정 확인 | 상세 |
|---|----------|----------|------|
| C-M-01 (Major) | `update_challenge_progress`에서 `spending_limit` 완료 판정 누락 | **수정 완료** | 1232~1265행에 `spending_limit` 분기 추가. `CURRENT_DATE >= end_date` 시 실제 지출이 `target_value` 이하이면 `completed`, 초과이면 `failed` 처리. XP/배지 보상도 정상 지급. |
| C-M-02 (Minor) | `upsert_daily_record` 스트릭 계산 중복 코드 | **수정 완료** | 불필요한 윈도우 함수 서브쿼리 제거. 반복문 로직(973~986행)만 유지. |
| C-M-03 (Minor) | profiles RLS에서 민감 필드 전체 공개 | **수정 완료** | `profiles_select_public` 정책 제거. `profiles_select_own`만 유지. 타 사용자 공개 정보는 `get_public_profile` RPC(SECURITY DEFINER)로 제공. 민감 필드(push_token, premium_expires_at, delete_requested_at) 노출 차단 확인. |
| D-M-01 (Major) | 섹션 16 Glossary 누락 | **수정 완료** | 섹션 16에 21개 도메인·기술 용어 정의 추가. 최소 10개 요건 충족. 무지출 인증, 스트릭, 원탭 인증, 절약방, 배지, XP, 레벨, Server Wins, SyncQueue, RLS, Edge Function, RPC, MMKV, op-sqlite, RevenueCat, YONO 등 포함. |

**4건 모두 수정 완료 확인.**

---

## A. 사업성 검증 — **통과**

### A-1. 시장 규모·경쟁분석 정합성

PRD의 시장 수치(TAM 990만 명, SAM 119만 명, SOM 2.38만 유료/47.6만 총 사용자)는 2단계 시장분석 산출물과 완전히 일치한다. 인구 통계(20~39세 1,237만 명), 절약 관심 비율(80.7%), 무지출 챌린지 적극 참여층(12%) 등 산출 논거가 동일하다.

경쟁분석도 PRD 섹션 9(Competitive Differentiation)에 2단계 산출물의 경쟁자 3사(뱅크샐러드 샐러드게임, 카카오페이 카드 절약 챌린지, 챌린저스)와 간접 경쟁자(거지방, 인스타그램 등)가 모두 반영되어 있다.

### A-2. 차별점 방어 가능성

"극단적 단순화(1탭 인증) + 스트릭 기반 장기 습관 형성 + 앱 내 절약 커뮤니티 + 독립 전용 앱"이라는 차별점은 2단계 경쟁분석에서 도출된 전략과 일치한다. UX/포지셔닝 차별점이므로 기술적 해자는 낮으나, PRD 섹션 9에서 커뮤니티 네트워크 효과 기반 방어 전략을 명시하고 있다.

### A-3. 수익 모델

프리미엄 구독(월 2,900원) + 광고 하이브리드 모델이 구체적으로 기술되어 있다. SOM 기준 연 11.6억 원(구독 8.3억 + 광고 3.3억) 전망은 2단계 시장분석의 SOM 합계 13억 원과 대체로 일치(PRD에서 구독 단가를 2,900원으로 확정하면서 소폭 조정). 절약 앱 사용자의 과금 민감성 리스크도 섹션 11에서 대응 방안과 함께 명시.

### A-4. TAM/SAM/SOM 논리적 근거

- TAM: 행정안전부 인구통계 + 한국방송광고진흥공사 조사 → 논거 있음
- SAM: TAM의 12%(카카오톡 거지방 운영 규모, 챌린저스 171만 사용자 등 근거) → 합리적
- SOM: SAM의 2%(1인 개발 앱의 현실적 침투율) → 보수적이고 현실적

**판정: 통과**

---

## B. 리스크 검증 — **통과**

### B-1. 핵심 리스크 식별 및 대응

PRD 섹션 11(Risk Matrix)에 10개 리스크가 사업/기술/운영/법률/의존성 분류별로 식별되어 있다:

| 분류 | 리스크 수 | 대응 방안 유무 |
|------|-----------|--------------|
| 사업 | 3개 | 모두 있음 |
| 기술 | 2개 | 모두 있음 |
| 운영 | 2개 | 모두 있음 |
| 법률 | 1개 | 있음 |
| 의존성 | 2개 | 모두 있음 |

핵심 리스크(대기업 진출, 유료 전환 저항, 커뮤니티 콜드 스타트)에 대한 대응 방안이 구체적으로 기술되어 있다.

### B-2. 외부 의존성 관리

섹션 12에 외부 의존성 5개(Supabase, RevenueCat, Expo Push, App Store/Play Store, Kakao OAuth)가 리스크 수준과 함께 명시. Supabase는 오픈소스 기반 셀프호스팅 전환 가능, RevenueCat은 react-native-iap 직접 구현으로 대체 가능 등 대안이 제시되어 있다.

**판정: 통과**

---

## C. 기술 검증 — **통과**

### C-1. 기술 스택 구현 가능성

Expo Managed Workflow + TypeScript + Supabase로 명세된 모든 기능이 구현 가능하다. 캘린더(react-native-calendars), 애니메이션(Lottie), 결제(RevenueCat), 로컬 DB(op-sqlite), KV 저장소(MMKV) 등 모든 라이브러리 선택이 적절하다.

### C-2. project-init 고정 기술 스택 준수

Expo Router, Zustand, MMKV, op-sqlite 등 project-init 고정 기술 스택을 모두 준수. 섹션 12에서 "모든 기술 선택이 project-init 고정 스택 및 조건부 의존성 테이블 범위 내"임을 확인.

### C-3. Windows 11 + EAS Build 환경 호환성

macOS 필수 도구나 iOS 전용 API(HealthKit, ARKit 등) 의존 없음. EAS Build 클라우드 빌드로 iOS 빌드 가능.

### C-4. 1인 개발 규모 적정성

MVP 범위가 P0 9개 기능 + P1 7개로 구성. 복잡한 실시간 기능(채팅 등)은 명시적으로 제외(Out of Scope). 커뮤니티는 비동기 피드 방식으로 설계.

### C-5. 3개월 MVP 범위 현실성

12주 로드맵이 주 단위로 상세 기술. Phase 1(기반, 3주) → Phase 2(핵심 기능, 4주) → Phase 3(소셜+마무리, 5주) 구조. 마일스톤 체크포인트 5개로 진행 관리 가능.

### C-6. DB 스키마·API 정합성

11개 테이블 정의 완료. FK 관계, 인덱스, CHECK 제약조건 모두 명시. RLS 정책이 CRUD 권한 매트릭스와 함께 완전한 SQL로 작성. API 엔드포인트 23개가 REST + DB Function(RPC) + Edge Function으로 적절히 분배. 기능-테이블-API 매핑표(섹션 7-8)로 모든 P0 기능이 매핑.

### C-7. 장애 시나리오 대응

오프라인 동작(인증·캘린더·스트릭), 네트워크 복귀 시 동기화, SyncQueue 재시도 로직(지수 백오프, 최대 5회), 실패 시 사용자 알림 등이 명세. Supabase 장애 시 오프라인 우선 설계로 핵심 기능 로컬 동작, RevenueCat 장애 시 grace period 등 대응 기술.

### C-8. 오프라인 우선 설계

- 저장소별 역할 분담: MMKV(KV 데이터), op-sqlite(관계형 데이터), Supabase Storage(이미지) — 명확히 분리
- 동기화 큐: MMKV 기반 SyncQueue 구조(id, type, table, payload, created_at, retry_count, next_retry_at) — 구체적
- 충돌 해결: Server Wins 정책 + updated_at 기반 비교 — 명시적

### C-9. 핵심 DB Function SQL 본문

7개 RPC(upsert_daily_record, get_monthly_summary, join_challenge, update_challenge_progress, toggle_post_like, handle_new_user, get_public_profile)가 완전한 SQL 본문으로 작성. 스텁 아님.

v1.1에서 `update_challenge_progress`의 `spending_limit` 완료 판정 로직이 정상 추가됨. `get_public_profile` RPC도 SECURITY DEFINER로 추가되어 타 사용자 프로필 조회 시 민감 필드 노출을 방지.

### C-10. TypeScript 인터페이스

핵심 API의 요청/응답 인터페이스가 모두 작성. Profile, PublicProfile, DailyRecord, UpsertDailyRecordRequest/Response, MonthlySummary, Challenge, ChallengeParticipant, BadgeDefinition, UserBadge, CommunityPost, PostComment 등 전체 커버.

### C-11. 상태 관리 구조

Zustand Store 6개(AuthStore, RecordStore, ChallengeStore, CommunityStore, BadgeStore, SyncStore)가 필드·타입·역할과 함께 정의.

**판정: 통과**

---

## D. 문서 품질 검증 — **통과**

### D-1. 목표·기능·기술 명세 간 모순

- Executive Summary의 핵심 기능 3가지가 Functional Requirements(FR-02~FR-07)와 정확히 대응
- 기술 스택 선택이 기능 요구사항과 정합
- 수익 모델(프리미엄 월 2,900원)이 기능 요구사항(FR-15, FR-11)과 일치
- 모순 발견되지 않음

### D-2. 16개 섹션 존재 여부

| # | 섹션 | 존재 |
|---|------|------|
| 1 | Executive Summary | O |
| 2 | Problem Statement | O |
| 3 | Target Users & Personas | O |
| 4 | User Stories | O |
| 5 | Functional Requirements | O |
| 6 | Non-functional Requirements | O |
| 7 | Technical Architecture | O |
| 8 | Screen Map & UI 명세 | O |
| 9 | Competitive Differentiation | O |
| 10 | Monetization Strategy | O |
| 11 | Risk Matrix | O |
| 12 | Assumptions & Constraints | O |
| 13 | Out of Scope | O |
| 14 | MVP Roadmap | O |
| 15 | Success Metrics | O |
| 16 | Glossary (용어 사전) | O |

**16개 섹션 모두 존재.**

### D-3. KPI 구체성·측정 가능성

섹션 15에 3개 카테고리(사용자/비즈니스/제품)의 KPI가 수치 목표·측정 방법과 함께 정의. North Star Metric(주간 무지출 인증 횟수)도 명시. 모두 구체적이고 측정 가능.

### D-4. P0 수용 기준 검증

모든 P0 기능(FR-01~FR-09)에 AC-XX-X 형식의 검증 가능한 조건문이 존재.

| P0 기능 | AC 수 |
|---------|-------|
| FR-01 | AC-01-1~4 (4개) |
| FR-02 | AC-02-1~4 (4개) |
| FR-03 | AC-03-1~4 (4개) |
| FR-04 | AC-04-1~4 (4개) |
| FR-05 | AC-05-1~4 (4개) |
| FR-06 | AC-06-1~4 (4개) |
| FR-07 | AC-07-1~4 (4개) |
| FR-08 | AC-08-1~3 (3개) |
| FR-09 | AC-09-1~4 (4개) |

### D-5. User Story 커버리지

8개 Epic, 24개 User Story. 온보딩·인증(Epic 1), 핵심 기능(Epic 2~4), 리포트(Epic 5), 알림(Epic 6), 설정(Epic 7), 결제(Epic 8) 모든 기능 영역을 커버. 최소 15개 요건 충족.

### D-6. Screen Map & 핵심 화면 UI

- 네비게이션 구조 트리: Expo Router 기반 (auth) → (main) → 5개 탭 + Modal Stack 구조가 명시
- 핵심 화면 12개의 UI 구성이 영역별 구성 요소, 데이터, API와 함께 기술

### D-7. 기능-테이블-API 매핑표

섹션 7-8에 매핑표 존재. 모든 P0 기능(FR-01~FR-09)이 관련 테이블·API·비고와 함께 매핑 완료.

**판정: 통과**

---

## 미달 목록

**없음.** 1차 검증에서 발견된 Major 2건, Minor 2건이 모두 수정 완료되었으며, 2차 검증에서 새로운 미달 사항이 발견되지 않았다.

---

## 최종 판정

### **통과**

**근거:**
- A(사업성): 통과 — 시장 수치 정합, 차별점 명시, 수익 모델 구체적
- B(리스크): 통과 — 10개 리스크 식별 및 대응 방안 완비
- C(기술): 통과 — 1차 미달 3건(spending_limit 판정, 스트릭 중복코드, RLS 민감필드) 모두 수정 완료. 기술 스택·DB 스키마·API·오프라인 설계·상태 관리 모두 정합
- D(문서): 통과 — 1차 미달 1건(Glossary 누락) 수정 완료. 16개 섹션 전체 존재, 수용 기준·User Story·Screen Map·매핑표 모두 충족
- **Critical/Major 미달 없음 → 통과 판정**
