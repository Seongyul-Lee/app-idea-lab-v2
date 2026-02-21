# /sync-check: CLAUDE.md 동기화 검증

project-init-v2의 CLAUDE.md를 **source of truth**로 두고, app-idea-lab-v2의 CLAUDE.md 및 Stage 3 커맨드가 이와 일치하는지 단방향 검증한다.

## 위치

```
이 커맨드는 파이프라인 단계가 아닌 유틸리티 커맨드다.
두 프로젝트 간 CLAUDE.md 동기화 상태를 점검할 때 수시로 실행한다.
```

## 입력

| 파일 | 역할 |
|------|------|
| `~/project-init-v2/CLAUDE.md` | Source of Truth (기준 문서) |
| `~/app-idea-lab-v2/CLAUDE.md` | 검증 대상 (본 프로젝트) |
| `~/app-idea-lab-v2/.claude/commands/stage-3.md` | 검증 대상 (조건부 의존성 참조 확인) |

## 실행 절차

### Step 1: 기준 문서 읽기

아래 파일을 읽고 각 섹션의 내용을 파싱한다.

**project-init-v2 CLAUDE.md** (`~/project-init-v2/CLAUDE.md`):
- "기술 스택 (고정)" 섹션 → 레이어별 기술 목록 추출
- "공통 의존성" 섹션 → dependencies / devDependencies 패키지 목록 추출
- "조건부 의존성 매핑" 섹션 → 영역별(인증, 알림, 분석 등) PRD 기술명 목록 추출
- "개발 환경" 섹션 → OS, 빌드 환경, 디버깅 도구 추출
- "타겟 플랫폼" 섹션 → 플랫폼 목록 추출

### Step 2: 검증 대상 문서 읽기

**app-idea-lab-v2 CLAUDE.md** (`~/app-idea-lab-v2/CLAUDE.md`):
- "기술 스택 제약" 섹션 → 레이어별 기술 목록 추출
- "개발 환경" 섹션 → OS, 빌드 환경, 디버깅 도구 추출
- "타겟 플랫폼" 섹션 → 플랫폼 목록 추출
- "동기화 필수 항목" 테이블 → 대응 섹션명 목록 추출

**Stage 3 커맨드** (`~/app-idea-lab-v2/.claude/commands/stage-3.md`):
- "project-init 고정 기술 스택" 테이블 → 레이어별 기술 목록 추출
- "project-init 조건부 의존성" 테이블 → 영역별 기술명 목록 추출

### Step 3: 영역별 비교 수행

아래 5개 영역을 순서대로 비교한다.

**영역 1: 기술 스택**
- project-init-v2 "기술 스택 (고정)"의 각 레이어(프레임워크, 언어, 라우팅, UI, 상태 관리, 백엔드, 로컬 KV, 로컬 DB)별 값을 기준으로:
  - app-idea-lab-v2 "기술 스택 제약"의 동일 레이어 값과 비교
  - Stage 3 "project-init 고정 기술 스택" 테이블의 동일 레이어 값과 비교
- 값이 다르면 불일치로 기록 (어느 쪽이 다른지 명시)

**영역 2: 공통 의존성 현황**
- project-init-v2 "공통 의존성" 섹션에서:
  - 필수 dependencies 패키지 수 집계
  - 필수 devDependencies 패키지 수 집계
  - 전체 패키지 목록을 요약 출력
- 이 영역은 불일치 판정 없이 현황 정보만 출력한다

**영역 3: 조건부 의존성 매핑**
- project-init-v2 "조건부 의존성 매핑"의 영역별 PRD 기술명 목록을 기준으로:
  - Stage 3 "project-init 조건부 의존성" 테이블의 영역별 기술명과 비교
  - project-init-v2에는 있으나 Stage 3에 없는 항목 → 불일치
  - Stage 3에는 있으나 project-init-v2에 없는 항목 → 불일치
- 영역 카테고리(인증, 알림, 분석, 결제, 차트, UI 컴포넌트, 미디어/디바이스, 애니메이션)도 일치하는지 확인

**영역 4: 개발 환경 + 타겟 플랫폼**
- 개발 환경: project-init-v2 vs app-idea-lab-v2의 각 항목(OS, 개발·디버깅, iOS 빌드, iOS 검증) 비교
- 타겟 플랫폼: 양쪽 값 비교

**영역 5: 동기화 규칙 테이블 정합성**
- app-idea-lab-v2 "동기화 필수 항목" 테이블의 "project-init-v2 대응 섹션" 열에 나열된 섹션명들을 추출
- 각 섹션명이 project-init-v2 CLAUDE.md에 실제 섹션(헤딩)으로 존재하는지 확인
- 존재하지 않는 섹션이 있으면 불일치로 기록

### Step 4: 리포트 출력

아래 형식으로 터미널에 출력한다. **파일은 생성하지 않는다.**

```
═══════════════════════════════════════════
  /sync-check 동기화 검증 리포트
  실행일: YYYY-MM-DD
  Source of Truth: ~/project-init-v2/CLAUDE.md
  검증 대상: ~/app-idea-lab-v2/CLAUDE.md
═══════════════════════════════════════════

[영역 1: 기술 스택] ✅ 일치 / ⚠️ 불일치 N건
  (불일치 시) - 레이어명: lab="X" ≠ init="Y"
  (불일치 시) - 레이어명: stage-3="X" ≠ init="Y"

[영역 2: 공통 의존성] ℹ️ 현황
  - 필수 dependencies: N개
  - 필수 devDependencies: N개
  - 설치 명령어 패키지 수: N개

[영역 3: 조건부 의존성 매핑] ✅ 일치 / ⚠️ 불일치 N건
  (불일치 시) - "기술명" → init에 있으나 stage-3에 없음
  (불일치 시) - "기술명" → stage-3에 있으나 init에 없음

[영역 4: 개발 환경 + 플랫폼] ✅ 일치 / ⚠️ 불일치 N건
  (불일치 시) - 항목명: lab="X" ≠ init="Y"

[영역 5: 동기화 규칙 정합성] ✅ 일치 / ⚠️ 불일치 N건
  (불일치 시) - 대응 섹션 "XXX"가 project-init-v2에 존재하지 않음

═══════════════════════════════════════════
  종합: ✅ 전체 동기화 정상 / ⚠️ N개 영역에서 불일치 발견
═══════════════════════════════════════════
```

## 주의사항

- 이 커맨드는 **읽기 전용**이다. 불일치를 보고만 하며, 자동 수정은 하지 않는다
- 불일치 발견 시 사용자가 어느 쪽을 수정할지 판단한다 (원칙: project-init-v2가 source of truth)
- MCP 도구는 사용하지 않는다. 로컬 파일 읽기만으로 수행한다
