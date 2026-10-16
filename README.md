## Application Details
|               |
| ------------- |
|**Generation Date and Time**<br>Wed Sep 16 2026 08:12:16 GMT+0900 (Korean Standard Time)|
|**App Generator**<br>SAP Fiori Application Generator|
|**App Generator Version**<br>1.10.1|
|**Generation Platform**<br>Visual Studio Code|
|**Template Used**<br>Overview Page (OVP)|
|**Service Type**<br>OData URL|
|**Service URL**<br>https://saphana2.s-pert.com:44360/sap/opu/odata/sap/ZSP_ITSM_P_OVERVIEW_UI_V2|
|**Module Name**<br>zspf_overview|
|**Application Title**<br>고객 요청현황 (ITSM 종합 대시보드)|
|**Namespace**<br>zsp.itsm|
|**UI5 Theme**<br>sap_horizon|
|**UI5 Version**<br>1.108.0|
|**Enable TypeScript**<br>False|
|**Main Entity**<br>Main|
|**Navigation Entity**<br>None|

## zspf_overview

ITSM 종합 현황 및 고객 요청 분석 대시보드 (Overview Page)

### Starting the generated app

-   This app has been generated using the SAP Fiori tools - App Generator, as part of the SAP Fiori tools suite.  To launch the generated application, run the following from the generated application root folder:

```
    npm start
```

#### Pre-requisites:

1. Active NodeJS LTS (Long Term Support) version and associated supported NPM version.  (See https://nodejs.org)

<br>

### Custom UI5 Overview Page (OVP) Architecture & Cards

SAP Fiori Overview Page(OVP) 템플릿을 기반으로 고객 요청 현황, 담당자별 처리 실적, 지연 현황 및 유관 시스템 링크를 통합 제공하는 종합 관제 대시보드입니다.

<br>

#### 1) manifest.json — Global Filter 및 기본 일자 범위 설정
- **글로벌 필터 바**: `MainType` 엔티티를 기준으로 상단 글로벌 필터가 동작하며 차트 및 테이블 카드에 필터 조건이 연동됩니다.
- **YEARTODATE 기본값**: 요청일자(`ReqDate`) 필터에 당해연도 누계 연산자(`YEARTODATE`)가 기본 적용되어, 앱 실행 시 현재 연도 기준의 집계 데이터가 즉시 조회됩니다.
- **데이터 라벨 활성화**: 차트의 시인성을 극대화하기 위해 `chartSettings.showDataLabel: true`가 적용되었습니다.

```json
"sap.ovp": {
  "globalFilterModel": "mainModel",
  "globalFilterEntityType": "MainType",
  "containerLayout": "resizable",
  "enableLiveFilter": true,
  "considerAnalyticalParameters": true,
  "chartSettings": {
    "showDataLabel": true
  },
  "filterSettings": {
    "dateSettings": {
      "fields": {
        "ReqDate": {
          "defaultValue": {
            "operation": "YEARTODATE"
          }
        }
      }
    }
  }
}
```

<br>

#### 2) 대시보드 카드(Cards) 구성 상세 (총 7개 카드)

| 카드 ID | 카드 유형 | 대상 엔티티 | 어노테이션 / 주요 설정 | 비고 |
| :--- | :--- | :--- | :--- | :--- |
| **card010** | Analytical (Donut) | `Main` | `Chart#Request03` / `DataPoint#RequestCnt` | 고객별 요청 비율 (도넛 차트) |
| **card020** | Analytical (Column) | `Main` | `Chart#Request01` / `DataPoint#RequestCnt` | 고객별 요청 건수 (세로 막대 차트) |
| **card030** | Analytical (Combination) | `Manager` | `Chart#Request02` / `DataPoint#SuccCnt` | 담당자별 요청 대비 처리 완료 건수 |
| **card033** | Analytical (Combination) | `Main` | `Chart#Request04` / `DataPoint#SuccCnt` | 요청 분류별 처리 현황 |
| **card040** | Table Card | `Manager` | `LineItem#delay01` (정렬: `DelayDays` asc) | 현재일 기준 지연 현황 리스트 |
| **card050** | LinkList (Dynamic) | `ContactDetail` | `LineItem#ContactList` (`useGlobalFilter: false`) | 운영담당자 연락처 (전체 너비 5컬럼) |
| **card900** | LinkList (Static) | 정적 링크 | 메일, 게시판, 캘린더 바로가기 | 네이버 웍스 사내 도구 바로가기 |

<br>

#### 3) Cross-App Navigation & 외부 도구 링크 연동
- **Fiori Launchpad Inbound**: Semantic Object `ZSPF_OVERVIEW`, Action `display`
- **Intent-Based Navigation**: 차트 및 테이블 클릭 시 상세 화면 또는 타겟 ITSM 애플리케이션으로 이동할 수 있도록 어노테이션에 `UI.Identification#identification01` 등록
- **네이버 웍스 정적 링크 (`card900`)**: 사내 업무 편의를 위해 메일(`https://mail.worksmobile.com/`), 게시판(`https://board.worksmobile.com/`), 캘린더(`https://calendar.worksmobile.com/main.nhn`) 바로가기를 새 창(`openInNewWindow: true`)으로 연결

```json
"card900": {
  "model": "mainModel",
  "template": "sap.ovp.cards.linklist",
  "settings": {
    "title": "네이버 웍스",
    "listFlavor": "standard",
    "staticContent": [
      {
        "title": "메일",
        "imageUri": "sap-icon://email",
        "targetUri": "https://mail.worksmobile.com/",
        "openInNewWindow": true
      },
      {
        "title": "게시판",
        "imageUri": "sap-icon://task",
        "targetUri": "https://board.worksmobile.com/",
        "openInNewWindow": true
      },
      {
        "title": "캘린더",
        "imageUri": "sap-icon://calendar",
        "targetUri": "https://calendar.worksmobile.com/main.nhn",
        "openInNewWindow": true
      }
    ],
    "defaultSpan": {
      "cols": 5,
      "rows": 12
    }
  }
}
```
