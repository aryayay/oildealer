# 고객웹 — 작업 공간 (WIP · 백로그)

> 🛠️ **Arya의 작업 공간.** 고객웹 현재 구현 + 진행 중(WIP) + 앞으로 할 일을 여기서 관리. 확정 스펙은 `01_웹개요`·`02_웹주문`으로 승격.
> 출처: `FE_oildealer/src/apps/customer/pages/orders` + `BE_oildealer` 주문 v2(`gas-station.order.v2.controller.ts`, `order.v2.model.ts`) + 원격 브랜치(Jira SCRUM)
> 기준: 코드 As-is + 진행 상태. [완료] = main 반영 · [WIP] = 작업 브랜치 존재 · [할일] = 코드 TODO/미정 · [대기] = 외부 회신
> 범위: **웹 주문 한정** (홈/시장/관리/마이 웹 이관은 별도 문서)

---

## 0. 한 줄 현황

웹은 주문 목적으로 개발됨. **V2 "간단한 주문"(단건) 흐름이 main에 반영**되어 동작하고, 상태/홈연결/에스크로/알림 등은 작업 브랜치에서 진행 중. 대량주문은 미착수.

## 1. V2 주문 모델 (현행 핵심) [코드확인]

핵심: **생성 시 최소 정보만, 상세는 제안 선택 후 입력**.

- 주문 생성(`POST /orders`)에는 **수량·납기·마감을 받지 않음**. (BE TODO 주석: "V2 간단한 주문에서는 quantity/deliveryDate/biddingDeadline 입력 안 함 — 제안 선택 후 `PUT /details`로 입력")
- 흐름: 주문 생성(유종·주유소) → 벤더 제안 수신 → **제안 선택**(`select-proposal`) → **상세 입력**(`PUT /{id}/details`: 수량·배송) → **주문 확정**(`confirm-order`) → 에스크로 입금 → 배송/수령 확정.
- V1 필드(`tradeType`, `biddingDeadline`, `deliveryTimeSlot` 등)는 모델에 있으나 **V2에서 미사용(주석처리)**.

> [할일] `order.v2.model`의 미사용 V1 필드 정리(죽은 필드 제거 or 명시적 deprecated). tradeType/거래조건 UI는 이미 제거됨(commit `74dc0cd`).

## 2. 현재 구현 (완료, main) [코드확인]

최근 커밋 기준 main 반영 기능:

- **V2 간단 주문 생성** 페이지(`SimpleOrderStart`, commit `e5bc381`)
- **주문 확정 페이지** + 배송수단 선택, 수량/날짜/시간 limits(min/max) 검증, Figma 모달(`288039b`,`61f2424`,`ee2c039`)
- **제안 목록/선택** UI(`32b21b2`, `12113b2`)
- **에스크로 입금** 계좌 생성·입금통보·확인(BE `/escrow/*`)
- **주문 취소** 전용 API+UI(`dc57f52`)
- **채권(외상) 주문** + 채권 결제 내역 표시(`8abd28f`, `POST /orders/receivable`)
- **출하지명 표시**(코드 대신 이름, `edb1d98`)
- **상태값** `waiting_confirm_order` 등 추가(`e4d9f3d`,`37da9da`), `OrderStatusCard` v1/v2 통합(`660bdc3`)
- 배송비 산정/마스킹, 마감 리마인더, SSE 구독(`order.v2.model` 메타)

## 3. 진행 중 (WIP — 원격 작업 브랜치) [코드확인: 브랜치 존재]

| 브랜치 | 내용 |
|---|---|
| `feature/SCRUM-166-...주문-홈-FE-API-연결-및-완결성-확인` | 주문/홈 FE↔API 연결 및 완결성 점검 |
| `feature/SCRUM-165-order-status` | 주문 상태 처리 |
| `feature/SCRUM-176-escrow` | 에스크로 |
| `feature/SCRUM-175-vendor-proposal` | 벤더 제안(주문 연계) |
| `feature/SCRUM-183-alarm-integration` | 알림 연동(주문 상태 트리거) |
| `feature/SCRUM-172-FE-임시-API-API-repo로-이관` | FE 임시 API → API repo 이관 |
| `feature/SCRUM-153-order` | 주문 일반 |

> [할일] 각 브랜치 머지 상태/잔여 범위는 Jira·PR로 확정 필요. 위는 브랜치명 기준 추정.

## 4. 백로그 / 할 일

### 4.1 데이터·정책 정리 [할일]
- V2 미사용 V1 필드 정리(모델 클린업)
- `OrderStatusV2` 전체 enum·상태 전이 다이어그램 정본화(`AMOUNT_ERROR`·`CS_REQUIRED` 포함)

### 4.2 외부 회신 대기 [대기] (web_order_v2 기준)
- 배송비 추가분 부담 주체 / 전표 정산 기준 (공통결정 C1)
- 온도·부피 입력(정산 시점) 정책
- e-biz 다운로드 양식 컬럼 매핑(정산팀 회신)

### 4.3 미착수 (별도 트랙) [할일]
- **대량주문**(다유종·다회차·엑셀 일괄·orderGroup) — 데이터모델부터 신규. 갭분석 문서 참조.
- 홈/시장/관리/마이 **웹 이관** — 별도 문서.

## 5. 활성 라우트 vs WIP (코드 확인) [코드확인]

`App.tsx` 기준 **현재 활성** 주문 라우트(상위 페이지):

`/orders`(Orders) → `/orders/create`(OrderCreate) → `/orders/:id/proposals`(ProposalList) → `/orders/:id/confirm`(ConfirmOrder) → `/orders/:id/select-delivery`(SelectDeliveryMethod) → `/orders/:id/payment`(OrderPayment) → `/orders/:id/delivery`(DeliveryTracking) · 상세 `/orders/:id`(OrderDetail)

> `pages/orders/` 하위 폴더(`SimpleOrderStart`, `OrderFlow`, `OrderSelectDetails`, `ConfirmOrderPage` 등 limits·grid 로직 포함)는 **라우터에 미연결** → 신규 V2 흐름 **WIP**로 추정. 활성 화면은 상위 페이지.

## 6. TODO / 확인 필요
- WIP `orders/` 하위 컴포넌트가 어느 브랜치에서 상위 페이지를 대체할 예정인지 (SCRUM-153/165/166 연계) 확인
- WIP 브랜치별 머지 예정일/담당 (Snoo·개발 확인)
- `dev_status`: 트래커 T1 "완료"는 V2 단건 한정임을 명시
