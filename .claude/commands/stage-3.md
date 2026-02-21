## 3단계: PRD 문서 작성

대상 아이디어: $ARGUMENTS

## 목적
채택된 아이디어에 대해 구체적인 제품 요구사항 문서를 작성한다. **작성된 PRD는 Task Master에 투입하여 즉시 개발 작업으로 분해할 수 있는 수준의 상세도를 갖추어야 한다.**

## 입력
- `ideas/NNN-아이디어명.md` — 평가 완료 아이디어 문서
- `research/competitors/NNN-경쟁분석.md` — 경쟁사 분석
- `research/market-data/NNN-시장분석.md` — 시장 분석
- `research/market-data/NNN-트렌드메모.md` — 트렌드 분석
- `validation/NNN-아이디어명-validation.md` — Stage 1V 시장 검증 카드 (있을 경우)
- `ideas/NNN-아이디어명-review.md` — (3단계 회귀 시) 4단계 판별 보고서
- `ideas/NNN-아이디어명-prd.md` — (3단계 회귀 시) 기존 PRD 문서

## 사용 도구
| 도구 | 역할 |
|------|------|
| Skill: `sc:design` | Technical Architecture 섹션(시스템 구조, DB 스키마, API 설계, 오프라인 설계) 작성 |

## 실행 워크플로우
아래 단계를 순서대로 **모두** 실행한다. 어떤 단계도 생략하지 않는다.

### Step 1: 입력 문서 읽기
- 입력 섹션의 모든 파일을 읽는다 (존재하지 않는 파일은 건너뛴다)
- `ideas/NNN-아이디어명-review.md`가 존재하면 **반드시** 읽고, 수정 지침(수정 대상·결함 내용·수정 방향)을 파악한다

### Step 2: 회귀 여부 판단
- **review 파일이 존재하고 "3단계 회귀" 판정인 경우 → 기존 PRD 수정 모드**
  - 기존 `ideas/NNN-아이디어명-prd.md`를 읽는다
  - review 보고서의 수정 지침에 해당하는 섹션만 수정·보완한다
  - 수정 완료 후 **Step 6(자체 점검)으로 이동**한다
- **review 파일이 없는 경우 → 신규 작성 모드** (Step 3부터 순서대로 진행)

### Step 3: 섹션 1~6 작성
- 아래 "PRD 작성 구조"의 섹션 1~6을 작성한다

### Step 4: sc:design 호출 → 섹션 7 작성
- 아래 "sc:design 호출 지침"에 따라 `sc:design`을 1회 호출한다
- 출력물을 PRD 섹션 7(Technical Architecture)에 통합한다

### Step 5: 섹션 8~16 작성
- sc:design 출력을 섹션 7에 통합한 **직후**, 아래 섹션을 **연속으로** 작성한다
- **중단하거나 사용자 확인을 기다리지 않는다**
  - 8\. Screen Map & UI 명세
  - 9\. Competitive Differentiation
  - 10\. Monetization Strategy
  - 11\. Risk Matrix
  - 12\. Assumptions & Constraints
  - 13\. Out of Scope
  - 14\. MVP Roadmap
  - 15\. Success Metrics
  - 16\. Glossary (용어 사전)

### Step 6: 자체 점검
- 아래 "작성 완료 후 자체 점검" 항목을 **모두** 대조 확인한다
- 미충족 항목이 있으면 해당 섹션을 보완한 뒤 완료 처리한다

## PRD 작성 구조
아래 16개 섹션으로 PRD를 작성한다. 각 섹션에서 2단계 산출물(시장분석·경쟁분석)의 데이터를 근거로 인용한다.

