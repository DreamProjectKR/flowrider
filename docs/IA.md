# RiderFlow IA & Routing (v1.1)

## 1. 주요 페이지 및 경로

- 인증
  - `/login`: 로그인 페이지.
  - `/invite/accept`: 초대 수락 및 비밀번호 설정 페이지.

- 루트 관리자 전용 (Root Only)
  - `/admin/agencies`: 본사 목록 및 생성/수정.
  - `/admin/branches`: 지사 목록 및 생성/수정.
  - `/admin/invites`: 관리자 초대 현황 관리.

- 대시보드 (공용)
  - `/dashboard`: 메인 요약 통계 및 그래프.
  - `/dashboard/calendar`: 일자별 요약 캘린더 뷰.

- 정산 관리 (Settlement)
  - `/settlement/upload`: 엑셀 파일 업로드 및 검증.
  - `/settlement/history`: 과거 정산 데이터 조회 및 수정.
  - `/settlement/results`: 업로드 결과 세션 리포트.

- 기사 관리 (Rider)
  - `/riders`: 기사 목록 및 검색.
  - `/riders/new`: 신규 기사 수동 등록.
  - `/riders/[id]`: 기사별 상세 정보 및 개인 통계.

- 알림 및 설정
  - `/notifications`: 카카오톡 발송 로그 및 현황.
  - `/settings`: 지사 설정, 목표 매출 및 정산 공식 설정.

## 2. 내비게이션 규칙

- 레이아웃: 대시보드 하위는 공용 사이드바와 상단 헤더를 포함하는 레이아웃 공유.
- 접근 제어: Middleware를 사용하여 비로그인 사용자 및 권한 외 경로 접근 차단.
- 상태 유지: 기사 목록이나 정산 내역 조회 시 검색 필터 조건(URL Query Param) 유지.
