---
track: T2
title: 고객앱 작업 공간 WIP 백로그
group: 고객앱
channel: 주유소모바일
spec_status: 초안
spec_version: wip
dev_status: 진행중
owner: arya
repo: FE_oildealer
depends_on: []
related_docs: ["specs/product/customer-app/01_회원가입_사업자등록분리_기획서_v1.md", "specs/product/customer-app/02_홈_기획서_v1.md", "specs/product/customer-app/03_시장_기획서_v1.md", "specs/product/customer-app/04_관리_기획서_v1.md", "specs/product/customer-app/05_마이페이지_기획서_v1.md", "specs/product/customer-app/06_주문_기획서_v1.md", "specs/product/customer-app/07_장터_기획서_v1.md", "specs/product/customer-app/장터MVP_릴리즈범위_v1.md"]
review_docs: ["reviews/07_mobile_order_v2.md", "reviews/04_mobile_gap_analysis.md", "reviews/03_mobile_order_tracking.md"]
last_updated: 2026-06-26
source_channel: oildealer-product-specs
source_ts: '1782450772.667639'
source_thread_ts: '1782450772.667639'
source_file_ts: '1782450842.914259'
source_file_id: F0BD85U83E1
---

# 고객앱 — 작업 공간 (WIP · 백로그)

> 🛠️ **Arya의 작업 공간.** 고객앱(모바일) 현재 구현 + 진행 중(WIP) + 앞으로 할 일을 여기서 관리. 확정 스펙은 `01~06` 기획서로 승격.
> 기준: 코드 As-is + 진행 상태. [완료]=🟢 서비스중 · [WIP]=🔵 개발중 · [할일]=🟡 기획중 · [대기]=⚪ 펜딩
> 범위: 주유소 모바일앱(iOS/Android) — 홈·시장·관리·마이·주문·회원가입
>
> **🔁 작업 흐름 (역할 구분)**
> 1. 기획 본문은 **Arya가 작성** (WIP에 메모 → spec 본문). Amy가 내용을 쓰지 않음.
> 2. 파일 골격만 Amy: `Amy, customer-app에 "OO" spec 파일 만들어줘 (frontmatter·트래커 등록).`
> 3. 리뷰: spec에 **`[확인필요]`(기획/정책 확인 — 담당 지정 없이 누군가 확인)** 태그 → thread 요청. **개발 확인은 별도 태그로 추적하지 않음** — `개발 Go` 핸드오프로 넘기면 개발봇(Snoo)이 구현가능성·영향 답변. (`[개발검토]` 인라인 메모는 개발 참고용으로 남길 수 있으나 '검토 대기'로 집계 X)
> 4. Amy가 `reviews/`·human-check 정리, `[결정]`은 spec·status_tracker 반영. (정본=oildealer-specs)

---

## 0. 현황

- [완료] 코드 기준 화면 6종 정본화: 01_회원가입 · 02_홈 · 03_시장 · 04_관리 · 05_마이페이지 · 06_주문
- 주문 정본은 `06_주문_기획서_v1`. 모바일주문 v2 제안·근거는 `reviews/`(07_mobile_order_v2 · 03_mobile_order_tracking · 04_mobile_gap_analysis)

## 1. 진행 중 (WIP)

- **장터(MRO) MVP — 이번 릴리즈** (범위: `장터MVP_릴리즈범위_v1`, 모바일 1.0.11 우선, 계좌입금·포인트 없음)
  - `[정본화후보]` 장터 화면 `feature/store-phase0` → main 머지 (07_장터) + 1.0.11 빌드
  - `[완료]` 가입 승인 정책(홈 개방 / 신청·주문 시 `OrderGateModal` 게이트) — prod 1.0.10 반영 완료
  - `[결정]` C3 전까지 장터 진입점은 열람 노출, 구매·신청 단계는 "준비중" 안내
  - `[개발검토]` QA-01 배송지 변경 + 도로명 주소 검색
  - `[확인필요·Arya]` 장터 SKU 목록 정리·전달 → 어드민 카탈로그 등록(09 A-04)
  - 다음 phase: 소식탭/배너, 포인트, 장터 고도화(추천·시즌가·한도)
- 모바일주문 v2 제안(상태 6단계 분리·enum 정정·묶음추적 Phase) → 06_주문 v2 승격 검토

## 2. 백로그 / 할 일

- [할일] **세차 시세 (세차가격 DB → 앱 노출)** — 데이터 `세차가격_DB_v3`(119곳). 노출대상=주유소 사장님(시세·벤치마크). **위치 IA 미정**(시장탭 여부 미확정). 앱 반영 전 결정 필요:
  - `[확인필요]` 노출대상: 세차기없음(22)·고장(1) 제외 여부 / 재확인필요 36곳 포함 여부 (1차 "확인완료+운영중" 약 59곳 기준?)
  - `[확인필요]` 경쟁정보 민감성: 동종업자 노출 → 담당자명·연락처 비노출 / 가격공개 동의·상호 익명화 / "OO월 조사 시세" 프레이밍
  - `[확인필요]` 가격 표시: 주유금액 구간 티어 UI 방식 / 미주유 미확인 31곳 처리(추정 금지) / 차종 가감액·비고 다차종 / 조건부·패키지 할인 / 현금·카드 가감액
  - `[확인필요]` 옵션(3_세차옵션): 추가금 vs 별도상품가 표기 / "옵션없음 vs 미조사" 구분 불가
  - **선결(블로커)**: 위경도·주소 102곳 공백 → 지도·거리순 불가. 확보방법·담당·기한 필요 (영업시간 116곳 공백)
  - 정합성: 두창(S017) 가격ID 순서꼬임 / "두창주유소" vs "두창주유소(친절)" 동일업체 미확정
- [할일] 각 화면 연동 엔드포인트 명세 (홈 위젯·시장 데이터·관리 매출/탱크 등 API 매핑)
- [할일] 마이페이지 알림 설정 항목 전체 목록·기본값 (NotificationSettings 773줄 분해)
- [할일] 홈 "지금 사야 할까?" 시그널 산출 로직·표기 등급
- [할일] 관리탭 채권(외상) 정책 — 공통결정 C4(여신/외상) 연계
- [할일] 대량주문 추적(03)·앱 누락분(04) 중 모바일 반영분 선별

## 3. 결정 대기 [대기]

- 회원가입 T0 트랙 확정 여부 (Arya/Snoo)
- 에이전트 미연결(상태 B) 화면 노출 정책 표준화
