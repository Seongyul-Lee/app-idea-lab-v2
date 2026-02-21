---
id: "002"
name: "마음날씨"
type: "review"
verdict: "pass"
final_decision: "adopted"
critical_count: 0
major_count: 0
minor_count: 0
date: "2026-02-02"
---

# 002-마음날씨 PRD 정합성 판별 보고서

> 판별일: 2026-02-02
> PRD 버전: v1.2 (2026-02-02, C-3 수정 반영)
> 판별 대상: `ideas/002-마음날씨-prd.md`
> 리뷰 차수: 6차 (5차 잔존 Minor 1건 수정 확인)

---

## A. 사업성 검증 — **통과**

### 시장 규모·경쟁분석 일치 여부

PRD의 시장 데이터는 2단계 산출물과 **일치**한다.

- TAM/SAM/SOM: PRD §2-2의 "MZ세대 감정 관리 중간층 300~500만 명"은 시장분석 §1-2의 "감정 기록 앱 잠재 사용자 약 300~500만 명"과 동일
- 한국 기분 기록 앱 성장 데이터 "2023 50만→2025 200만 (4배 성장, 와이즈앱)"이 양 문서에서 동일 인용
- 경쟁사 분석(하루콩 1,000만 DL, 루빗 210만+ DL, 라일리 하루 2024.12 출시 등)이 경쟁분석 문서와 일치
- 가격 변경(2단계 3,500원 → PRD 3,900원): PRD §10-2에서 RevenueCat 2025 벤치마크 기반 상향 근거를 제시하여 합리적 변경

### 차별점 방어 가능성

- "기록→분석→행동 완결 루프"는 경쟁분석 §3-1에서 "높음" 강도로 평가된 유의미한 차별점
- PRD §9-3에서 단기/중기/장기 Moat 전략이 구체적
- §9-4에서 라일리 하루 대비 "분석 중심 vs 행동 제안 중심" 포지셔닝 분리가 명확

### 수익 모델

- 프리미엄 구독 (월 3,900원 / 연 29,900원)은 경쟁사 벤치마크 대비 합리적 가격대
- 무료/유료 가치 격차가 명확: 월간 상세 리포트(블러 처리), 무제한 셀프케어 카드
- 7일 무료 체험(결제 정보 없이)→유료 전환 전략이 RevenueCat 2025 데이터에 근거

---

## B. 리스크 검증 — **통과**

### 핵심 리스크 식별

PRD §11에서 10개 리스크가 유형(사업/기술/법률/운영)별로 분류되고, 가능성/영향도/심각도/대응 방안이 모두 기술됨.

- **R1(하루콩 AI 기능 추가)**: Critical — 하루콩의 글로벌 전략(해외 80%)으로 한국어 특화 가능성 낮다는 분석이 경쟁분석과 일치
- **R6(감정 기록 습관화 실패)**: Critical — 10초 기록 + 푸시 리마인더 + 셀프케어 카드 즉시 보상으로 대응
- **R2(블루시그넘 생태계 완성)**: High — 경쟁분석에서 분산적 약점이 있다는 분석과 일치

### 외부 API·플랫폼 의존성

- PRD §12-2에서 외부 의존성 5개를 식별하고 각각 대안을 명시
- Supabase 장애 시 오프라인 모드 대응(§7-3)이 구체적으로 설계됨
- RevenueCat 대안으로 react-native-iap 직접 구현 명시

---

## C. 기술 검증 — **통과**

### React Native + TypeScript + Supabase 구현 가능성

모든 P0 기능이 해당 스택으로 구현 가능하다. 외부 AI/ML 의존 없이 PostgreSQL SQL 집계로 패턴 분석을 구현하는 접근은 1인 개발에 적합하다.

### project-init 기술 스택 준수

§7-1 기술 스택 테이블에서 project-init 고정 스택(Expo Router, Zustand, MMKV, op-sqlite, React Native Paper)을 명시적으로 채택. 조건부 의존성(RevenueCat, expo-notifications, Aptabase 등)도 정확히 식별됨.

### Windows 11 + EAS Build 환경

