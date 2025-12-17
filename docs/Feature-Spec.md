# RiderFlow Feature Spec

## 1. 대시보드 (Dashboard)

- 요약 카드:
  - 총 배달 건수, 총 정산액 (Edge Function 계산 결과 노출)
  - 활성 기사 수 (status: working)
  - 미발송 정산 알림 수
- 통계 그래프:
  - 일자별 수익 추이 (Bar/Line Chart)
  - 지사별 매출 목표 달성률 (Gauge Chart)

## 2. 정산 엑셀 업로드 (Settlement Upload)

- 업로드 방식: Drag & Drop 및 파일 선택 (.xlsx, .xls) 지원.
- 파싱 엔진:
  - `xlsx` 라이브러리를 사용한 클라이언트 사이드 파싱.
  - 매칭 키: 이름, 전화번호, 엑셀 고유 ID.
- 데이터 처리:
  - 엑셀의 각 행 데이터를 `daily_settlements` 테이블에 원본 수치(건수, 단가 등)대로 저장.
  - 저장 후 필요한 계산은 Edge Functions를 호출하여 수행.
- 오류 처리:
  - 라이더 매칭 실패 행 표시.
  - 데이터 형식 오류 검증 및 실패 행 리스트 CSV 다운로드 기능.

## 3. 기사 관리 (Rider Management)

- 목록 조회: 이름, 연락처, 상태, 마지막 정산일 검색 및 필터링.
- 상태 업데이트:
  - 근무(working), 휴무(off), 제거(removed) 상태 직접 관리.
  - `working` 상태인 라이더만 대시보드 집계 및 플랜 인원 계산에 포함.

## 4. 계정 및 초대 (Auth & Admin)

- 루트 관리 도구: 본사 및 지사 생성/관리.
- 관리자 초대:
  - 이메일로 초대장 발송 -> 사용자가 직접 비밀번호 설정.
  - 계정 생성 시 소속 조직(본사/지사) 및 역할(Role) 부여.

## 5. 알림 및 로그 (Notification)

- 정산 알림: (추후 확정) 정산 요약 이미지 생성 및 카카오톡 전송.
- Edge Functions 활용: 데이터 집계, 이미지 생성 등을 서버 사이드에서 안전하게 처리.

## 6. UI/UX 디자인 가이드

- Design System: Shadcn/UI + Base UI 컴포넌트 활용.
- Layout: 반응형 대시보드, 사이드바 네비게이션.
