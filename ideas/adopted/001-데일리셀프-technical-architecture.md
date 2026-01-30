# 데일리셀프 (DailySelf) — Technical Architecture Design

> **문서 버전**: v1.0
> **작성일**: 2026-01-31
> **대상 PRD**: `ideas/001-데일리셀프-prd.md` v2.0
> **용도**: PRD 섹션 7 (Technical Architecture) 원본

---

## 7-1. 기술 스택

| 레이어 | 기술 | 버전 | 선정 사유 |
|--------|------|------|----------|
| **프론트엔드** | React Native + TypeScript | RN 0.76+ | 크로스플랫폼 단일 코드베이스, 1인 개발 최적. Expo Managed Workflow로 네이티브 빌드 복잡성 제거 |
| **프레임워크** | Expo SDK | 52+ | Managed Workflow: 카카오 로그인은 expo-auth-session WebBrowser 방식, 애플은 expo-apple-authentication. EAS Build로 네이티브 빌드 |
| **네비게이션** | React Navigation | v7 | RN 표준 네비게이션. Static API로 타입 안전성 강화 |
| **UI 컴포넌트** | React Native Paper | v5 | Material Design 3 기반, 다크모드 내장, 접근성 기본 지원. NativeBase 대비 번들 크기 작고 유지보수 활발 |
| **상태 관리** | Zustand | v5 | 경량(1.1kB), 보일러플레이트 최소, TypeScript 타입 추론 우수. Redux 대비 설정 코드 90% 감소 |
| **로컬 저장소 (KV)** | react-native-mmkv | v3 | JSI 기반 동기 읽기/쓰기, AsyncStorage 대비 30배 빠름. 인증 토큰, 설정, 동기화 큐 저장 |
| **로컬 저장소 (구조화)** | @op-engineering/op-sqlite | v9 | 오프라인 체크인/플랜 캐시, 루틴 풀 로컬 사본. WatermelonDB 대비 단순, 직접 SQL 제어 가능 |
| **백엔드** | Supabase (BaaS) | — | Auth, PostgreSQL, Edge Functions, Realtime 올인원. 별도 서버 운영 불필요 |
| **데이터베이스** | PostgreSQL (Supabase) | 15+ | 관계형 데이터, RLS, SQL 함수로 통계 계산. pg_cron으로 스케줄 작업 |
| **결제** | RevenueCat | v8 | iOS/Android 구독 통합 관리, Webhook으로 서버 동기화. 1인 개발 최적 (월 $0~$19) |
| **푸시 알림** | Expo Notifications | SDK 52+ | Expo 생태계 통합, FCM/APNs 추상화, 무료 |
| **분석** | PostHog | Cloud Free | 이벤트 분석 + 퍼널 + 리텐션. 월 100만 이벤트 무료. 셀프호스트 전환 가능 |
| **차트** | react-native-gifted-charts | v1 | 주간 리포트 바 차트/라인 차트. SVG 기반으로 성능 양호 |
| **빌드/배포** | EAS Build + EAS Submit | — | Expo 공식 빌드/배포. CI/CD 없이 로컬에서 `eas build` → `eas submit` |
| **날짜 처리** | date-fns | v3 | 트리 쉐이킹 가능, Moment.js 대비 경량. 주간 리포트 날짜 계산에 사용 |

---

## 7-2. 시스템 구조도

```
┌─────────────────────────────────────────────────────────────────┐
│                     React Native App (Expo)                      │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                    Presentation Layer                        │ │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐  │ │
│  │  │ 홈/체크인 │ │ 루틴관리  │ │ 리포트   │ │ 설정/구독    │  │ │
│  │  │ Screen   │ │ Screen   │ │ Screen   │ │ Screen       │  │ │
│  │  └────┬─────┘ └────┬─────┘ └────┬─────┘ └──────┬───────┘  │ │
│  │       │            │            │               │           │ │
│  │  React Navigation v7 (Static API)                           │ │
│  └───────┼────────────┼────────────┼───────────────┼───────────┘ │
│          │            │            │               │             │
│  ┌───────┴────────────┴────────────┴───────────────┴───────────┐ │
│  │                     Business Logic Layer                     │ │
│  │  ┌─────────────────┐  ┌──────────────────────────────────┐  │ │
│  │  │ Adaptive Engine  │  │ Zustand Stores                   │  │ │
│  │  │ (순수 TS 모듈)   │  │ ┌────────┐ ┌──────┐ ┌────────┐ │  │ │
│  │  │ • 규칙 매트릭스   │  │ │AuthStore│ │Today │ │Routine │ │  │ │
│  │  │ • 선택 알고리즘   │  │ │        │ │Store │ │Store   │ │  │ │
│  │  │ • 폴백 처리      │  │ └────────┘ └──────┘ └────────┘ │  │ │
│  │  └─────────────────┘  │ ┌────────┐ ┌──────┐             │  │ │
│  │                        │ │Report  │ │Sub   │             │  │ │
│  │                        │ │Store   │ │Store │             │  │ │
│  │                        │ └────────┘ └──────┘             │  │ │
│  │                        └──────────────────────────────────┘  │ │
│  └──────────────────────────┬──────────────────────────────────┘ │
│                              │                                    │
│  ┌──────────────────────────┴──────────────────────────────────┐ │
│  │                      Data Layer                              │ │
│  │  ┌─────────────────┐  ┌───────────────┐  ┌──────────────┐  │ │
│  │  │ Supabase Client │  │ Local DB      │  │ MMKV Store   │  │ │
│  │  │ • Auth          │  │ (op-sqlite)   │  │ • JWT Token  │  │ │
│  │  │ • REST API      │  │ • check_ins   │  │ • Settings   │  │ │
│  │  │ • RPC calls     │  │ • daily_plans │  │ • Sync Queue │  │ │
│  │  │ • Realtime      │  │ • plan_items  │  │ • Theme      │  │ │
│  │  └────────┬────────┘  │ • routines    │  └──────────────┘  │ │
│  │           │           │ (로컬 캐시)    │                     │ │
│  │           │           └───────────────┘                     │ │
│  │  ┌────────┴──────────────────────────────────────────────┐  │ │
│  │  │              Sync Manager                              │  │ │
│  │  │  • Offline Queue (MMKV)                                │  │ │
│  │  │  • Conflict Resolution (Server Wins)                   │  │ │
│  │  │  • Exponential Backoff Retry                           │  │ │
│  │  └───────────────────────────────────────────────────────┘  │ │
│  └──────────────────────────────────────────────────────────────┘ │
└──────────────────────────┬──────────────────────────────────────┘
                            │ HTTPS / WSS
┌──────────────────────────┴──────────────────────────────────────┐
│                       Supabase Backend                           │
│  ┌──────────────┐  ┌───────────────┐  ┌──────────────────────┐ │
│  │ Auth          │  │ PostgreSQL    │  │ Edge Functions        │ │
│  │ • Kakao OAuth │  │ + RLS         │  │ • process-subscription│ │
│  │ • Apple OAuth │  │ + pg_cron     │  │ • delete-user-data   │ │
│  │ • Email/PW   │  │ + Functions   │  │                      │ │
│  └──────────────┘  └───────────────┘  └──────────────────────┘ │
└──────────────────────────┬──────────────────────────────────────┘
                            │
┌──────────────────────────┴──────────────────────────────────────┐
│                     External Services                            │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │ RevenueCat   │  │ Expo Push    │  │ PostHog              │   │
│  │ (구독 관리)   │  │ (알림)       │  │ (분석)               │   │
│  └─────────────┘  └──────────────┘  └──────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### 데이터 흐름 요약

```
[아침 체크인 흐름]
사용자 입력 → TodayStore → 로컬 DB 저장 → Adaptive Engine 실행 →
플랜 생성 → 화면 표시 → (온라인 시) Supabase 동기화

