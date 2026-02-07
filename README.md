<a id="readme-top"></a>

# App Idea Lab v2

한국 시장 타겟 1인 개발 모바일 앱 아이디어의 **발굴 → 검증 → 평가 → PRD 작성 → 출시 → 성장**까지 전체 라이프사이클을 관리하는 문서 기반 워크플로우 프로젝트입니다.

<!-- TABLE OF CONTENTS -->
<details>
  <summary>목차</summary>
  <ol>
    <li><a href="#프로젝트-소개">프로젝트 소개</a></li>
    <li><a href="#전체-파이프라인">전체 파이프라인</a></li>
    <li><a href="#프로젝트-구조">프로젝트 구조</a></li>
    <li><a href="#시작하기">시작하기</a></li>
    <li><a href="#사전-설정">사전 설정</a></li>
    <li><a href="#사용법">사용법</a></li>
    <li><a href="#기술-스택-제약">기술 스택 제약</a></li>
    <li><a href="#평가-기준">평가 기준</a></li>
    <li><a href="#연관-프로젝트">연관 프로젝트</a></li>
  </ol>
</details>

## 프로젝트 소개

이 프로젝트는 **코드 개발을 하지 않습니다.** Claude Code의 슬래시 커맨드를 통해 아이디어 발굴부터 출시 후 성장 전략까지 8단계 파이프라인을 수행하며, 최종 산출물은 즉시 개발 착수 가능한 수준의 PRD 문서입니다.

**핵심 원칙:**
- 1인 개발자가 1-3개월 내 MVP 출시 가능한 아이디어만 대상으로 합니다
- 한국 로컬 시장의 실제 pain point 해결에 집중합니다
- 채택된 PRD는 Task Master에 투입하여 별도 설계 없이 개발 태스크로 분해할 수 있습니다

<p align="right">(<a href="#readme-top">back to top</a>)</p>

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
                                      실제 MVP 개발 (TaskMaster-AI 활용)
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
|:---:|---|---|---|
| Stage 1 | `/stage-1` | 아이디어 발굴 | `NNN-아이디어명.md` |
| Stage 1V | `/stage-1v` | 시장 수요 검증 | `NNN-아이디어명-validation.md` |
| Stage 2 | `/stage-2` | 사업성/기술/경쟁력 평가 | 시장분석, 경쟁분석 문서 |
| Stage 3 | `/stage-3` | PRD 문서 작성 | `NNN-아이디어명-prd.md` |
| Stage 4 | `/stage-4` | PRD 정합성 판별 | `NNN-아이디어명-review.md` + 채택/탈락 판정 |
| Stage 5 | `/stage-5` | 스토어 출시 + ASO 최적화 | `NNN-아이디어명-launch.md` |
| Stage 6 | `/stage-6` | 초기 성장 전략 | `NNN-아이디어명-growth.md` |
| Monthly | `/monthly-review` | 포트폴리오 월간 리뷰 | `YYYY-MM-review.md` |

### 판정 기준

**Stage 1V (시장 검증):**
- **GO** (70점 이상) → Stage 2로 진행합니다
- **CONDITIONAL** (40~69점) → 보완 후 재검증 또는 조건부로 진행합니다
- **NO-GO** (39점 이하) → `/stage-1`로 돌아가 새 아이디어를 발굴합니다

**Stage 4 (PRD 정합성):**
- 채택 → `ideas/adopted/`로 이동합니다
- 탈락 → `ideas/rejected/`로 이동합니다 (사유 기록 필수, 사용자 동의 후 처리)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 프로젝트 구조

```
app-idea-lab-v2/
├── .claude/
│   └── commands/              # 슬래시 커맨드 (stage-1 ~ monthly-review)
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
├── launch/                    # Stage 5 출시 참조 문서
│   ├── aso-checklist.md
│   └── store-assets-guide.md
├── growth/                    # Stage 6 성장 전략 참조 문서
│   ├── playbook.md
│   └── community-marketing.md
├── evaluation/
│   ├── criteria.md            # 평가 기준 (Stage 2)
│   ├── monthly-kpi.md         # 월간 KPI 추적 템플릿
│   └── portfolio-decision.md  # 유지/피벗/중단 판단 프레임워크
├── CLAUDE.md                  # 프로젝트 지침
└── README.md
```

### 파일명 규칙

