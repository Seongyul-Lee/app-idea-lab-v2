# App Idea Lab v2

## 목적
한국 시장 타겟 1인 개발 모바일 앱 아이디어 발굴, 검증, 평가, PRD 작성, 출시, 성장까지의 전체 라이프사이클 관리

## 프로젝트 역할
이 프로젝트는 **코드 개발을 하지 않는다.** 앱 아이디어의 발굴부터 출시 후 성장 전략까지 아래 파이프라인을 수행한다.

## 전체 파이프라인

```
Stage 1         Stage 1V        Stage 2         Stage 3         Stage 4
아이디어 발굴 →  시장 검증    →  사업성 평가  →  PRD 작성    →  PRD 정합성 검증
/stage-1        /stage-1v       /stage-2        /stage-3        /stage-4
                  │                                               │
            ┌─────┤                                     ┌─────────┤
            ▼     ▼                                     ▼         ▼
          NO-GO  GO/CONDITIONAL                     adopted/   rejected/
          (→ /stage-1 재실행)                           │
                                            ┌───────────┘
                                            ▼
                                      project-init-v2
                                      /init-scaffold → /init-docs
                                            │
                                            ▼
                                      실제 MVP 개발
                                            │
                                            ▼
                                      Stage 5           Stage 6
                                      출시 + ASO    →   성장 전략
                                      /stage-5          /stage-6
                                            │               │
                                            └───────┬───────┘
                                                    ▼
                                              /monthly-review
                                              (매월 반복)
```

| 단계 | 커맨드 | 설명 | 산출물 |
|------|--------|------|--------|
| Stage 1 | `/stage-1` | 아이디어 발굴 | `NNN-아이디어명.md` |
| **Stage 1V** | `/stage-1v` | 발굴된 아이디어의 시장 수요 검증 | `NNN-아이디어명-validation.md` |
| Stage 2 | `/stage-2` | 사업성/기술/경쟁력 평가 | 시장분석, 경쟁분석 문서 |
| Stage 3 | `/stage-3` | PRD 문서 작성 | `NNN-아이디어명-prd.md` |
| Stage 4 | `/stage-4` | PRD 정합성 판별 | `NNN-아이디어명-review.md` + 채택/탈락 판정 |
| **Stage 5** | `/stage-5` | 스토어 출시 + ASO 최적화 | `NNN-아이디어명-launch.md` |
| **Stage 6** | `/stage-6` | 초기 성장 전략 실행 | `NNN-아이디어명-growth.md` |
| **Monthly Review** | `/monthly-review` | 포트폴리오 성과 분석 + 유지/피벗/중단 판단 | `YYYY-MM-review.md` |

### Stage 1V 판정 기준
- **GO** (70점 이상): Stage 2로 진행
- **CONDITIONAL** (40~69점): 보완 후 재검증 또는 조건부 진행
- **NO-GO** (39점 이하): `/stage-1`로 돌아가 새 아이디어 발굴

### Stage 4 판정 결과
- 채택 → `ideas/adopted/`
- 탈락 → `ideas/rejected/` (사유 기록 필수)
- 탈락 처리는 반드시 사용자의 명시적 동의를 받은 후에만 실행

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

## 프로젝트 구조