macOS 필수 도구나 iOS 전용 API 의존이 없다. §12-4에서 project-init 조건부 매핑 미지원 항목(expo-sharing)을 별도 식별.

### 1인 개발 규모 적절성

- 3개월 12주 주별 마일스톤(§14-1)이 현실적으로 구성됨
- P2 기능(분기별 트렌드, PDF 내보내기)은 MVP에서 명시적으로 제외(§13)

### DB 스키마·API 정합성

- 9개 테이블 정의, FK 관계, 인덱스, RLS 정책이 완전히 작성됨 (§7-5)
- 6개 핵심 DB Function(RPC)의 **완전한 SQL 본문**이 작성됨 (§7-5-5)
- API 설계가 PostgREST REST(18개) / RPC(4개) / Edge Function(4개)으로 3계층 분리
- TypeScript 인터페이스 20개가 완비 (§7-6-3)
- 기능-테이블-API 매핑표(§7-8)에서 모든 P0 기능이 매핑됨

### 오프라인 우선 설계

- MMKV + op-sqlite 역할 분담(§7-3-1, §7-3-2)이 데이터 특성에 맞게 설계됨
- 동기화 큐 로직(§7-3-3)이 상태 머신(pending→synced→failed)으로 정의, 지수 백오프 재시도 명시
- 충돌 해결은 Server Wins 정책(§7-3-4) — 1인 사용자 앱에서 합리적

### 상태 관리 구조

- 9개 Zustand Store(§7-7)가 역할별로 정의, 각 Store의 주요 상태 필드와 persist 전략이 명시

### 5차 리뷰 잔존 Minor 수정 확인

| 5차 지적 | 수정 여부 | 확인 |
|-----------|----------|------|
| C-3 (Minor): RPC 1·2의 implicit/explicit JOIN 혼합 (`FROM el, UNNEST(...)` 패턴) | **수정됨** | §7-5-5 RPC 1(get_weekly_summary)의 emotion_distribution 서브쿼리, RPC 2(get_monthly_pattern_report)의 day_of_week_distribution, hour_distribution, activity_emotion_correlation, daily_trend 모든 서브쿼리가 `CROSS JOIN LATERAL UNNEST`로 explicit JOIN 통일. RPC 4(get_calendar_summary)와 동일한 패턴으로 전체 일관성 확보 |

### 잔존 Minor (신규 발견)

**없음.** 6개 RPC 전체에서 JOIN 패턴이 일관되게 통일되었다.

---

## D. 문서 품질 검증 — **통과**

### 목표·기능·기술 명세 간 모순

- Executive Summary(§1)의 "월 3,900원"과 §10-2의 가격 구조가 일치
- User Story 26개(§4)와 Functional Requirements 24개(§5-1)가 상호 참조됨
- 모든 P0 FR이 매핑표(§7-8)에서 테이블·API에 매핑됨
- PRD v1.2의 자체 점검(§7-9) 6번 항목에서 "RPC 1·2의 implicit/explicit JOIN 혼합을 CROSS JOIN LATERAL UNNEST로 통일"이 실제 SQL 본문과 일치
- **모순 없음**

### 섹션 완전성 (16개 섹션)

| # | 섹션 | 존재 여부 |
|---|------|----------|
| 1 | Executive Summary | O (§1) |
| 2 | Problem Statement | O (§2) |
| 3 | Target Users & Personas | O (§3, 3개 페르소나) |
| 4 | User Stories | O (§4, 7개 Epic, 26개 Story) |
| 5 | Functional Requirements | O (§5, 24개 FR + AC) |
| 6 | Non-functional Requirements | O (§6, 성능/보안/접근성/확장성) |
| 7 | Technical Architecture | O (§7, 스택/구조도/오프라인/DB/API/상태관리/매핑표) |
| 8 | Screen Map & UI 명세 | O (§8, 네비게이션 트리 + 9개 화면 UI) |
| 9 | Competitive Differentiation | O (§9, 차별화 테이블/메시지/Moat/라일리 하루 대비) |
| 10 | Monetization Strategy | O (§10, Freemium/가격/전환전략/수익전망) |
| 11 | Risk Matrix | O (§11, 10개 리스크) |
| 12 | Assumptions & Constraints | O (§12, 가정/의존성/제약/수동설치항목) |
| 13 | Out of Scope | O (§13, 10개 제외 항목) |
| 14 | MVP Roadmap | O (§14, 12주 주별 마일스톤 + v1.1 계획) |
| 15 | Success Metrics | O (§15, KPI/North Star/측정 인프라) |
| 16 | Glossary (용어 사전) | O (§16, 33개 용어) |

