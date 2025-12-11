# RiderFlow Data Model (Supabase)

## 테이블 요약

- Agency(회사)
- Branch(지사) ← FK agency_id
- BranchManager(지사 관리자) ← FK branch_id
- Rider(라이더)
- RiderBranchMap(N:N) ← FK rider_id, branch_id, status
- DailySettlement(일일 정산) ← FK branch_id, rider_id
- PayslipGenerated(주간 명세서) ← FK branch_id, rider_id
- MessageLog(알림 발송) ← FK rider_id, agency_id, payslip_id
- KakaoProfile(옵션) ← FK branch_id

## 검증 규칙(예시)

- DailySettlement: work_date 필수/DATE, delivery_count INT>=0, delivery_fee INT>=0, total_pay 계산 결과와 음수 여부 체크.
- RiderBranchMap: status ENUM(active/inactive), 동일 라이더-지사 active 중복 금지.
- PayslipGenerated: week_start <= week_end, branch_id·rider_id not null.
- MessageLog: status ENUM(success/fail), message_type ENUM(payslip).

## 파생 로직

- 주간 집계: DailySettlement를 주 단위 그룹 → PayslipGenerated 생성.
- 발송: PayslipGenerated.id 기반으로 MessageLog 생성 후 상태 업데이트.
- 지사별 필터: 모든 쿼리는 branch_id 스코프(RLS).

## 인덱스 제안

- DailySettlement: (branch_id, work_date), (rider_id, work_date)
- RiderBranchMap: (rider_id, status), (branch_id, status)
- PayslipGenerated: (branch_id, week_start, week_end), (rider_id, week_start)
- MessageLog: (rider_id, sent_at), (status, sent_at)
