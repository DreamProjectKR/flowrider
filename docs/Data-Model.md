# RiderFlow Data Model

## 1. 테이블 설계 개요

### 1.1 조직 및 계정

- agencies (본사): 최상위 조직 단위.
  - `id`, `name`, `business_number`, `created_at`
- branches (지사): 본사 하위 조직.
  - `id`, `agency_id` (FK), `name`, `target_revenue`, `created_at`
- profiles (사용자 프로필): Supabase Auth와 연동된 관리자 정보.
  - `id` (UUID, Auth.users FK), `email`, `role` (root, agency_admin, branch_admin), `agency_id` (nullable), `branch_id` (nullable)

### 1.2 라이더 관리

- riders (라이더): 시스템에 등록된 전체 라이더 엔티티.
  - `id`, `name`, `phone`, `external_id` (엑셀 내 고유 ID), `created_at`
- rider_branch_map (라이더-지사 매핑): 라이더의 소속 및 현재 상태 관리.
  - `id`, `rider_id` (FK), `branch_id` (FK), `status` (working, off, removed), `joined_at`

### 1.3 정산 데이터

- daily_settlements (일일 정산): 엑셀에서 파싱된 로우 데이터 (기초 수치만 저장).
  - `id`, `branch_id` (FK), `rider_id` (FK), `work_date`, `delivery_count`, `unit_price`, `promotion_fee`, `created_at`
  - *참고: 총 수익, 순수익 등 합산 데이터는 DB에 저장하지 않고 Edge Functions를 통해 실시간 계산함.*

### 1.4 알림 및 로그

- message_logs (발송 로그): 라이더 대상 정산 알림 발송 기록.
  - `id`, `rider_id` (FK), `branch_id` (FK), `settlement_date`, `status` (sent, failed), `error_message`, `sent_at`

## 2. 핵심 제약 및 로직

- RLS (Row Level Security):
  - `root`: 모든 데이터 접근 가능.
  - `agency_admin`: 소속 `agency_id`와 관련된 모든 지사 데이터 접근 가능.
  - `branch_admin`: 소속 `branch_id`와 관련된 데이터만 접근 가능.
- 라이더 식별: 정산 엑셀 파싱 시 `이름(name)` + `전화번호(phone)` + `엑셀 고유 ID(external_id)` 조합으로 매칭을 수행한다.
- 계산 로직: 모든 합산 및 집계(총 수익, 주간/월간 수익 등)는 Supabase Edge Functions에서 처리하여 프런트엔드에 제공한다.
