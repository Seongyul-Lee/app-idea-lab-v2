---
id: "003"
name: "살았니"
type: "review"
verdict: "pass"
final_decision: "adopted"
critical_count: 0
major_count: 0
minor_count: 0
date: "2026-02-02"
---
# 003-살았니 (SafePin) — 4단계 PRD 정합성 판별 보고서 (v6)

> 검증일: 2026-02-02
> 대상: `ideas/003-살았니-prd.md` (PRD v1.4)
> 이전 검증: v1 → Major 2건으로 3단계 회귀, v2 → Minor 7건으로 3단계 회귀, v3 → Minor 3건 통과 판정, v4 → Minor 5건 통과 판정 (사용자 선택으로 3단계 회귀), v5 → Minor 2건 통과 판정 (사용자 선택으로 3단계 회귀)
> 본 리뷰: v1.4에 대한 독립적 전체 재검증 (비판적 리뷰어 관점)

---

## 영역별 판정 요약

| 영역 | 판정 | Critical | Major | Minor |
|------|------|----------|-------|-------|
| A. 사업성 검증 | **통과** | 0 | 0 | 0 |
| B. 리스크 검증 | **통과** | 0 | 0 | 0 |
| C. 기술 검증 | **통과** | 0 | 0 | 0 |
| D. 문서 품질 검증 | **통과** | 0 | 0 | 0 |
| **합계** | | **0** | **0** | **0** |

---

## A. 사업성 검증 — 통과

| 점검 항목 | 결과 | 근거 |
|----------|------|------|
| PRD ↔ 2단계 시장 규모 일치 | ✅ | TAM 804만(1.93조), SAM 503만(2,414억), SOM 3~6만 유료(7.2~14.4억) — 시장분석 §1과 완전 일치 |
| PRD ↔ 2단계 경쟁분석 일치 | ✅ | Demumu/Snug Safety/루키스/정부 서비스 분석이 경쟁분석 §1~§4와 정합. 차별화 6개 항목 대응 |
| 차별점 방어 가능성 | ✅ | 카카오톡 알림톡(외국 경쟁자 진입장벽), 한국어 네이티브, 선점 우위. 기술적 해자가 얕은 점은 아이디어 문서와 PRD 양쪽에서 인식하고 빠른 실행으로 대응 |
| 수익 모델 구체성/현실성 | ✅ | 무료/프리미엄(월 ₩3,900/연 ₩39,000) 명확. 3개 시나리오 추정(보수적 ₩2.1억~낙관적 ₩25.2억) + SOM 관계 설명 완비 |
| TAM/SAM/SOM 논리적 근거 | ✅ | 각 단계 축소 근거(연령→불안 경험→유료 전환) + 출처(국가데이터처, Korea Herald) 명시 |
| Demumu 앱스토어 제거 반영 | ✅ | §2-2에 "2026년 1월 15일 중국 앱스토어에서 제거됨" 사실 + 바이럴 소멸 가능성 명시. §9-2에 마케팅 전략이 Demumu 바이럴 의존도를 낮추고 한국 고독사 이슈 중심으로 구성 |

---

## B. 리스크 검증 — 통과

| 점검 항목 | 결과 | 근거 |
|----------|------|------|
| 핵심 리스크 식별 완전성 | ✅ | 사업(R1~R3), 기술(R4~R5), 법률(R6~R7), 운영(R8), 의존성(R9~R10) 총 10개 리스크, 5개 유형 포괄 |
| 리스크 대응 방안 구체성 | ✅ | 각 리스크별 심각도·발생확률·대응방안 명시. R7은 법적 논거(개인정보보호법 §15-1-5, 정보통신망법 §50 단서) + 법률 자문 일정(W10~W11) 로드맵 포함 |
| 외부 API/플랫폼 의존성 관리 | ✅ | §12-2에 5개 외부 의존성(알리고, 카카오, FCM, IAP, Supabase)별 리스크·대안 완비 |

---

## C. 기술 검증 — 통과

| 점검 항목 | 결과 | 근거 |
|----------|------|------|
| Expo + RN + TS + Supabase 구현 가능성 | ✅ | §7-1에 17개+ 라이브러리 선정 사유 완비. 핵심 기능(체크인, 푸시, SMS, 인앱결제) 모두 해당 스택으로 구현 가능 |
| project-init 고정 기술 스택 준수 | ✅ | Expo Router, Zustand, MMKV, op-sqlite, React Native Paper 모두 §7-1에 명시. 조건부 의존성 별도 표기 |
| Windows 11 + EAS Build 환경 호환 | ✅ | macOS 필수 도구/iOS 전용 API 과의존 없음. EAS Build(클라우드)로 iOS 빌드 처리 |
| 1인 개발 규모 적정성 | ✅ | P0 14개, DB 테이블 7개, Edge Function 8개, DB Function 11개. 관리 가능한 규모 |
| 3개월 MVP 범위 현실성 | ✅ | 12주 4단계 로드맵, 주별 마일스톤이 기능 단위로 분리되어 진척 관리 가능 |
| DB 스키마 ↔ 요구사항 정합 | ✅ | 7개 테이블 CREATE TABLE SQL, FK/인덱스/RLS 완비. F10(next_deadline_at 자동 계산), F11(무료 주기 강제) 트리거로 데이터 정합성 보장 |
| 장애 시나리오 대응 | ✅ | 오프라인 체크인 + 동기화, SMS 재시도 3회(F9), 카카오→SMS 폴백, 5분 catch-up cron, 오알림 자동 취소(동기화 후 활성 유예 확인) |
| 오프라인 우선 설계 구체성 | ✅ | MMKV/op-sqlite 역할 분담 9항목 상세, 동기화 큐 5단계 흐름도, 지수 백오프 재시도(최대 5회), Server Wins 충돌 해결 정책 + 근거 |
| DB Function SQL 본문 완성도 | ✅ | F1~F11 전체 11개 함수 SQL 본문 + pg_cron 스케줄 등록 구문 3건. 스텁 없음 |
| DB Function 성능 안전장치 | ✅ | F2에 `SET statement_timeout = '50s'` — 1분 cron 주기 내 완료 보장. F5에 성능 주의 주석 + 최적화 방향 명시 |
| TypeScript 인터페이스 완성도 | ✅ | §7-6-3에 REST API + Edge Function 전체 요청/응답 인터페이스 + 공통 타입 8개 |
| Zustand Store 구조 정의 | ✅ | 7개 Store별 역할·주요 상태 필드·persist 전략(MMKV/op-sqlite/없음)·actions 목록 완비 |