1. **Executive Summary** — 핵심 가치 제안 한 문단
2. **Problem Statement** — 2단계 시장분석 기반, 구체적 pain point 기술
3. **Target Users & Personas** — 주요 사용자 세그먼트 2-3개, 각 페르소나 정의
4. **User Stories** — Epic별로 분류하여 작성한다. Epic당 2-5개 Story, **전체 최소 15개 이상**. `As a [사용자], I want [기능], so that [가치]` 형식. 온보딩, 인증, 핵심 기능, 설정, 결제 등 **모든 기능 영역을 빠짐없이** 포함한다.
5. **Functional Requirements** — MVP 범위의 기능 목록, 우선순위(P0/P1/P2) 명시. 아래 규칙을 반드시 준수한다:
   - **모든 P0 기능에 번호 형식(AC-XX-X)의 수용 기준(Acceptance Criteria)을 기술한다**
   - 수용 기준은 **검증 가능한 조건문** 형태여야 한다 (예: "감정 태그 1~3개 선택 후 저장 시 1초 이내 응답")
   - 기능 명세 테이블에 입력/출력/제한사항/무료-유료 차이를 포함한다
6. **Non-functional Requirements** — 성능, 보안, 접근성, 확장성
7. **Technical Architecture** — `sc:design`을 1회 호출하여 작성. Expo Managed Workflow + React Native + TypeScript + Supabase 기준으로 아래 **모든 하위 항목**을 포함한다:
   - **7-1. 기술 스택**: 레이어별(프론트엔드, 상태관리, 로컬저장, 백엔드, 결제, 알림, 분석, 빌드) 기술 선택 및 선정 사유
   - **7-2. 시스템 구조도**: 텍스트 기반 아키텍처 다이어그램, 컴포넌트 간 데이터 흐름 표현
   - **7-3. 오프라인 우선 설계**: 로컬 저장소 선택 및 근거(고정 스택: MMKV for KV, op-sqlite for DB), 동기화 큐 로직(재시도 횟수, 백오프 전략), 충돌 해결 정책(Server Wins 등), 저장소별 역할 분담(어떤 데이터를 어디에 저장하는지 명시)
   - **7-4. 핵심 설계 원칙**: 아키텍처 결정의 근거 3-5개
   - **7-5. DB 스키마**: 모든 테이블의 **`CREATE TABLE` SQL문**을 작성한다. 컬럼·타입·제약조건·FK·인덱스를 포함한다. RLS 정책을 **테이블별 CRUD 권한 매트릭스**로 정리한다. **핵심 DB Function(RPC)은 시그니처만이 아닌 완전한 SQL 본문을 작성한다.**
   - **7-6. API 설계**: Supabase REST vs Edge Function 선택 기준을 명시한다. 모든 엔드포인트(경로·메서드·설명·구현방식)를 나열한다. **핵심 요청/응답에 대한 TypeScript 인터페이스를 작성한다.** Edge Function은 내부 비즈니스 로직(알고리즘 단계, 검증 로직, 외부 API 호출 흐름)을 서술한다.
   - **7-7. 상태 관리 구조**: Zustand Store 목록 및 각 Store의 주요 상태 필드와 역할을 정의한다.
   - **7-8. 기능-테이블-API 매핑표**: P0 기능 요구사항 각각이 어떤 테이블·API에 대응하는지 대조표 작성
   - **7-9. 자체 점검 결과**: 아래 "작성 완료 후 자체 점검" 항목의 점검 결과를 표로 기록
8. **Screen Map & UI 명세** — 아래 항목을 모두 포함한다:
   - **네비게이션 구조 트리**: Auth Stack, Main Tab Navigator, Modal 등 전체 화면 계층을 트리 형태로 표현
   - **핵심 화면별 UI 구성**: 각 화면의 주요 UI 요소(상단/중앙/하단 구성, 컴포넌트, CTA 버튼 등)를 텍스트로 설명
   - 최소 온보딩/홈/핵심 기능/리포트/설정 화면을 포함한다
   - 각 화면에서 사용하는 데이터와 호출하는 API를 명시한다