**16/16개 섹션 존재. 완전.**

### P0 기능 수용 기준 (AC)

- 17개 P0 기능 모두에 AC-XX-X 형식의 수용 기준 존재
- 총 AC 항목: 약 50개, 모두 검증 가능한 조건문 형태
- **충족**

### User Story 커버리지

- 7개 Epic: 온보딩/인증(3), 감정기록(6), 리포트(4), 셀프케어(4), 설정/프로필(4), 결제/구독(3), 공유/데이터(2) = **총 26개**
- 모든 기능 영역 커버 (최소 15개 기준 초과)
- **충족**

### Screen Map & 화면별 UI 구성

- §8-1: Expo Router 파일 기반 네비게이션 구조 트리
- §8-2: 9개 핵심 화면별 UI 구성 (로그인, 온보딩, 홈, 감정기록, 셀프케어, 리포트, 설정, 구독, 기록상세)
- **충족**

### 기능-테이블-API 매핑표

- §7-8: 17개 P0 기능 모두 매핑 완료
- **충족**

---

## 미달 목록

| # | 영역 | 심각도 | 유형 | 내용 |
|---|------|--------|------|------|
| — | — | — | — | **Critical/Major/Minor 미달 사항 없음** |

---

## 최종 판정: **통과**

### 판정 근거

- 5차 리뷰에서 지적된 Minor 1건(C-3)이 **수정됨**
  - C-3: RPC 1(get_weekly_summary)·RPC 2(get_monthly_pattern_report)의 모든 서브쿼리가 `CROSS JOIN LATERAL UNNEST`로 explicit JOIN 통일. 6개 RPC 전체에서 JOIN 패턴 일관성 확보
- 4개 검증 영역 모두 **통과**: 사업성(A), 리스크(B), 기술(C), 문서 품질(D)
- 신규 Minor 발견 없음
- Critical/Major 미달 사항 없음 → 통과 조건 충족

### CLAUDE.md Task Master 투입 요건 충족 여부

| 요건 | 충족 여부 | 근거 |
|------|----------|------|
| 기능 명세 (P0/P1/P2 우선순위, 입력/출력/제한사항) | **충족** | §5-1: 24개 FR 완비 |
| 수용 기준 (AC-XX-X 형식, 검증 가능한 조건문) | **충족** | §5-2: 17개 P0 기능 모두 AC 작성, ~50개 조건문 |
| DB 스키마 (CREATE TABLE, FK, 인덱스, RLS, DB Function SQL) | **충족** | §7-5: 9개 테이블 DDL, 6개 RPC 완전 SQL 본문 |
| API 설계 (엔드포인트, TypeScript 인터페이스, 비즈니스 로직) | **충족** | §7-6: REST 18개 + RPC 4개 + Edge Function 4개, TS 인터페이스 20개 |
| 아키텍처 상세도 (기술 스택, 시스템 구조도, 상태 관리, 매핑표) | **충족** | §7-1~§7-8 |
| 화면 구조 / UI 명세 (네비게이션 트리, 핵심 화면 UI) | **충족** | §8-1~§8-2 |
| 오프라인/동기화 설계 (로컬 저장소, 동기화 큐, 충돌 해결) | **충족** | §7-3 |

**Task Master 투입 요건 7/7 전체 충족.**

### 리뷰 이력

| 차수 | PRD 버전 | 미달 | 결과 |
|------|---------|------|------|
| 1~3차 | v1.0 | 다수 Critical/Major | 3단계 회귀 |
| 4차 | v1.0→v1.1 | Minor 2건 (C-1, C-2) | 3단계 회귀 |
| 5차 | v1.1 | Minor 1건 (C-3) | 통과 (C-3 수정 위해 3단계 회귀) |
| **6차** | **v1.2** | **없음** | **통과** |
