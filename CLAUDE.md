# App Idea Lab

## 목적
한국 시장 타겟 1인 개발 모바일 앱 아이디어 발굴 및 평가

## 프로젝트 역할
이 프로젝트는 **코드 개발을 하지 않는다.** 앱 아이디어 발굴 → 평가 → PRD 작성 → 검증·판정의 4단계를 수행한다.

## 문서 작성 언어
- 모든 문서는 한국어로 작성

## 기술 스택 제약
- **프레임워크**: Expo Managed Workflow (SDK 최신 안정 버전)
- **언어**: TypeScript (strict mode)
- **라우팅**: Expo Router (파일 기반)
- **UI**: React Native Paper
- **상태 관리**: Zustand
- **백엔드**: Supabase (PostgreSQL + Auth + Edge Functions + Storage)
- **로컬 KV**: react-native-mmkv (경량 키-값)
- **로컬 DB (필요 시)**: op-sqlite (구조화 데이터, PRD에서 명시한 경우 조건부 설치)
- 외부 파트너십 없이 혼자 구현 가능해야 함

## 타겟 플랫폼
- iOS / Android 모두 배포

## 개발 환경
- OS: Windows 11
- 개발·디버깅: Android 에뮬레이터 + Expo DevTools + Chrome Remote Debugger
- iOS 빌드: EAS Build (클라우드)
- iOS 검증: 배포 전 실기기로 최종 검증

## 평가 우선순위
1. 1-3개월 내 MVP 출시 가능 여부
2. 한국 로컬 시장의 실제 pain point
3. 기존 스택으로 구현 가능한지

## 제외 기준
아래에 해당하는 아이디어는 조사 대상에서 제외한다.
- 법적 인허가가 필수이거나 규제가 까다로운 영역 (의료, 금융 등)
- 기업/기관과의 협약이 필수인 아이디어
- 1인 개발로 구현이 불가능한 아이디어 (예: 다중 쇼핑몰 매출/재고 관리 서비스 — 수십~수백 개 API 연동 및 기업 협약 필요)
- 기술 스택 부적합: RN + Supabase로 구현 불가, macOS 빌드 환경 필수, 또는 플랫폼 전용 API(HealthKit, ARKit 등)에 핵심이 의존하는 아이디어

## PRD의 최종 목표
이 프로젝트에서 채택(adopted)된 PRD 문서는 **Task Master(또는 동등한 태스크 분해 도구)에 투입하여 즉시 개발 작업으로 분해할 수 있는 수준**이어야 한다. 즉, PRD만으로 별도의 추가 설계 없이 개발 태스크를 생성하고 착수할 수 있어야 한다.

### Task Master 투입 요건
채택된 PRD는 아래 항목을 모두 포함해야 한다:
- **기능 명세**: P0/P1/P2 우선순위, 입력/출력/제한사항 정의
- **수용 기준(AC)**: 모든 P0 기능에 AC-XX-X 형식의 검증 가능한 조건문
- **DB 스키마**: CREATE TABLE SQL, FK, 인덱스, RLS 정책, 핵심 DB Function SQL 본문
- **API 설계**: 전체 엔드포인트, TypeScript 인터페이스(요청/응답), 비즈니스 로직 서술
- **아키텍처 상세도**: 기술 스택, 시스템 구조도, 상태 관리 구조, 기능-테이블-API 매핑표
- **화면 구조 / UI 명세**: 네비게이션 구조 트리, 핵심 화면별 UI 구성, 화면-데이터-API 매핑
- **오프라인/동기화 설계**: 로컬 저장소 선택, 동기화 큐 로직, 충돌 해결 정책

이 요건은 Stage 3(PRD 작성)에서 작성하고, Stage 4(정합성 판별)에서 검증한다.

## 연관 프로젝트: project-init

### 관계
`~/project-init`은 본 프로젝트에서 채택(adopted)된 PRD 문서를 기반으로, 기술 스택에 맞는 프로젝트를 자동 생성·초기화하는 워크플로우 도구다. 즉, app-idea-lab의 **산출물(채택된 PRD)**이 project-init의 **입력**이 된다.

### 동기화 필수 항목
아래 섹션이 변경되면 `~/project-init/CLAUDE.md`의 대응 섹션도 반드시 함께 수정해야 한다.

| app-idea-lab 섹션 | project-init 대응 섹션 | 비고 |
|---|---|---|
| 기술 스택 제약 | 기술 스택 (고정), 공통 의존성, 조건부 의존성 매핑 | 스택 추가/제거 시 의존성 목록도 갱신 |
| 개발 환경 | 개발 환경, 경로 상수 | OS, 빌드 환경 변경 시 |
| 타겟 플랫폼 | 타겟 플랫폼 | 플랫폼 변경 시 scaffold 로직에 영향 |
| PRD의 최종 목표 / Task Master 투입 요건 | 생성할 CLAUDE.md 골격, 생성할 KNOWLEDGE.md 골격 | PRD 구조 변경 시 템플릿 갱신 |

## 판정 결과 처리
채택 시 `ideas/adopted/`, 탈락 시 `ideas/rejected/`로 이동. 탈락 처리는 반드시 사용자의 명시적 동의를 받은 후에만 실행한다.

## 아이디어 문서 규칙
- 템플릿: `ideas/_template.md`를 복사하여 작성
- 파일명 규칙:
  - 아이디어 카드: `NNN-아이디어명.md` (예: `009-복약케어.md`)
  - PRD: `NNN-아이디어명-prd.md` (예: `009-복약케어-prd.md`)
  - 리뷰: `NNN-아이디어명-review.md` (예: `009-복약케어-review.md`)
- 채택 시 `ideas/adopted/`에, 탈락 시 `ideas/rejected/`에 이동
- 탈락 아이디어도 반드시 판정 사유를 기록