9. **Competitive Differentiation** — 2단계 경쟁분석 기반 차별화 전략
10. **Monetization Strategy** — 수익 모델 구체화
11. **Risk Matrix** — 사업·기술·법률·운영 리스크 및 대응 방안, 의존성 리스크
12. **Assumptions & Constraints** — 사업적 가정, 외부 의존성, 기술 제약 명시
13. **Out of Scope** — MVP에서 명시적으로 제외하는 기능·영역
14. **MVP Roadmap** — 3개월 일정, 주별 마일스톤
15. **Success Metrics** — 핵심 지표(사용자/비즈니스/제품) 정의, 측정 방법 명시
16. **Glossary (용어 사전)** — 도메인 특화 용어와 정의. `용어 — 정의` 형식으로 작성. 최소 10개 이상

### 기술 설계 제약
- Expo Managed Workflow + React Native + TypeScript + Supabase(PostgreSQL) 전용
- 개발 환경: Windows 11 — 개발·디버깅은 Android 에뮬레이터, iOS 빌드는 EAS Build(클라우드), iOS는 배포 전 실기기 최종 검증
- 1인 개발 규모에 적합한 설계
- 3개월 MVP 출시 가능한 범위로 한정

### project-init 고정 기술 스택 (필수 준수)
PRD의 Section 7-1 기술 선택은 아래 고정 스택을 **반드시 따라야 한다.** 이 목록은 project-init(프로젝트 초기화 도구)이 scaffold 시 항상 설치하는 패키지이며, PRD가 다른 기술을 선택하면 scaffold와 불일치가 발생한다.

| 레이어 | 고정 기술 | 비고 |
|---|---|---|
| 프레임워크 | Expo Managed Workflow (SDK 최신 안정 버전) | |
| 언어 | TypeScript (strict mode) | |
| 라우팅 | Expo Router (파일 기반) | React Navigation 사용 금지 |
| UI | React Native Paper | Tamagui, NativeBase 등 사용 금지 |
| 상태 관리 | Zustand | Jotai, Redux Toolkit 등 사용 금지 |
| 백엔드 | Supabase (PostgreSQL + Auth + Edge Functions + Storage) | |
| 로컬 KV | react-native-mmkv | AsyncStorage 사용 금지 |
| 로컬 DB (필요 시) | op-sqlite | WatermelonDB 사용 금지 (1인 개발 규모 부적합) |

### project-init 조건부 의존성 (선택 가능 범위)
PRD가 아래 기술을 필요로 하는 경우, **이 테이블에 있는 기술만** 선택해야 한다. 테이블에 없는 기술을 선택하면 scaffold가 해당 패키지를 설치하지 못한다.

| 영역 | 사용 가능한 기술 |
|---|---|
| 인증 | Kakao OAuth, Google OAuth, Apple Auth, Naver OAuth |
| 알림 | expo-notifications (Push Notification) |
| 분석 | Firebase Analytics, Aptabase |
| 결제 | react-native-iap, RevenueCat |
| 차트 | Gifted Charts (SVG 기반), Victory Native (Skia 기반) |
| UI 컴포넌트 | react-native-calendars, DateTimePicker, react-native-svg |
| 미디어/디바이스 | expo-image-picker, expo-camera, expo-haptics |
| 애니메이션 | Lottie |

> **이 테이블에 없는 기술이 반드시 필요한 경우**, PRD Section 12 (Assumptions & Constraints)에 "project-init 조건부 매핑 미지원 — 수동 설치 필요" 항목으로 명시한다.

### sc:design 호출 지침
- 호출 시점: 섹션 1~6 작성 완료 후
- **제약 전달 우선순위**: sc:design 호출 시 아래 제약 블록을 **프롬프트 최상단**에 배치하여, 설계 전 과정에서 제약이 누락되지 않도록 한다.
- 입력 컨텍스트: **기술 설계 제약 + project-init 고정 기술 스택 + 조건부 의존성 테이블 + 사용 금지 목록** → Functional Requirements(P0/P1) → Non-functional Requirements 순서로 전달
- **sc:design에 아래 제약을 명시적으로 전달한다:**
  - "Section 7-1 기술 선택은 위의 '고정 기술 스택' 테이블을 따를 것"
  - "조건부 기술은 '조건부 의존성' 테이블에 있는 기술 중에서 선택하고, 테이블의 **정확한 기술명**(예: 'Gifted Charts (SVG 기반)', 'Victory Native (Skia 기반)')을 그대로 사용할 것"
  - "개발 환경은 Windows 11이며, iOS 빌드는 EAS Build(클라우드), 개발·디버깅은 Android 에뮬레이터 기준으로 설계할 것"
  - "**사용 금지**: React Navigation, Tamagui, NativeBase, Jotai, Redux Toolkit, AsyncStorage, WatermelonDB — 이 기술들은 어떤 경우에도 선택하지 않는다"