[루틴 완료 흐름]
탭 체크 → TodayStore 업데이트 → 로컬 DB 업데이트 →
달성률 재계산 → (온라인 시) Supabase 동기화

[주간 리포트 흐름]
pg_cron (매주 월요일 01:00 KST) → generate_weekly_report() RPC →
weekly_reports 테이블 저장 → 클라이언트 Pull

[구독 동기화 흐름]
RevenueCat Webhook → Edge Function (process-subscription) →
users.is_premium 업데이트 → 클라이언트 Realtime 수신
```

---

## 7-3. 오프라인 우선 설계

### 저장소별 역할 분담

| 저장소 | 용도 | 데이터 | 선택 사유 |
|--------|------|--------|----------|
| **MMKV** | 키-값 설정, 인증 토큰, 동기화 큐 메타 | JWT 토큰, 알림 시간, 테마, 온보딩 완료 여부, 동기화 큐 JSON | JSI 기반 동기 읽기(0.03ms), 앱 시작 시 즉시 인증 상태 판단 가능 |
| **op-sqlite** | 구조화된 오프라인 캐시 | 오늘 체크인, 오늘 플랜, 플랜 항목, 루틴 풀 (로컬 사본) | SQL 쿼리로 적응형 엔진에 필요한 "최근 7일 달성률" 등 집계 가능. AsyncStorage는 JSON 파싱 오버헤드로 부적합 |
| **Supabase (원본)** | 영구 저장소, 진실의 원천 (Source of Truth) | 전체 사용자 데이터 | 서버가 항상 최종 진실. 로컬은 캐시 역할 |

### 오프라인 지원 범위

| 기능 | 오프라인 동작 | 온라인 필수 |
|------|-------------|-----------|
| 아침 체크인 | O — 로컬 DB에 저장, 엔진 로컬 실행 | |
| 적응형 엔진 실행 | O — 순수 TS 모듈, 로컬 데이터만 사용 | |
| 루틴 완료 체크 | O — 로컬 DB 업데이트 | |
| 저녁 회고 입력 | O — 로컬 DB 저장 | |
| 루틴 풀 조회 | O — 로컬 캐시 | |
| 루틴 추가/편집 | | O — 서버 동기화 필요 |
| 주간 리포트 조회 | | O — 서버에서 Pull |
| 로그인/회원가입 | | O |
| 구독 관리 | | O |
| 데이터 동기화 | | O |

### 동기화 큐 로직

```
[Sync Queue 구조 (MMKV에 JSON 배열로 저장)]
{
  "queue": [
    {
      "id": "uuid",
      "table": "check_ins",
      "operation": "INSERT",
      "data": { ... },
      "created_at": "2026-01-31T07:30:00+09:00",
      "retry_count": 0,
      "max_retries": 5
    }
  ]
}

[동기화 트리거]
1. 앱이 포그라운드로 돌아올 때 (AppState change)
2. 네트워크 상태가 offline → online 전환 시 (NetInfo)
3. 데이터 쓰기 시 온라인이면 즉시 동기화

[재시도 전략: 지수 백오프]
- 1차: 즉시
- 2차: 2초 후
- 3차: 4초 후
- 4차: 8초 후
- 5차: 16초 후
- 5회 실패 시: 큐에 유지, 다음 동기화 트리거에서 재시도
- 공식: delay = min(2^retry_count * 1000, 30000) ms

[충돌 해결 정책: Server Wins]
- 로컬 캐시와 서버 데이터가 충돌하면 서버 데이터 우선
- 사유: 1인 사용 앱이므로 다중 디바이스 충돌은 드묾.
  서버를 진실의 원천으로 유지하여 설계 단순화
