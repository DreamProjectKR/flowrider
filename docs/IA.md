# RiderFlow IA & Routing (초안)

## 주요 페이지 트리

- 로그인 `/login`
- 회원가입(옵션) `/signup`
- 대시보드 `/dashboard`
  - 정산 업로드 `/dashboard/upload`
  - 정산 결과(요약) `/dashboard/results`
  - 기사별 정산 상세 `/dashboard/riders/:riderId`
  - 지난 정산 내역 `/dashboard/history`
  - 기사 관리 `/dashboard/riders`
  - 지사 설정 `/dashboard/settings`
  - 알림센터 `/dashboard/notifications`

## 내비게이션 규칙

- 대시보드 하위는 중첩 라우트로 구성해 공용 레이아웃(헤더/사이드바)을 공유.
- 정산 업로드 완료 후 결과 페이지로 토스트+리다이렉트.
- 기사별 상세에서 뒤로 가기 시 이전 필터/정렬 상태 보존(쿼리스트링 유지).
- 알림센터는 발송 로그 필터(기간/상태)와 페이징을 제공.