---

## D. 문서 품질 검증 — 통과

| 점검 항목 | 결과 | 근거 |
|----------|------|------|
| 목표·기능·기술 명세 간 모순 | ✅ 없음 | Executive Summary → FR → DB/API → UI 명세 일관. 무료/유료 분기가 수익 모델·AC·DB 트리거(F11)까지 일관 |
| 16개 섹션 존재 | ✅ | §1~§16 전체 16개 섹션 존재 (§16 Glossary 17개 항목 포함) |
| KPI 구체성/측정 가능성 | ✅ | §15에 15개 KPI, 정의·목표·측정 방법 모두 명시. 사용자(MAU, DAU/MAU, Retention), 비즈니스(전환율, MRR, ARPU, Churn, LTV), 제품(응답시간, 오알림, 도달률, NPS, 평점) 3개 영역 |
| P0 수용 기준 (AC-XX-X 형식) | ✅ | FR-01~FR-14 전체 46개 AC 존재, 검증 가능한 조건문 형태 |
| User Story 커버리지 (최소 15개) | ✅ | 7개 Epic(온보딩·체크인·긴급연락처·미응답감지·타임라인·설정·구독), 27개 User Story. 모든 기능 영역 커버 |
| Screen Map / UI 명세 | ✅ | §8-1 네비게이션 구조 트리(Auth Stack/Main Tab Navigator/Modal Stack) + §8-2 8개 핵심 화면 UI 구성·데이터·API 매핑 |
| 기능-테이블-API 매핑표 | ✅ | §7-8에 14개 P0 기능(FR-01~FR-14) + 계정 삭제(PIPA 준수) 전체 매핑 완료 |

---

## 전체 미달 목록

**없음** — 4개 영역 전체에서 Critical/Major/Minor 미달 사항 0건.

---

## v5 리뷰 Minor 2건 해소 확인

| v5 # | 내용 | v1.4 반영 상태 |
|------|------|---------------|
| C-M1 | §8-2 일부 화면(ContactList, Settings, GraceAlert) API 경로 축약 표기 | ✅ 해소 — ContactListScreen: `/rest/v1/emergency_contacts` 정식 경로 + `/functions/v1/send-consent-sms`. SettingsScreen: `/rest/v1/user_settings`, `/rest/v1/subscriptions` 정식 경로. GraceAlertModal: `/functions/v1/cancel-grace` 정식 경로. OnboardingScreen: `/rest/v1/user_settings`, `/rest/v1/emergency_contacts`, `/functions/v1/send-consent-sms` 정식 경로 |
| D-M1 | §8-2 SubscriptionScreen에서 react-native-iap 표기 + 엔드포인트 축약 | ✅ 해소 — `RevenueCat SDK (구매/복원)` + `POST /functions/v1/verify-subscription`으로 수정. §7-1 기술 스택과 완전 일치 |

---

## 수정 지침

해당 없음 — Critical/Major/Minor 미달 사항이 없다.

---

## 최종 판정

### **통과**

| 판정 기준 | 결과 |
|----------|------|
| Critical 미달 | 0건 |
| Major 미달 | 0건 |
| Minor 미달 | 0건 |
| 아이디어 결함 | 0건 |

**판정 근거:**

1. **사업성**: TAM/SAM/SOM 수치가 2단계 산출물과 완전 일치. 수익 모델이 3개 시나리오로 상세 추정. Demumu 앱스토어 제거 사실이 §2-2, §9-2에 적절히 반영되어 사업 전제가 균형적. 차별점(카카오톡 알림, 한국어 네이티브, 체크인 커스터마이징)이 합리적
2. **리스크**: 5개 유형 10개 리스크 식별·대응 완비. 법적 논거(R7)가 개인정보보호법·정보통신망법 근거 + 법률 자문 일정(W10~W11)으로 구체적. 외부 의존성 5건 모두 대안 제시
3. **기술**: 11개 DB Function SQL 본문 완성(스텁 없음). F2 statement_timeout 안전장치. F5 성능 최적화 방향 명시. TypeScript 인터페이스 전체 API 커버. 7개 Zustand Store 역할·상태·persist·actions 완비. 오프라인 동기화 5단계 흐름도 + Server Wins 충돌 해결. project-init 고정 스택 100% 준수. §7-8 매핑표에 계정 삭제 행 포함. **v5 Minor 2건(§8-2 API 경로 축약, SubscriptionScreen 라이브러리 표기) 모두 해소 확인**
4. **문서 품질**: §1~§16 전체 16개 섹션 완전(Glossary 포함). 27개 User Story(7개 Epic). 46개 AC(검증 가능한 조건문). 8개 화면 UI 명세 — **모든 화면의 API 경로가 §7-6-2 정식 엔드포인트와 일치**. 기능-테이블-API 매핑표 P0 전체 + 계정 삭제 매핑 완료

**PRD v1.4는 Task Master에 투입하여 즉시 개발 태스크로 분해할 수 있는 수준으로 판단된다.**