| 유형 | 형식 | 예시 |
|---|---|---|
| 아이디어 카드 | `NNN-아이디어명.md` | `009-복약케어.md` |
| 검증 보고서 | `NNN-아이디어명-validation.md` | `009-복약케어-validation.md` |
| 시장분석 | `NNN-시장분석.md` | `009-시장분석.md` |
| 경쟁분석 | `NNN-경쟁분석.md` | `009-경쟁분석.md` |
| PRD | `NNN-아이디어명-prd.md` | `009-복약케어-prd.md` |
| 리뷰 | `NNN-아이디어명-review.md` | `009-복약케어-review.md` |
| 출시 계획 | `NNN-아이디어명-launch.md` | `009-복약케어-launch.md` |
| 성장 전략 | `NNN-아이디어명-growth.md` | `009-복약케어-growth.md` |
| 월간 리뷰 | `YYYY-MM-review.md` | `2026-02-review.md` |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 시작하기

### 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 설치 및 인증 완료

### 설치

```sh
git clone <repo-url> ~/app-idea-lab-v2
cd ~/app-idea-lab-v2
```

별도의 의존성 설치는 필요 없습니다. Claude Code 세션에서 슬래시 커맨드를 실행하면 됩니다.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 사전 설정

이 프로젝트의 커맨드가 정상 동작하려면 아래 MCP 서버와 Skills가 연동되어 있어야 합니다.

### 필수 MCP 서버

각 MCP 서버는 Claude Code의 설정(`~/.claude/settings.json` 또는 프로젝트 `.claude/settings.json`)에 등록해 주세요.

| MCP 서버 | 용도 | 사용 스테이지 |
|---|---|---|
| **naver-search** | 네이버 블로그/카페/뉴스/지식iN/쇼핑/데이터랩 검색으로 한국 시장 수요 조사 | Stage 1, 1V, 2 |
| **brave-search** | 글로벌 웹 검색, 해외 유사 앱 탐색, 시장 규모 데이터 수집 | Stage 1, 1V, 2 |
| **fetch** | 검색 결과 중 유의미한 URL의 본문 상세 확인 | Stage 1, 2 |

**설정 예시 (Claude Code MCP 설정):**

```json
{
  "mcpServers": {
    "naver-search": {
      "command": "npx",
      "args": ["-y", "@anthropics/naver-search-mcp"],
      "env": {
        "NAVER_CLIENT_ID": "<네이버 API 클라이언트 ID>",
        "NAVER_CLIENT_SECRET": "<네이버 API 시크릿>"
      }
    },
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@anthropics/brave-search-mcp"],
      "env": {
        "BRAVE_API_KEY": "<Brave Search API 키>"
      }
    },
    "fetch": {
      "command": "npx",
      "args": ["-y", "@anthropics/fetch-mcp"]
    }
  }
}
```