- 출력: 기술 스택, 시스템 구조도, 오프라인 설계, DB 스키마(CREATE TABLE SQL 포함), API 설계(TypeScript 인터페이스 포함), 상태 관리 구조, 기능-테이블-API 매핑표
- **출력물은 반드시 PRD 문서의 섹션 7 본문에 직접 작성한다. 별도의 Technical Architecture 파일을 생성하지 않는다.**

### 작성 완료 후 자체 점검
PRD 초안 완성 후, 아래 항목을 **모두** 대조 확인한다. 미충족 항목이 있으면 해당 섹션을 보완한 뒤 완료 처리한다.

| # | 점검 항목 | 확인 대상 |
|---|----------|----------|
| 1 | 모든 P0 기능에 번호 형식(AC-XX-X)의 수용 기준이 존재하는가 | 섹션 5 |
| 2 | P0 기능별 DB 테이블·API 매핑이 매핑표에 존재하는가 | 섹션 7-8 |
| 3 | DB 스키마의 FK 관계가 기능 간 데이터 흐름과 일치하는가 | 섹션 7-5, 7-8 |
| 4 | API 엔드포인트가 각 P0 수용 기준을 충족할 수 있는가 | 섹션 7-6, 5 |
| 5 | 핵심 API의 TypeScript 인터페이스(요청/응답)가 존재하는가 | 섹션 7-6 |
| 6 | 핵심 DB Function의 SQL 본문이 완성되었는가 (스텁이 아닌지) | 섹션 7-5 |
| 7 | 오프라인 저장소·동기화·충돌 해결 정책이 명시되었는가 | 섹션 7-3 |
| 8 | 네비게이션 구조 트리 + 핵심 화면 UI 설명이 존재하는가 | 섹션 8 |
| 9 | User Story가 모든 기능 영역(온보딩·인증·핵심·설정·결제)을 커버하는가 | 섹션 4 |
| 10 | Section 7-1 기술 선택이 고정 스택을 준수하고, 조건부 기술이 허용 목록 내이며, 사용 금지 기술이 포함되지 않았는가 | 섹션 7-1 |

## 산출물
| 파일 | 위치 |
|------|------|
| PRD 문서 | `ideas/NNN-아이디어명-prd.md` |

> **이 단계의 산출물은 위 PRD 파일 1개뿐이다. Technical Architecture를 포함한 모든 내용은 PRD 문서 내 해당 섹션에 작성하며, 별도 파일을 생성하지 않는다.**

## Frontmatter 업데이트

PRD 작성 완료 후 아래 2개 파일의 frontmatter를 채운다.

1. **PRD** (`ideas/NNN-아이디어명-prd.md`):
   - `id`, `name`: 해당 값
   - `type`: `"prd"`
   - `version`: `"v1.0"` (회귀로 인한 재작성 시 버전 증가 — 예: `"v1.1"`, `"v2.0"`)
   - `date`: 오늘 날짜

2. **아이디어 카드** (`ideas/NNN-아이디어명.md`):
   - `current_stage`: `"stage-3"`
   - `updated_at`: 오늘 날짜

## 전환
- PRD 전체 섹션 작성 완료, 사용자 확인 → "4단계: `/stage-4 NNN-아이디어명`" 안내
- 작성 중 기술적 실현 불가 판단 → "2단계 회귀: `/stage-2 NNN-아이디어명`" 안내