```
app-idea-lab-v2/
├── .claude/
│   └── commands/              # 슬래시 커맨드
│       ├── stage-1.md         # 아이디어 발굴
│       ├── stage-1v.md        # 시장 검증 (Stage 1 → 2 사이)
│       ├── stage-2.md         # 사업성 평가
│       ├── stage-3.md         # PRD 작성
│       ├── stage-4.md         # PRD 정합성 검증
│       ├── stage-5.md         # 출시 + ASO (MVP 개발 완료 후)
│       ├── stage-6.md         # 성장 전략 (출시 후)
│       └── monthly-review.md  # 월간 포트폴리오 리뷰
├── ideas/
│   ├── _template.md           # 아이디어 카드 템플릿
│   ├── adopted/               # 채택된 아이디어 + PRD + 리뷰
│   └── rejected/              # 탈락된 아이디어
├── research/
│   ├── competitors/           # 경쟁 분석 문서
│   └── market-data/           # 시장 분석 문서
├── validation/                # Stage 1V 검증 결과
│   ├── _template.md           # 검증 보고서 템플릿
│   └── channels.md            # 검증 채널별 가이드
├── launch/                    # Stage 5 출시 관련 참조 문서
│   ├── aso-checklist.md       # ASO 체크리스트
│   └── store-assets-guide.md  # 스토어 에셋 가이드
├── growth/                    # Stage 6 성장 전략 참조 문서
│   ├── playbook.md            # 채널별 성장 플레이북
│   └── community-marketing.md # 커뮤니티 마케팅 전술
├── evaluation/
│   ├── criteria.md            # 평가 기준 (Stage 2)
│   ├── monthly-kpi.md         # 월간 KPI 추적 템플릿
│   └── portfolio-decision.md  # 유지/피벗/중단 판단 프레임워크
├── CLAUDE.md                  # 프로젝트 지침
└── README.md
```

## 연관 프로젝트: project-init-v2

### 관계
`~/project-init-v2`은 본 프로젝트에서 채택(adopted)된 PRD 문서를 기반으로, 기술 스택에 맞는 프로젝트를 자동 생성·초기화하는 워크플로우 도구다. 즉, app-idea-lab-v2의 **산출물(채택된 PRD)**이 project-init-v2의 **입력**이 된다.

### 동기화 필수 항목
아래 섹션이 변경되면 `~/project-init-v2/CLAUDE.md`의 대응 섹션도 반드시 함께 수정해야 한다.

| app-idea-lab-v2 섹션 | project-init-v2 대응 섹션 | 비고 |
|---|---|---|
| 기술 스택 제약 | 기술 스택 (고정), 공통 의존성, 조건부 의존성 매핑 | 스택 추가/제거 시 의존성 목록도 갱신 |
| 개발 환경 | 개발 환경, 경로 상수 | OS, 빌드 환경 변경 시 |
| 타겟 플랫폼 | 타겟 플랫폼 | 플랫폼 변경 시 scaffold 로직에 영향 |
| PRD의 최종 목표 / Task Master 투입 요건 | 생성할 CLAUDE.md 골격, 생성할 KNOWLEDGE.md 골격 | PRD 구조 변경 시 템플릿 갱신 |
| 전체 파이프라인 (Stage 1V/5/6) | 연관 프로젝트 섹션, 파이프라인 위치 설명 | 단계 추가/변경 시 |

## 아이디어 문서 규칙
- 템플릿: `ideas/_template.md`를 복사하여 작성
- 파일명 규칙:
  - 아이디어 카드: `NNN-아이디어명.md` (예: `009-복약케어.md`)
  - 검증 보고서: `NNN-아이디어명-validation.md` (예: `009-복약케어-validation.md`)
  - PRD: `NNN-아이디어명-prd.md` (예: `009-복약케어-prd.md`)
  - 리뷰: `NNN-아이디어명-review.md` (예: `009-복약케어-review.md`)
  - 출시 계획: `NNN-아이디어명-launch.md` (예: `009-복약케어-launch.md`)
  - 성장 전략: `NNN-아이디어명-growth.md` (예: `009-복약케어-growth.md`)
  - 월간 리뷰: `YYYY-MM-review.md` (예: `2026-02-review.md`)
- 산출물 저장 위치:
  - 검증 보고서 → `validation/`
  - 아이디어/PRD/리뷰 → `ideas/` (채택 시 `adopted/`, 탈락 시 `rejected/`)
  - 출시 산출물 → `launch/`
  - 성장 산출물 → `growth/`
  - 월간 리뷰 → `evaluation/`
- 탈락 아이디어도 반드시 판정 사유를 기록