> **API 키 발급:**
> - 네이버: [Naver Developers](https://developers.naver.com) → 애플리케이션 등록 → 검색 API를 사용합니다
> - Brave: [Brave Search API](https://brave.com/search/api/) → Free 또는 Pro 플랜에 가입합니다

### 필수 Skills

Stage 3(PRD 작성)에서 기술 아키텍처 섹션을 작성할 때 사용되는 스킬입니다.

| Skill | 용도 | 사용 스테이지 |
|---|---|---|
| **sc:design** | 시스템 아키텍처, DB 스키마, API 설계, 오프라인 설계 작성 | Stage 3 |

Skills는 Claude Code의 `/` 커맨드 시스템을 통해 자동 호출됩니다. 별도 설치 없이 프로젝트의 `.claude/commands/` 디렉토리에 커맨드 파일이 있으면 사용 가능합니다.

### 슬래시 커맨드 목록

이 프로젝트에 포함된 커맨드입니다 (`.claude/commands/`):

| 커맨드 | 인자 | 설명 |
|---|---|---|
| `/stage-1` | `[주제]` (선택) | 아이디어 발굴. 주제 미입력 시 트렌드 기반 자동 탐색 |
| `/stage-1v` | `NNN-아이디어명` | 시장 수요 검증 |
| `/stage-2` | `NNN-아이디어명` | 사업성/기술/경쟁력 평가 |
| `/stage-3` | `NNN-아이디어명` | PRD 문서 작성 |
| `/stage-4` | `NNN-아이디어명` | PRD 정합성 판별 |
| `/stage-5` | `NNN-아이디어명` | 출시 + ASO 최적화 |
| `/stage-6` | `NNN-아이디어명` | 초기 성장 전략 |
| `/monthly-review` | — | 포트폴리오 월간 리뷰 |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 사용법

Claude Code에서 이 프로젝트 디렉토리를 열고 파이프라인을 순서대로 실행합니다.

```sh
# 프로젝트 디렉토리에서 Claude Code 시작
cd ~/app-idea-lab-v2
claude
```

```
# 1단계: 새 아이디어 발굴
/stage-1                    # Claude가 직접 주제 선정 및 탐색
/stage-1 반려동물 건강관리     # 특정 주제로 탐색

# 1V단계: 시장 수요 검증
/stage-1v 009-복약케어

# 2단계: 사업성 평가
/stage-2 009-복약케어

# 3단계: PRD 작성
/stage-3 009-복약케어

# 4단계: PRD 정합성 검증 및 판정
/stage-4 009-복약케어

# --- 채택된 PRD → project-init-v2로 프로젝트 초기화 → MVP 개발 ---

# 5단계: 출시 + ASO (MVP 빌드 완료 후)
/stage-5 009-복약케어

# 6단계: 성장 전략 (출시 후)
/stage-6 009-복약케어

# 월간 리뷰 (매월 반복)
/monthly-review
```

### 일반적인 흐름

1. `/stage-1`로 아이디어를 발굴합니다
2. `/stage-1v`로 시장 수요를 검증합니다 → GO 판정 시 다음 단계로 진행합니다
3. `/stage-2`로 사업성을 평가합니다
4. `/stage-3`로 PRD를 작성합니다
5. `/stage-4`로 PRD 정합성을 검증합니다 → 채택 시 `ideas/adopted/`로 이동합니다
6. **project-init-v2**에서 프로젝트를 초기화합니다 (`/init-scaffold` → `/init-docs`)
7. **TaskMaster-AI**로 PRD를 태스크로 분해하여 MVP를 개발합니다
8. `/stage-5`로 출시 + ASO를 최적화합니다
9. `/stage-6`로 성장 전략을 수립하고 실행합니다
10. `/monthly-review`로 매월 성과를 리뷰합니다

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 기술 스택 제약

채택된 PRD는 아래 스택으로 구현 가능해야 합니다.

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

- 법적 인허가 필수 또는 규제가 까다로운 영역 (의료, 금융 등)
- 기업/기관 협약 필수 아이디어
- 1인 개발 구현 불가 아이디어
- RN + Supabase 스택 부적합 아이디어

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 평가 기준

1. 1-3개월 내 MVP 출시 가능 여부
2. 한국 로컬 시장의 실제 pain point
3. 기존 스택으로 구현 가능한지

### PRD 필수 포함 항목 (Task Master 투입 요건)

채택된 PRD는 TaskMaster-AI에 투입하여 즉시 개발 태스크로 분해할 수 있는 수준이어야 합니다.

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

본 프로젝트에서 채택된 PRD는 [`project-init-v2`](../project-init-v2)의 입력이 됩니다. project-init-v2는 PRD를 기반으로 Expo + Supabase 프로젝트를 자동 생성하고 초기화합니다.

```
app-idea-lab-v2 (PRD 산출)
        ↓
project-init-v2 (프로젝트 초기화)
        ↓
TaskMaster-AI (태스크 분해 + 개발)
        ↓
app-idea-lab-v2 (출시/성장/월간 리뷰)
```

### TaskMaster-AI

프로젝트 초기화 이후 실제 개발 착수 시에는 [TaskMaster-AI](https://github.com/eyaltoledano/claude-task-master)를 적극 활용하는 것을 권장합니다. TaskMaster-AI는 PRD를 기반으로 개발 태스크를 자동 분해하고, 의존성 관리 및 작업 순서를 최적화해줍니다.

**TaskMaster-AI 활용 흐름:**
1. `project-init-v2`로 프로젝트를 초기화합니다
2. 초기화된 프로젝트에서 TaskMaster-AI를 설정합니다 (`task-master init`)
3. PRD를 기반으로 태스크를 자동 생성합니다 (`task-master parse-prd`)
4. `/next` 커맨드로 다음 작업을 조회합니다
5. `/plan` 커맨드로 구현 계획을 수립합니다
6. 구현 → 검증 → 완료 사이클을 반복합니다

<p align="right">(<a href="#readme-top">back to top</a>)</p>
