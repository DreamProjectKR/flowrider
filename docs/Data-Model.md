# RiderFlow Data Model (Supabase)

## 테이블 요약

- agencies(회사)
- branches(지사) ← FK agency_id
- user_memberships(관리자/직원 권한) ← FK user_id(auth.users), agency_id, branch_id(nullable)
- riders(라이더)
- rider_branch_map(N:N) ← FK rider_id, branch_id, status
- daily_settlements(일일 정산) ← FK branch_id, rider_id
- payslips(주간 명세서) ← FK branch_id, rider_id
- message_logs(알림 발송 로그) ← FK branch_id, rider_id, payslip_id(nullable)
- kakao_profiles(옵션) ← FK branch_id

## 검증 규칙(예시)

- daily_settlements: work_date 필수/DATE, delivery_count INT>=0, 금액 컬럼 INT>=0(정책에 따라 예외), (branch_id, rider_id, work_date) 중복 방지 권장.
- rider_branch_map: status `active/inactive`, (rider_id, branch_id) 중복 금지.
- payslips: week_start <= week_end, (branch_id, rider_id, week_start, week_end) 중복 방지 권장.
- message_logs: status `queued/success/fail`, message_type 예: `payslip`.

## 파생 로직

- 주간 집계: daily_settlements를 주 단위 그룹 → payslips 생성.
- 발송: payslips.id 기반으로 message_logs 생성 후 상태 업데이트.
- 지사별 필터: 모든 쿼리는 branch_id 스코프(RLS).

## 인덱스 제안

- daily_settlements: (branch_id, work_date), (rider_id, work_date)
- rider_branch_map: (rider_id, status), (branch_id, status)
- payslips: (branch_id, week_start, week_end), (rider_id, week_start)
- message_logs: (branch_id, sent_at), (status, sent_at), (payslip_id)