- 예외: 오프라인 체크인은 서버에 없으므로 INSERT (upsert on conflict)
```

---

## 7-4. 핵심 설계 원칙

1. **로컬 퍼스트 (Local First)**: 적응형 엔진은 클라이언트에서 실행. 체크인/루틴 체크는 로컬에 먼저 저장. 서버 의존 없이 핵심 기능 동작 → 1초 이내 응답 보장, 서버 비용 절감.

2. **서버리스 (Serverless)**: Supabase BaaS로 별도 서버 운영 제거. Edge Functions(Deno)로 구독 웹훅, 데이터 삭제 등 서버 로직 처리. 1인 개발자가 인프라 운영에 시간을 쓰지 않음.

3. **최소 외부 의존 (Minimal External Dependencies)**: 핵심 로직(적응형 엔진, 인사이트)에 외부 AI API 미사용. 규칙 기반으로 구현하여 비용 0원, 오프라인 동작, 지연 시간 없음. RevenueCat, Expo Push 정도만 외부 의존.

4. **점진적 복잡성 (Progressive Complexity)**: MVP는 규칙 기반 엔진 + 단순 집계 리포트. 데이터 축적 후 P1에서 패턴 학습/AI 인사이트 추가. 초기 복잡성을 최소화하여 3개월 내 출시.

5. **데이터 프라이버시 우선 (Privacy by Default)**: RLS로 모든 테이블의 데이터 격리. 최소 수집 원칙. 회원 탈퇴 시 30일 내 완전 삭제. 에너지/기분 데이터는 민감 정보로 취급.

---

## 7-5. DB 스키마

### CREATE TABLE SQL

```sql
-- ============================================================
-- 1. users (사용자)
-- ============================================================
CREATE TABLE users (
  id            uuid PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  email         text UNIQUE NOT NULL,
  nickname      text NOT NULL DEFAULT '',
  avatar_url    text,
  morning_alarm time NOT NULL DEFAULT '07:00',
  evening_alarm time NOT NULL DEFAULT '22:00',
  is_premium    boolean NOT NULL DEFAULT false,
  premium_expires_at timestamptz,
  onboarding_completed boolean NOT NULL DEFAULT false,
  deleted_at    timestamptz,  -- 탈퇴 예약 시 설정 (30일 후 삭제)
  created_at    timestamptz NOT NULL DEFAULT now(),
  updated_at    timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_users_deleted_at ON users (deleted_at) WHERE deleted_at IS NOT NULL;

-- ============================================================
-- 2. routine_categories (루틴 카테고리)
-- ============================================================
CREATE TABLE routine_categories (
  id          uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     uuid REFERENCES users(id) ON DELETE CASCADE,  -- NULL이면 시스템 카테고리
  name        text NOT NULL,
  icon        text NOT NULL DEFAULT '📌',
  is_system   boolean NOT NULL DEFAULT false,
  sort_order  int NOT NULL DEFAULT 0,
  created_at  timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_routine_categories_user ON routine_categories (user_id);

-- ============================================================
-- 3. routines (루틴 풀)
-- ============================================================
CREATE TABLE routines (
  id            uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id       uuid NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  category_id   uuid NOT NULL REFERENCES routine_categories(id) ON DELETE RESTRICT,
  name          text NOT NULL CHECK (char_length(name) <= 50),
  duration_min  int NOT NULL CHECK (duration_min BETWEEN 1 AND 180),
  difficulty    text NOT NULL CHECK (difficulty IN ('low', 'mid', 'high')),
  is_active     boolean NOT NULL DEFAULT true,
  is_template   boolean NOT NULL DEFAULT false,
  deleted_at    timestamptz,  -- soft delete
  created_at    timestamptz NOT NULL DEFAULT now(),
  updated_at    timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_routines_user_active ON routines (user_id) WHERE is_active = true AND deleted_at IS NULL;
CREATE INDEX idx_routines_category ON routines (category_id);

-- ============================================================
-- 4. check_ins (아침 컨디션 체크인)
-- ============================================================
CREATE TABLE check_ins (
  id          uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     uuid NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date        date NOT NULL,
  energy      int NOT NULL CHECK (energy BETWEEN 1 AND 5),
  sleep_hours numeric(3,1) NOT NULL CHECK (sleep_hours BETWEEN 0 AND 24),
  mood        text NOT NULL CHECK (mood IN ('good', 'normal', 'bad')),
  created_at  timestamptz NOT NULL DEFAULT now(),
  updated_at  timestamptz NOT NULL DEFAULT now(),
  UNIQUE (user_id, date)
);

CREATE INDEX idx_check_ins_user_date ON check_ins (user_id, date DESC);

-- ============================================================
-- 5. daily_plans (일일 적응형 플랜)
-- ============================================================
CREATE TABLE daily_plans (
  id                  uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id             uuid NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  check_in_id         uuid NOT NULL REFERENCES check_ins(id) ON DELETE CASCADE,
  date                date NOT NULL,
  total_duration_min  int NOT NULL DEFAULT 0,
  completion_rate     numeric(5,2) NOT NULL DEFAULT 0.00,
  created_at          timestamptz NOT NULL DEFAULT now(),
  updated_at          timestamptz NOT NULL DEFAULT now(),
  UNIQUE (user_id, date)
);

CREATE INDEX idx_daily_plans_user_date ON daily_plans (user_id, date DESC);

-- ============================================================
-- 6. plan_items (플랜 내 개별 루틴 항목)
-- ============================================================
CREATE TABLE plan_items (
  id                  uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  plan_id             uuid NOT NULL REFERENCES daily_plans(id) ON DELETE CASCADE,
  routine_id          uuid NOT NULL REFERENCES routines(id) ON DELETE RESTRICT,
  sort_order          int NOT NULL DEFAULT 0,
  is_completed        boolean NOT NULL DEFAULT false,
  completed_at        timestamptz,
  is_auto_suggested   boolean NOT NULL DEFAULT true,
  adjusted_difficulty text CHECK (adjusted_difficulty IN ('low', 'mid', 'high')),
  created_at          timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_plan_items_plan ON plan_items (plan_id);
CREATE INDEX idx_plan_items_routine ON plan_items (routine_id);

-- ============================================================
-- 7. reflections (저녁 회고)
-- ============================================================
CREATE TABLE reflections (
  id          uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     uuid NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  plan_id     uuid NOT NULL REFERENCES daily_plans(id) ON DELETE CASCADE,
  date        date NOT NULL,
  note        text CHECK (char_length(note) <= 200),
  created_at  timestamptz NOT NULL DEFAULT now(),
  UNIQUE (user_id, date)
);

CREATE INDEX idx_reflections_user_date ON reflections (user_id, date DESC);

-- ============================================================
-- 8. weekly_reports (주간 리포트)
-- ============================================================
CREATE TABLE weekly_reports (
  id              uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         uuid NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  week_start      date NOT NULL,  -- 월요일
  week_end        date NOT NULL,  -- 일요일
  check_in_count  int NOT NULL DEFAULT 0,
  avg_energy      numeric(3,1),
  avg_sleep       numeric(3,1),
  avg_completion  numeric(5,2),
  insight_text    text,
  report_data     jsonb,  -- 상세 일별 데이터
  created_at      timestamptz NOT NULL DEFAULT now(),
  UNIQUE (user_id, week_start)
);

CREATE INDEX idx_weekly_reports_user ON weekly_reports (user_id, week_start DESC);
```

### 시스템 카테고리 시드 데이터

```sql
INSERT INTO routine_categories (id, user_id, name, icon, is_system, sort_order) VALUES
  ('11111111-0000-0000-0000-000000000001', NULL, '운동', '🏃', true, 1),
  ('11111111-0000-0000-0000-000000000002', NULL, '학습', '📚', true, 2),
  ('11111111-0000-0000-0000-000000000003', NULL, '자기관리', '✨', true, 3),
  ('11111111-0000-0000-0000-000000000004', NULL, '건강', '🍎', true, 4),
  ('11111111-0000-0000-0000-000000000005', NULL, '마음챙김', '🧘', true, 5);
```

### RLS 정책 — 테이블별 CRUD 권한 매트릭스

| 테이블 | SELECT | INSERT | UPDATE | DELETE | 특별 규칙 |
|--------|--------|--------|--------|--------|----------|
| **users** | `auth.uid() = id` | Supabase Auth trigger | `auth.uid() = id` | — (Edge Function만) | 탈퇴는 soft delete (deleted_at 설정) |
| **routine_categories** | `user_id = auth.uid() OR is_system = true` | `auth.uid() = user_id` | `auth.uid() = user_id AND is_system = false` | `auth.uid() = user_id AND is_system = false` | 시스템 카테고리는 전체 읽기 허용, 수정/삭제 불가 |
| **routines** | `auth.uid() = user_id` | `auth.uid() = user_id` | `auth.uid() = user_id` | — (soft delete만) | soft delete: UPDATE로 deleted_at 설정 |
| **check_ins** | `auth.uid() = user_id` | `auth.uid() = user_id` | `auth.uid() = user_id` | — | 날짜+사용자 유니크 제약으로 하루 1회 보장 |
| **daily_plans** | `auth.uid() = user_id` | `auth.uid() = user_id` | `auth.uid() = user_id` | — | |
| **plan_items** | plan의 user_id = auth.uid() | plan의 user_id = auth.uid() | plan의 user_id = auth.uid() | plan의 user_id = auth.uid() | JOIN 기반 정책 (daily_plans.user_id 확인) |
| **reflections** | `auth.uid() = user_id` | `auth.uid() = user_id` | `auth.uid() = user_id` | — | |
| **weekly_reports** | `auth.uid() = user_id` | service_role만 (RPC) | service_role만 (RPC) | — | 사용자는 읽기만 가능 |

### RLS SQL

```sql
-- users
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
CREATE POLICY "users_select_own" ON users FOR SELECT USING (auth.uid() = id);
CREATE POLICY "users_update_own" ON users FOR UPDATE USING (auth.uid() = id);

-- routine_categories: 시스템 카테고리는 전체 읽기 허용
ALTER TABLE routine_categories ENABLE ROW LEVEL SECURITY;
CREATE POLICY "categories_select" ON routine_categories FOR SELECT
  USING (user_id = auth.uid() OR is_system = true);
CREATE POLICY "categories_insert_own" ON routine_categories FOR INSERT
  WITH CHECK (auth.uid() = user_id AND is_system = false);
CREATE POLICY "categories_update_own" ON routine_categories FOR UPDATE
  USING (auth.uid() = user_id AND is_system = false);
CREATE POLICY "categories_delete_own" ON routine_categories FOR DELETE
  USING (auth.uid() = user_id AND is_system = false);

-- routines
ALTER TABLE routines ENABLE ROW LEVEL SECURITY;
CREATE POLICY "routines_select_own" ON routines FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "routines_insert_own" ON routines FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "routines_update_own" ON routines FOR UPDATE USING (auth.uid() = user_id);

-- check_ins
ALTER TABLE check_ins ENABLE ROW LEVEL SECURITY;
CREATE POLICY "check_ins_select_own" ON check_ins FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "check_ins_insert_own" ON check_ins FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "check_ins_update_own" ON check_ins FOR UPDATE USING (auth.uid() = user_id);

-- daily_plans
ALTER TABLE daily_plans ENABLE ROW LEVEL SECURITY;
CREATE POLICY "plans_select_own" ON daily_plans FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "plans_insert_own" ON daily_plans FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "plans_update_own" ON daily_plans FOR UPDATE USING (auth.uid() = user_id);

-- plan_items (JOIN 기반)
ALTER TABLE plan_items ENABLE ROW LEVEL SECURITY;
CREATE POLICY "plan_items_select" ON plan_items FOR SELECT
  USING (EXISTS (SELECT 1 FROM daily_plans WHERE daily_plans.id = plan_items.plan_id AND daily_plans.user_id = auth.uid()));
CREATE POLICY "plan_items_insert" ON plan_items FOR INSERT
  WITH CHECK (EXISTS (SELECT 1 FROM daily_plans WHERE daily_plans.id = plan_items.plan_id AND daily_plans.user_id = auth.uid()));
CREATE POLICY "plan_items_update" ON plan_items FOR UPDATE
  USING (EXISTS (SELECT 1 FROM daily_plans WHERE daily_plans.id = plan_items.plan_id AND daily_plans.user_id = auth.uid()));
CREATE POLICY "plan_items_delete" ON plan_items FOR DELETE
  USING (EXISTS (SELECT 1 FROM daily_plans WHERE daily_plans.id = plan_items.plan_id AND daily_plans.user_id = auth.uid()));

-- reflections
ALTER TABLE reflections ENABLE ROW LEVEL SECURITY;
CREATE POLICY "reflections_select_own" ON reflections FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "reflections_insert_own" ON reflections FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "reflections_update_own" ON reflections FOR UPDATE USING (auth.uid() = user_id);

-- weekly_reports (읽기만 허용, 생성은 RPC)
ALTER TABLE weekly_reports ENABLE ROW LEVEL SECURITY;
CREATE POLICY "reports_select_own" ON weekly_reports FOR SELECT USING (auth.uid() = user_id);
```

### 핵심 DB Function — SQL 본문

#### generate_weekly_report

```sql
CREATE OR REPLACE FUNCTION generate_weekly_report(
  p_user_id uuid,
  p_week_start date
) RETURNS void
LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public
AS $$
DECLARE
  v_week_end       date := p_week_start + 6;
  v_check_in_count int;
  v_avg_energy     numeric(3,1);
  v_avg_sleep      numeric(3,1);
  v_avg_completion numeric(5,2);
  v_insight        text;
  v_high_energy_rate numeric;
  v_low_energy_rate  numeric;
  v_report_data    jsonb;
BEGIN
  -- 1. 주간 체크인 수 확인
  SELECT COUNT(*) INTO v_check_in_count
  FROM check_ins
  WHERE user_id = p_user_id
    AND date BETWEEN p_week_start AND v_week_end;

  -- 최소 3일 체크인 필요
  IF v_check_in_count < 3 THEN
    RETURN;
  END IF;

  -- 2. 주간 평균 계산
  SELECT
    ROUND(AVG(energy)::numeric, 1),
    ROUND(AVG(sleep_hours)::numeric, 1)
  INTO v_avg_energy, v_avg_sleep
  FROM check_ins
  WHERE user_id = p_user_id
    AND date BETWEEN p_week_start AND v_week_end;

  SELECT ROUND(AVG(completion_rate)::numeric, 2)
  INTO v_avg_completion
  FROM daily_plans
  WHERE user_id = p_user_id
    AND date BETWEEN p_week_start AND v_week_end;

  -- 3. 인사이트 생성 (에너지-달성률 상관관계)
  -- 에너지 3 이상인 날의 평균 달성률
  SELECT ROUND(AVG(d.completion_rate)::numeric, 1)
  INTO v_high_energy_rate
  FROM check_ins c
  JOIN daily_plans d ON c.user_id = d.user_id AND c.date = d.date
  WHERE c.user_id = p_user_id
    AND c.date BETWEEN p_week_start AND v_week_end
    AND c.energy >= 3;

  -- 에너지 2 이하인 날의 평균 달성률
  SELECT ROUND(AVG(d.completion_rate)::numeric, 1)
  INTO v_low_energy_rate
  FROM check_ins c
  JOIN daily_plans d ON c.user_id = d.user_id AND c.date = d.date
  WHERE c.user_id = p_user_id
    AND c.date BETWEEN p_week_start AND v_week_end
    AND c.energy <= 2;

  -- 인사이트 문장 생성
  IF v_high_energy_rate IS NOT NULL AND v_low_energy_rate IS NOT NULL THEN
    IF v_high_energy_rate - v_low_energy_rate > 20 THEN
      v_insight := '에너지가 높은 날(3 이상) 달성률이 ' || v_high_energy_rate || '%로, 낮은 날(' || v_low_energy_rate || '%)보다 훨씬 높아요. 컨디션 관리가 핵심이에요!';
    ELSIF v_high_energy_rate - v_low_energy_rate > 5 THEN
      v_insight := '에너지와 달성률이 약한 양의 관계를 보여요. 에너지가 높은 날 달성률 ' || v_high_energy_rate || '%, 낮은 날 ' || v_low_energy_rate || '%입니다.';
    ELSE
      v_insight := '이번 주는 에너지와 관계없이 꾸준히 수행했어요! 평균 달성률 ' || COALESCE(v_avg_completion, 0) || '%입니다.';
    END IF;
  ELSIF v_avg_completion IS NOT NULL THEN
    v_insight := '이번 주 평균 달성률은 ' || v_avg_completion || '%입니다. 꾸준히 체크인해주세요!';
  ELSE
    v_insight := '이번 주 데이터를 분석 중이에요. 매일 체크인하면 더 정확한 인사이트를 받을 수 있어요!';
  END IF;

  -- 4. 일별 상세 데이터 JSON 구성
  SELECT jsonb_agg(
    jsonb_build_object(
      'date', c.date,
      'energy', c.energy,
      'sleep_hours', c.sleep_hours,
      'mood', c.mood,
      'completion_rate', COALESCE(d.completion_rate, 0),
      'total_routines', (SELECT COUNT(*) FROM plan_items pi WHERE pi.plan_id = d.id),
      'completed_routines', (SELECT COUNT(*) FROM plan_items pi WHERE pi.plan_id = d.id AND pi.is_completed = true)
    ) ORDER BY c.date
  )
  INTO v_report_data
  FROM check_ins c
  LEFT JOIN daily_plans d ON c.user_id = d.user_id AND c.date = d.date
  WHERE c.user_id = p_user_id
    AND c.date BETWEEN p_week_start AND v_week_end;

  -- 5. 리포트 저장 (UPSERT)
  INSERT INTO weekly_reports (
    user_id, week_start, week_end, check_in_count,
    avg_energy, avg_sleep, avg_completion, insight_text, report_data
  ) VALUES (
    p_user_id, p_week_start, v_week_end, v_check_in_count,
    v_avg_energy, v_avg_sleep, v_avg_completion, v_insight, v_report_data
  )
  ON CONFLICT (user_id, week_start) DO UPDATE SET
    check_in_count = EXCLUDED.check_in_count,
    avg_energy = EXCLUDED.avg_energy,
    avg_sleep = EXCLUDED.avg_sleep,
    avg_completion = EXCLUDED.avg_completion,
    insight_text = EXCLUDED.insight_text,
    report_data = EXCLUDED.report_data;
END;
$$;
```

#### get_dashboard_stats

```sql
CREATE OR REPLACE FUNCTION get_dashboard_stats(p_user_id uuid)
RETURNS jsonb
LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public
AS $$
DECLARE
  v_streak         int := 0;
  v_weekly_avg     numeric(5,2);
  v_total_checkins int;
  v_top_routine    jsonb;
  v_current_date   date;
  v_check_date     date;
BEGIN
  v_current_date := (now() AT TIME ZONE 'Asia/Seoul')::date;

  -- 1. 연속 체크인 일수 (streak)
  v_check_date := v_current_date;
  LOOP
    IF EXISTS (
      SELECT 1 FROM check_ins
      WHERE user_id = p_user_id AND date = v_check_date
    ) THEN
      v_streak := v_streak + 1;
      v_check_date := v_check_date - 1;
    ELSE
      EXIT;
    END IF;
  END LOOP;

  -- 2. 최근 7일 평균 달성률
  SELECT ROUND(AVG(completion_rate)::numeric, 2)
  INTO v_weekly_avg
  FROM daily_plans
  WHERE user_id = p_user_id
    AND date BETWEEN v_current_date - 6 AND v_current_date;

  -- 3. 총 체크인 횟수
  SELECT COUNT(*) INTO v_total_checkins
  FROM check_ins
  WHERE user_id = p_user_id;

  -- 4. 가장 많이 완료한 루틴 (최근 30일)
  SELECT jsonb_build_object(
    'routine_id', r.id,
    'name', r.name,
    'completed_count', COUNT(*)
  )
  INTO v_top_routine
  FROM plan_items pi
  JOIN daily_plans dp ON pi.plan_id = dp.id
  JOIN routines r ON pi.routine_id = r.id
  WHERE dp.user_id = p_user_id
    AND dp.date BETWEEN v_current_date - 29 AND v_current_date
    AND pi.is_completed = true
  GROUP BY r.id, r.name
  ORDER BY COUNT(*) DESC
  LIMIT 1;

  RETURN jsonb_build_object(
    'streak_days', v_streak,
    'weekly_avg_completion', COALESCE(v_weekly_avg, 0),
    'total_check_ins', v_total_checkins,
    'most_completed_routine', v_top_routine
  );
END;
$$;
```

#### delete_user_data (탈퇴 시 데이터 완전 삭제)

```sql
CREATE OR REPLACE FUNCTION delete_user_data(p_user_id uuid)
RETURNS void
LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public
AS $$
BEGIN
  -- 순서: FK 의존성 역순으로 삭제
  -- plan_items는 daily_plans CASCADE로 자동 삭제
  DELETE FROM weekly_reports WHERE user_id = p_user_id;
  DELETE FROM reflections WHERE user_id = p_user_id;
  DELETE FROM daily_plans WHERE user_id = p_user_id;  -- plan_items CASCADE
  DELETE FROM check_ins WHERE user_id = p_user_id;
  DELETE FROM routines WHERE user_id = p_user_id;
  DELETE FROM routine_categories WHERE user_id = p_user_id;
  DELETE FROM users WHERE id = p_user_id;

  -- Supabase Auth 사용자 삭제는 Edge Function에서 admin API로 처리
END;
$$;
```

#### 주간 리포트 자동 생성 (pg_cron)

```sql
-- pg_cron: 매주 월요일 01:00 KST에 전체 사용자 주간 리포트 생성
SELECT cron.schedule(
  'weekly-report-generation',
  '0 16 * * 0',  -- UTC 일요일 16:00 = KST 월요일 01:00
  $$
  SELECT generate_weekly_report(
    u.id,
    (date_trunc('week', (now() AT TIME ZONE 'Asia/Seoul')::date - 1))::date
  )
  FROM users u
  WHERE u.deleted_at IS NULL
    AND u.onboarding_completed = true;
  $$
);
```

#### 탈퇴 예약 사용자 삭제 (pg_cron)

```sql
-- pg_cron: 매일 03:00 KST에 탈퇴 예약 30일 경과 사용자 삭제
SELECT cron.schedule(
  'delete-expired-users',
  '0 18 * * *',  -- UTC 18:00 = KST 03:00
  $$
  SELECT delete_user_data(u.id)
  FROM users u
  WHERE u.deleted_at IS NOT NULL
    AND u.deleted_at < now() - interval '30 days';
  $$
);
```

#### updated_at 자동 갱신 트리거

```sql
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
  NEW.updated_at = now();
  RETURN NEW;
END;
$$;

CREATE TRIGGER tr_users_updated_at BEFORE UPDATE ON users
  FOR EACH ROW EXECUTE FUNCTION update_updated_at();

CREATE TRIGGER tr_routines_updated_at BEFORE UPDATE ON routines
  FOR EACH ROW EXECUTE FUNCTION update_updated_at();

CREATE TRIGGER tr_check_ins_updated_at BEFORE UPDATE ON check_ins
  FOR EACH ROW EXECUTE FUNCTION update_updated_at();

CREATE TRIGGER tr_daily_plans_updated_at BEFORE UPDATE ON daily_plans
  FOR EACH ROW EXECUTE FUNCTION update_updated_at();
```

---

## 7-6. API 설계

### Supabase REST vs Edge Function 선택 기준

| 기준 | Supabase REST (자동 API) | Edge Function |
|------|------------------------|---------------|
| **사용 시점** | 단순 CRUD, RLS로 권한 제어 충분 | 외부 서비스 연동, 복잡한 비즈니스 로직, admin 권한 필요 |
| **장점** | 코드 없이 즉시 사용, RLS 자동 적용 | 커스텀 로직, 외부 API 호출, 시크릿 관리 |
| **선택된 경우** | CRUD (routines, check_ins, daily_plans, plan_items, reflections) | RevenueCat 웹훅, 사용자 삭제, 푸시 토큰 등록 |

### 전체 엔드포인트 목록

#### Supabase Client SDK (자동 REST API)

| 엔티티 | 작업 | 호출 방식 | 용도 |
|--------|------|----------|------|
| users | Read, Update | `supabase.from('users')` | 프로필 조회/수정, 알림 시간 설정 |
| routine_categories | Read | `supabase.from('routine_categories')` | 카테고리 목록 (시스템+사용자) |
| routine_categories | Insert, Update, Delete | `supabase.from('routine_categories')` | 커스텀 카테고리 관리 (유료) |
| routines | Read, Insert, Update | `supabase.from('routines')` | 루틴 풀 CRUD (삭제는 soft delete) |
| check_ins | Insert, Read, Update | `supabase.from('check_ins')` | 체크인 생성/조회/수정 |
| daily_plans | Insert, Read, Update | `supabase.from('daily_plans')` | 플랜 생성/조회/달성률 업데이트 |
| plan_items | Insert, Read, Update, Delete | `supabase.from('plan_items')` | 플랜 항목 CRUD |
| reflections | Insert, Read | `supabase.from('reflections')` | 회고 생성/조회 |
| weekly_reports | Read | `supabase.from('weekly_reports')` | 주간 리포트 조회 |

#### PostgreSQL RPC

| 함수 | 호출 | 용도 |
|------|------|------|
| `generate_weekly_report` | `supabase.rpc('generate_weekly_report', { p_user_id, p_week_start })` | 주간 리포트 수동 생성 (자동은 pg_cron) |
| `get_dashboard_stats` | `supabase.rpc('get_dashboard_stats', { p_user_id })` | 홈 대시보드 통계 |

#### Edge Functions

| 경로 | 메서드 | 용도 | 인증 |
|------|--------|------|------|
| `/functions/v1/process-subscription` | POST | RevenueCat 웹훅 수신, 구독 상태 동기화 | RevenueCat webhook secret |
| `/functions/v1/delete-user-account` | POST | 회원 탈퇴 요청 처리 (deleted_at 설정 + Auth 사용자 비활성화) | Bearer JWT |
| `/functions/v1/register-push-token` | POST | Expo 푸시 토큰 등록/갱신 | Bearer JWT |

### TypeScript 인터페이스

```typescript
// ============================================================
// Database Types (Supabase Generated 기반)
// ============================================================

// --- Enums ---
type Mood = 'good' | 'normal' | 'bad';
type Difficulty = 'low' | 'mid' | 'high';

// --- Tables ---
interface User {
  id: string;
  email: string;
  nickname: string;
  avatar_url: string | null;
  morning_alarm: string;  // "07:00"
  evening_alarm: string;  // "22:00"
  is_premium: boolean;
  premium_expires_at: string | null;
  onboarding_completed: boolean;
  created_at: string;
  updated_at: string;
}

interface RoutineCategory {
  id: string;
  user_id: string | null;
  name: string;
  icon: string;
  is_system: boolean;
  sort_order: number;
  created_at: string;
}

interface Routine {
  id: string;
  user_id: string;
  category_id: string;
  name: string;
  duration_min: number;
  difficulty: Difficulty;
  is_active: boolean;
  is_template: boolean;
  deleted_at: string | null;
  created_at: string;
  updated_at: string;
}

interface CheckIn {
  id: string;
  user_id: string;
  date: string;  // "2026-01-31"
  energy: number;  // 1-5
  sleep_hours: number;  // 0-24
  mood: Mood;
  created_at: string;
  updated_at: string;
}

interface DailyPlan {
  id: string;
  user_id: string;
  check_in_id: string;
  date: string;
  total_duration_min: number;
  completion_rate: number;
  created_at: string;
  updated_at: string;
}

interface PlanItem {
  id: string;
  plan_id: string;
  routine_id: string;
  sort_order: number;
  is_completed: boolean;
  completed_at: string | null;
  is_auto_suggested: boolean;
  adjusted_difficulty: Difficulty | null;
  created_at: string;
}

interface Reflection {
  id: string;
  user_id: string;
  plan_id: string;
  date: string;
  note: string | null;
  created_at: string;
}

interface WeeklyReport {
  id: string;
  user_id: string;
  week_start: string;
  week_end: string;
  check_in_count: number;
  avg_energy: number | null;
  avg_sleep: number | null;
  avg_completion: number | null;
  insight_text: string | null;
  report_data: WeeklyReportDayData[] | null;
  created_at: string;
}

interface WeeklyReportDayData {
  date: string;
  energy: number;
  sleep_hours: number;
  mood: Mood;
  completion_rate: number;
  total_routines: number;
  completed_routines: number;
}

// --- RPC Responses ---
interface DashboardStats {
  streak_days: number;
  weekly_avg_completion: number;
  total_check_ins: number;
  most_completed_routine: {
    routine_id: string;
    name: string;
    completed_count: number;
  } | null;
}

// --- API Request Types ---
interface CreateCheckInRequest {
  energy: number;       // 1-5
  sleep_hours: number;  // 0-24, 0.5 step
  mood: Mood;
}

interface CreateRoutineRequest {
  category_id: string;
  name: string;          // max 50 chars
  duration_min: number;  // 1-180
  difficulty: Difficulty;
}

interface UpdateRoutineRequest {
  name?: string;
  category_id?: string;
  duration_min?: number;
  difficulty?: Difficulty;
  is_active?: boolean;
}

interface CreateReflectionRequest {
  plan_id: string;
  note?: string;  // max 200 chars
}

interface UpdateUserSettingsRequest {
  nickname?: string;
  avatar_url?: string | null;
  morning_alarm?: string;
  evening_alarm?: string;
}

// --- Adaptive Engine Types (클라이언트 전용) ---
interface AdaptiveEngineInput {
  checkIn: CreateCheckInRequest;
  routinePool: Routine[];
  recentCompletionRates: Map<string, number>;  // routineId → 최근 7일 완료율
}

interface AdaptiveEngineOutput {
  suggestedItems: SuggestedPlanItem[];
  totalDuration: number;
  appliedRules: string[];  // 적용된 규칙 설명 (디버그용)
}

interface SuggestedPlanItem {
  routineId: string;
  sortOrder: number;
  adjustedDifficulty: Difficulty;
  isAutoSuggested: boolean;
}

// --- Edge Function Types ---
interface ProcessSubscriptionWebhook {
  event: {
    type: 'INITIAL_PURCHASE' | 'RENEWAL' | 'CANCELLATION' | 'EXPIRATION';
    app_user_id: string;
    expiration_at_ms: number;
    product_id: string;
  };
}

interface DeleteUserAccountRequest {
  confirmation: true;
}

interface RegisterPushTokenRequest {
  token: string;
  platform: 'ios' | 'android';
}
```

### Edge Function 비즈니스 로직

#### process-subscription

```
1. RevenueCat webhook secret 검증 (Authorization 헤더)
2. event.type 분기:
   - INITIAL_PURCHASE, RENEWAL:
     → users.is_premium = true
     → users.premium_expires_at = event.expiration_at_ms를 timestamptz로 변환
   - CANCELLATION:
     → (만료일까지 유지, 별도 처리 없음 — 만료 시점에 EXPIRATION 이벤트 수신)
   - EXPIRATION:
     → users.is_premium = false
     → users.premium_expires_at = null
3. Supabase admin client로 users 테이블 UPDATE
4. 200 OK 응답
```

#### delete-user-account

```
1. JWT에서 user_id 추출
2. 활성 구독 확인: users.is_premium = true이면 400 에러 ("구독을 먼저 해지해주세요")
3. users.deleted_at = now() 설정 (soft delete)
4. Supabase Auth admin API로 사용자 세션 무효화
5. 200 OK 응답 (30일 후 pg_cron이 실제 데이터 삭제)
```

#### register-push-token

```
1. JWT에서 user_id 추출
2. users 테이블에 push_token, push_platform 컬럼 업데이트
   (참고: 별도 push_tokens 테이블 대신 users에 직접 저장 — 1인 1디바이스 가정)
3. 200 OK 응답
```

---

## 7-7. 상태 관리 구조

### Zustand Store 목록

| Store | 역할 | 주요 상태 | 지속성 |
|-------|------|----------|--------|
| **useAuthStore** | 인증 상태, 사용자 프로필 | `user`, `session`, `isAuthenticated`, `isLoading` | MMKV (토큰), Supabase Auth (세션) |
| **useTodayStore** | 오늘의 체크인, 플랜, 루틴 실행 상태 | `todayCheckIn`, `todayPlan`, `planItems`, `completionRate` | op-sqlite (오프라인 캐시) |
| **useRoutineStore** | 루틴 풀, 카테고리 관리 | `routines`, `categories`, `activeRoutineCount` | op-sqlite (로컬 캐시) |
| **useReportStore** | 주간 리포트, 대시보드 통계 | `weeklyReports`, `dashboardStats`, `isLoading` | 없음 (서버 Pull) |
| **useSubscriptionStore** | 구독 상태, RevenueCat 연동 | `isPremium`, `expiresAt`, `offerings`, `isLoading` | RevenueCat SDK 캐시 |
| **useSettingsStore** | 앱 설정 (알림 시간, 테마) | `morningAlarm`, `eveningAlarm`, `theme`, `pushPermission` | MMKV |
| **useSyncStore** | 동기화 큐 상태, 네트워크 상태 | `isOnline`, `pendingQueueCount`, `lastSyncAt`, `isSyncing` | MMKV (큐 데이터) |

### Store 상세 구조

```typescript
// useAuthStore
interface AuthState {
  user: User | null;
  session: Session | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  // Actions
  signInWithKakao: () => Promise<void>;
  signInWithApple: () => Promise<void>;
  signInWithEmail: (email: string, password: string) => Promise<void>;
  signUp: (email: string, password: string) => Promise<void>;
  signOut: () => Promise<void>;
  updateProfile: (data: UpdateUserSettingsRequest) => Promise<void>;
  completeOnboarding: () => Promise<void>;
}

// useTodayStore
interface TodayState {
  todayCheckIn: CheckIn | null;
  todayPlan: DailyPlan | null;
  planItems: (PlanItem & { routine: Routine })[];
  completionRate: number;
  isCheckInDone: boolean;
  isLoading: boolean;
  // Actions
  submitCheckIn: (data: CreateCheckInRequest) => Promise<void>;
  updateCheckIn: (data: Partial<CreateCheckInRequest>) => Promise<void>;
  toggleRoutineCompletion: (planItemId: string) => Promise<void>;
  addPlanItem: (routineId: string) => Promise<void>;
  removePlanItem: (planItemId: string) => Promise<void>;
  replacePlanItem: (planItemId: string, newRoutineId: string) => Promise<void>;
  submitReflection: (note: string) => Promise<void>;
  loadToday: () => Promise<void>;
}

// useRoutineStore
interface RoutineState {
  routines: Routine[];
  categories: RoutineCategory[];
  activeRoutineCount: number;
  isLoading: boolean;
  // Actions
  loadRoutines: () => Promise<void>;
  createRoutine: (data: CreateRoutineRequest) => Promise<void>;
  updateRoutine: (id: string, data: UpdateRoutineRequest) => Promise<void>;
  deleteRoutine: (id: string) => Promise<void>;  // soft delete
  loadCategories: () => Promise<void>;
}

// useReportStore
interface ReportState {
  weeklyReports: WeeklyReport[];
  dashboardStats: DashboardStats | null;
  isLoading: boolean;
  // Actions
  loadWeeklyReports: () => Promise<void>;
  loadDashboardStats: () => Promise<void>;
}

// useSubscriptionStore
interface SubscriptionState {
  isPremium: boolean;
  expiresAt: string | null;
  offerings: any;  // RevenueCat Offerings
  isLoading: boolean;
  // Actions
  loadSubscriptionStatus: () => Promise<void>;
  purchaseMonthly: () => Promise<void>;
  purchaseAnnual: () => Promise<void>;
  restorePurchases: () => Promise<void>;
}

// useSettingsStore
interface SettingsState {
  morningAlarm: string;  // "07:00"
  eveningAlarm: string;  // "22:00"
  theme: 'system' | 'light' | 'dark';
  pushPermission: 'granted' | 'denied' | 'undetermined';
  // Actions
  updateMorningAlarm: (time: string) => void;
  updateEveningAlarm: (time: string) => void;
  setTheme: (theme: 'system' | 'light' | 'dark') => void;
  requestPushPermission: () => Promise<void>;
}

// useSyncStore
interface SyncState {
  isOnline: boolean;
  pendingQueueCount: number;
  lastSyncAt: string | null;
  isSyncing: boolean;
  // Actions
  addToQueue: (item: SyncQueueItem) => void;
  processQueue: () => Promise<void>;
  setOnlineStatus: (isOnline: boolean) => void;
}
```

---

## 7-8. 기능-테이블-API 매핑표

| P0 기능 (FR) | 관련 테이블 | Supabase REST | RPC | Edge Function | 비고 |
|-------------|-----------|---------------|-----|---------------|------|
| **FR-01 인증** | users | UPDATE (프로필) | — | — | Supabase Auth로 로그인/가입 처리 |
| **FR-01 온보딩** | users, routine_categories, routines | SELECT (카테고리), INSERT (루틴), UPDATE (onboarding_completed) | — | — | 템플릿 루틴 복사 |
| **FR-02 체크인** | check_ins | INSERT, SELECT, UPDATE | — | — | 하루 1회 UNIQUE 제약 |
| **FR-03 루틴풀** | routines, routine_categories | CRUD | — | — | soft delete (deleted_at) |
| **FR-04 엔진** | — (클라이언트) | — | — | — | 순수 TS 모듈, 로컬 실행 |
| **FR-04 플랜저장** | daily_plans, plan_items | INSERT, UPDATE | — | — | 엔진 출력을 서버에 저장 |
| **FR-05 실행** | plan_items, daily_plans | UPDATE (is_completed, completion_rate) | — | — | 달성률 클라이언트 계산 후 저장 |
| **FR-05 회고** | reflections | INSERT, SELECT | — | — | |
| **FR-06 리포트** | weekly_reports, check_ins, daily_plans | SELECT (weekly_reports) | `generate_weekly_report` | — | pg_cron 자동 + 수동 트리거 |
| **FR-06 대시보드** | check_ins, daily_plans, plan_items, routines | — | `get_dashboard_stats` | — | 홈 화면 통계 |
| **FR-07 결제** | users | — | — | `process-subscription` | RevenueCat → Webhook → Edge Function |
| **FR-08 탈퇴** | users (+ 전체 테이블) | — | `delete_user_data` (pg_cron) | `delete-user-account` | Edge Function이 soft delete, pg_cron이 30일 후 실제 삭제 |

---

## 7-9. 자체 점검 결과

| # | 점검 항목 | 결과 | 근거 |
|---|----------|------|------|
| 1 | 모든 P0 기능에 AC-XX-X 수용 기준 존재 | **충족** | FR-01~FR-08 전체에 AC-01-1 ~ AC-08-3 형식으로 기술됨 (섹션 5) |
| 2 | P0 기능별 DB 테이블·API 매핑 존재 | **충족** | 섹션 7-8 매핑표에서 FR-01~FR-08 전체 대응 |
| 3 | DB 스키마 FK 관계가 기능 간 데이터 흐름과 일치 | **충족** | check_ins → daily_plans → plan_items 흐름이 FR-02→FR-04→FR-05 순서와 일치. routines → plan_items FK로 루틴 참조 보존 |
| 4 | API 엔드포인트가 P0 수용 기준을 충족 가능 | **충족** | 모든 AC가 REST CRUD + 2개 RPC + 3개 Edge Function으로 처리 가능 |
| 5 | 핵심 API TypeScript 인터페이스 존재 | **충족** | 섹션 7-6에 모든 테이블 인터페이스, 요청/응답 타입, 엔진 타입 기술 |
| 6 | 핵심 DB Function SQL 본문 완성 | **충족** | generate_weekly_report, get_dashboard_stats, delete_user_data 3개 함수의 완전한 PL/pgSQL 본문 + pg_cron 스케줄 |
| 7 | 오프라인 저장소·동기화·충돌 해결 명시 | **충족** | 섹션 7-3에 MMKV/op-sqlite 역할 분담, 동기화 큐(지수 백오프 5회), Server Wins 충돌 정책 |
| 8 | 네비게이션 구조 트리 + 핵심 화면 UI 설명 존재 | **PRD 섹션 8에서 작성** | 본 기술 설계 문서의 범위 외. PRD 섹션 8에서 별도 기술 |
| 9 | User Story가 모든 기능 영역 커버 | **충족** | Epic 1(온보딩), 2(인증), 3(체크인), 4(루틴), 5(실행), 6(인사이트), 7(설정), 8(결제) — 8개 Epic, 27개 Story (섹션 4) |
