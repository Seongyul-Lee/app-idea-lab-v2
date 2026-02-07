<a id="readme-top"></a>

# App Idea Lab v2

한국 시장 타겟 1인 개발 모바일 앱 아이디어를 발굴하고, 평가하고, PRD로 정제하는 문서 기반 워크플로우 프로젝트.

<!-- TABLE OF CONTENTS -->
<details>
  <summary>목차</summary>
  <ol>
    <li><a href="#프로젝트-소개">프로젝트 소개</a></li>
    <li><a href="#워크플로우">워크플로우</a></li>
    <li><a href="#프로젝트-구조">프로젝트 구조</a></li>
    <li><a href="#시작하기">시작하기</a></li>
    <li><a href="#사용법">사용법</a></li>
    <li><a href="#기술-스택-제약">기술 스택 제약</a></li>
    <li><a href="#평가-기준">평가 기준</a></li>
    <li><a href="#연관-프로젝트">연관 프로젝트</a></li>
  </ol>
</details>

## 프로젝트 소개

이 프로젝트는 **코드 개발을 하지 않는다.** 앱 아이디어의 발굴부터 PRD 검증까지 4단계 파이프라인을 통해, 즉시 개발 착수 가능한 수준의 PRD 문서를 산출하는 것이 목표다.

**핵심 원칙:**
- 1인 개발자가 1-3개월 내 MVP 출시 가능한 아이디어만 대상
- 한국 로컬 시장의 실제 pain point 해결에 집중
- 채택된 PRD는 Task Master에 투입하여 별도 설계 없이 개발 태스크로 분해 가능해야 함

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 워크플로우

4단계 파이프라인으로 운영되며, 각 단계는 Claude Code 슬래시 커맨드로 실행한다.

| 단계 | 커맨드 | 설명 | 산출물 |
|:---:|---|---|---|
| 1 | `/stage-1` | 아이디어 발굴 | `NNN-아이디어명.md` |
| 2 | `/stage-2` | 사업성/기술/경쟁력 평가 | 시장분석, 경쟁분석 문서 |
| 3 | `/stage-3` | PRD 문서 작성 | `NNN-아이디어명-prd.md` |
| 4 | `/stage-4` | PRD 정합성 판별 | `NNN-아이디어명-review.md` + 채택/탈락 판정 |

**판정 결과:**
- 채택 → `ideas/adopted/`
- 탈락 → `ideas/rejected/` (사유 기록 필수)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 프로젝트 구조

```
app-idea-lab/
├── .claude/
│   └── commands/           # 슬래시 커맨드 (stage-1 ~ stage-4)
├── ideas/
│   ├── _template.md        # 아이디어 카드 템플릿
│   ├── adopted/            # 채택된 아이디어 + PRD + 리뷰
│   └── rejected/           # 탈락된 아이디어
├── research/
│   ├── competitors/        # 경쟁 분석 문서
│   └── market-data/        # 시장 분석 문서
├── evaluation/
│   └── criteria.md         # 평가 기준
├── CLAUDE.md               # 프로젝트 지침
└── README.md
```

### 파일명 규칙

| 유형 | 형식 | 예시 |
|---|---|---|
| 아이디어 카드 | `NNN-아이디어명.md` | `009-복약케어.md` |
| PRD | `NNN-아이디어명-prd.md` | `009-복약케어-prd.md` |
| 리뷰 | `NNN-아이디어명-review.md` | `009-복약케어-review.md` |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 시작하기

### 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 설치

### 설치

```sh
git clone <repo-url> ~/app-idea-lab-v2
cd ~/app-idea-lab-v2
```

별도의 의존성 설치는 필요 없다. Claude Code 세션에서 슬래시 커맨드를 실행하면 된다.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 사용법

Claude Code에서 프로젝트를 열고 슬래시 커맨드를 실행한다.

```
# 1단계: 새 아이디어 발굴
/stage-1

# 2단계: 아이디어 평가
/stage-2

# 3단계: PRD 작성
/stage-3

# 4단계: PRD 정합성 검증 및 판정
/stage-4
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 기술 스택 제약

채택된 PRD는 아래 스택으로 구현 가능해야 한다.

| 레이어 | 기술 |
|---|---|
| 프레임워크 | Expo Managed Workflow (SDK 최신 안정 버전) |
| 언어 | TypeScript (strict mode) |
| 라우팅 | Expo Router (파일 기반) |
| UI | React Native Paper |
| 상태 관리 | Zustand |
| 백엔드 | Supabase (PostgreSQL + Auth + Edge Functions + Storage) |
| 로컬 KV | react-native-mmkv |
| 로컬 DB | op-sqlite (조건부) |
| 타겟 플랫폼 | iOS / Android |

### 제외 기준

- 법적 인허가 필수 또는 규제가 까다로운 영역
- 기업/기관 협약 필수 아이디어
- 1인 개발 구현 불가 아이디어
- RN + Supabase 스택 부적합 아이디어

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 평가 기준

1. 1-3개월 내 MVP 출시 가능 여부
2. 한국 로컬 시장의 실제 pain point
3. 기존 스택으로 구현 가능한지

### PRD 필수 포함 항목 (Task Master 투입 요건)

- 기능 명세 (P0/P1/P2 우선순위)
- 수용 기준 (AC-XX-X 형식)
- DB 스키마 (CREATE TABLE SQL, RLS 정책)
- API 설계 (엔드포인트, TypeScript 인터페이스)
- 아키텍처 상세도 (시스템 구조도, 매핑표)
- 화면 구조 / UI 명세
- 오프라인/동기화 설계

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 연관 프로젝트

### project-init-v2

본 프로젝트에서 채택된 PRD는 [`project-init-v2`](../project-init-v2)의 입력이 된다. project-init-v2은 PRD를 기반으로 Expo + Supabase 프로젝트를 자동 생성하고 초기화한다.

```
app-idea-lab-v2 (PRD 산출) → project-init-v2 (프로젝트 생성) → 실제 개발
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>
