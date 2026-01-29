CLAUDE.md의 워크플로우 4단계(PRD 정합성 판별)를 수행한다.

대상 PRD 파일: $ARGUMENTS

## 수행 절차
1. 대상 PRD 파일을 읽고 내용을 파악한다.
2. CLAUDE.md의 4단계 절차와 검증 체크리스트에 따라 다음 검증을 순서대로 수행한다:
   - `/sc-spec-panel --mode critique` — 문서 품질 검증 (내적 일관성, 완결성, 측정 가능성, 기술 정합성)
   - `/sc-business-panel --mode debate` — 사업성·리스크 검증 (시장 경쟁력, 차별점, 수익화, TAM/SAM/SOM, 핵심 리스크, 의존성 리스크)
   - `architect-review` 에이전트 — 기술 검증 (RN+TS+Supabase 구현 가능성, 1인 개발 규모 적합성, 3개월 MVP 범위)
3. 검증 결과를 종합하여 판별 보고서를 작성한다.
4. 판별 결과를 사용자에게 보고하고, CLAUDE.md의 전환 조건에 따라 다음 행선지를 제안한다.