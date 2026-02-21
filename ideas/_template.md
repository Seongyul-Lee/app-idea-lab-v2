---
# 이 frontmatter는 각 Stage 커맨드가 자동으로 업데이트합니다
id: "NNN"
name: "아이디어명"
status: "in-progress"         # in-progress | adopted | rejected
current_stage: "stage-1"     # stage-1 | stage-1v | stage-2 | stage-3 | stage-4 | stage-5 | stage-6
created_at: "YYYY-MM-DD"
updated_at: "YYYY-MM-DD"

validation:
  score: null
  verdict: null               # GO | CONDITIONAL | NO-GO
  date: null
  subscores:
    search_demand: null        # 0~25
    community_pain: null       # 0~30
    competitor_gap: null       # 0~25
    user_scale: null           # 0~20

evaluation:
  score: null                  # 0~30
  date: null
  subscores:
    mvp_feasibility: null      # 1~5
    pain_point_clarity: null   # 1~5
    tech_stack_fit: null       # 1~5
    market_size: null          # 1~5
    competition: null          # 1~5
    monetization: null         # 1~5

review:
  verdict: null                # pass | regression-3 | regression-2 | reject
  final_decision: null         # adopted | rejected
  date: null
  critical_count: null
  major_count: null
  minor_count: null
  areas:
    business: null             # pass | fail
    risk: null                 # pass | fail
    technical: null            # pass | fail
    documentation: null        # pass | fail

rejection:
  stage: null                  # stage-1v | stage-2 | stage-4
  reason: null
---

# [아이디어 이름]

## 한줄 요약
>

## 해결하려는 문제 (Pain Point)
-

## 타겟 사용자
- **주요 타겟**:
- **연령대**:
- **규모 추정**:

## 핵심 기능 (MVP 범위, 최대 3개)
1.
2.
3.

## 수익 모델
-

## 경쟁 현황
| 서비스명 | 차이점 |
|---------|--------|
|         |        |

**핵심 차별점**:

## 기술 구현 검토
- **Expo Managed Workflow + React Native + TypeScript**: 구현 가능 여부
- **Supabase**: 필요한 기능 커버 여부
- **외부 서비스 의존도**: (결제, 본인인증, 지도 등)
- **개발 환경 호환성**: Windows 11 + EAS Build 환경에서 개발·테스트·빌드 가능 여부

## 평가 점수
| 항목 | 점수 (1-5) | 비고 |
|------|-----------|------|
| MVP 출시 가능성 (1-3개월) | | |
| Pain Point 명확성 | | |
| 기술 스택 적합성 | | |
| 시장 규모 | | |
| 경쟁 강도 (높을수록 낮은 점수) | | |
| 수익 모델 명확성 | | |
| **총점** | **/30** | |

## 판정
- **결과**: `채택` / `보류` / `탈락`
- **탈락 단계**: (탈락 시) 1단계 / 2단계
- **미달 항목**: (탈락 시) 미달 평가 항목 및 점수
- **사유**:
- **날짜**: YYYY-MM-DD
- **주의 사항**: (채택 시) 낮은 점수 항목에 대한 리스크 및 대응 방향
