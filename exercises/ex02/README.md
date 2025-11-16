[홈 - RAP100](../../#exercises)

# Exercise 2: BO 데이터 모델 개선 및 OData Streams 활성화

## 소개
이전 실습에서는 _Travel_ 데이터를 저장하기 위한 데이터베이스 테이블을 생성하고, 그 위에 비즈니스 오브젝트(BO) 엔터티 _Travel_ 을 포함하는 UI 서비스를 생성했습니다(참고: [Exercise 1](../ex01/README.md)).

이번 실습에서는 기본 BO 데이터 모델과 Projected BO 데이터 모델 및 그 메타데이터 확장을 개선하고, 개선된 Fiori elements _Travel_ 앱을 테스트합니다. 이러한 개선 사항에는 새로운 Association, 요소, View Annotation 및 Element Annotation 정의가 포함됩니다. 또한, Fiori elements 기반 앱에서 대용량 객체(OData streams라고도 함) 처리를 활성화할 것입니다.

OData streams를 활성화하면 최종 사용자가 _Travel_ 앱에서 이미지를 업로드하고 다운로드할 수 있는 옵션이 제공됩니다.

- [2.1 - 기본 BO 데이터 모델 개선](#exercise-21-기본-bo-데이터-모델-개선)
- [2.2 - 대용량 객체(LOBs, OData Streams) 처리 활성화](#exercise-22-대용량-객체lobs-odata-streams-처리-활성화)
- [2.3 - Projected BO 데이터 모델 개선](#exercise-23-projected-bo-데이터-모델-개선)
- [2.4 - 메타데이터 확장에서 UI 시맨틱 조정](#exercise-24-메타데이터-확장에서-ui-시맨틱-조정)
- [2.5 - 개선된 Fiori elements 앱 미리보기 및 테스트](#exercise-25-개선된-travel-앱-미리보기-및-테스트)
- [요약](#요약)


> **알림**: 아래 실습 단계에서 접미사 자리 표시자 **`###`** 를 선택하거나 할당된 그룹 ID로 바꾸는 것을 잊지 마십시오.

> ℹ **추가 정보**: [RAP 컨텍스트와 관련된 CDS Annotations](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/130e02a697e14bf8b05dd6672c56250b.html) | [Working with Large Objects](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/10a3eb645b83413cbbebe4fc1d879a62.html)

> ⚠ **힌트: 누락된 CDS 접근 제어에 대한 경고**
> CDS 뷰 엔터티 `ZRAP100_R_TRAVELTP_###` 및 `ZRAP100_C_TRAVELTP_###`에 표시될 누락된 접근 제어에 대한 경고는 무시하십시오. 이는 해당 엔터티에 지정된 뷰 어노테이션 `@AccessControl.authorizationCheck: #CHECK` 때문입니다.
> 시간 제약으로 인해 이 워크숍에서는 CDS 접근 제어를 정의하지 않습니다.
>
> 어노테이션 값을 `#NOT_REQUIRED`로 변경하여 이러한 경고를 표시하지 않을 수 있습니다.

## Exercise 2.1: 기본 BO 데이터 모델 개선
[^페이지 상단으로](#)

> CDS 뷰 엔터티 ![datadefinition](images/adt_ddls.png)**`ZRAP100_R_TRAVELTP_###`** 에 정의된 기본 BO 데이터 모델에 새로운 Association을 정의하고 노출합니다:
> - 비즈니스 엔터티 _Customer_ (_\_Customer_) 및 _Agency_ (_\_Agency_)에 대한 Association
> - _Overall Status_ (_\_OverallStatus_) 및 _Currency_ (_\_Currency_)에 대한 유용한 정보 Association

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

 1. 새로운 Association **`_Agency`**, **`_Customer`**, **`_OverallStatus`**, **`_Currency`** 를 정의합니다.
 
    데이터 정의 ![datadefinition](images/adt_ddls.png)**`ZRAP100_R_TRAVELTP_###`** 를 열고 **ABAP Formatter**(일명 _Pretty Printer_)를 사용하여 소스 코드의 서식을 지정합니다(**Shift+F1**).
 
    아래 스크린샷과 같이 **`select`** 문 뒤에 다음 코드 스니펫을 삽입하고 소스 코드의 서식을 지정합니다(**Shift+F1**).
     
    ```ABAP
    association [0..1] to /DMO/I_Agency            as _Agency        on $projection.AgencyID = _Agency.AgencyID
    association [0..1] to /DMO/I_Customer          as _Customer      on $projection.CustomerID = _Customer.CustomerID
    association [1..1] to /DMO/I_Overall_Status_VH as _OverallStatus on $projection.OverallStatus = _OverallStatus.OverallStatus
    association [0..1] to I_Currency               as _Currency      on $projection.CurrencyCode = _Currency.Currency
    ```
    
    소스 코드는 다음과 같아야 합니다:
    
    ![association](images/nsc.png)
    
  2. 정의된 Association **`_Agency`**, **`_Customer`**, **`_OverallStatus`**, **`_Currency`** 를 Selection List에 노출합니다.
  
     이를 위해 아래 제공된 코드 스니펫을 스크린샷과 같이 중괄호(`{...}`) 사이의 Selection List에 삽입하고 소스 코드의 서식을 지정합니다(**Shift+F1**).

     ```ABAP
     ,
 
     //public associations
     _Customer,
     _Agency,
     _OverallStatus,
     _Currency
     ```
      
     ![association](images/nsc2.png)
      
   3. 변경 사항을 저장![save icon](images/adt_save.png) (**Ctrl+S**)하고 활성화![activate icon](images/adt_activate.png) (**Ctrl+F3**)합니다.

</details>


## Exercise 2.2: 대용량 객체(LOBs, OData Streams) 처리 활성화
[^페이지 상단으로](#)

> Fiori elements 앱에서 대용량 객체(LOBs, OData streams) 처리를 활성화하기 위해 기본 RAP BO의 데이터 모델을 조정합니다.
> 이렇게 함으로써 최종 사용자에게 _Travel_ 앱에서 이미지를 업로드하고 다운로드할 수 있는 옵션을 제공하게 됩니다.
> 
> RAP BO에서 해야 할 일은 관련 필드인 `Attachment`, `MimeType`, `FileName`에 대해 적절한 Semantics Annotation을 지정하는 것뿐입니다. 또한 _Travel_ 앱에서의 모양을 위해 CDS Metadata Extension에서 UI Semantics를 조정해야 합니다.

> ℹ **추가 정보**: [Working with Large Objects](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/10a3eb645b83413cbbebe4fc1d879a62.html)

<details>
  <summary>🔵 클릭하여 펼치기!</summary>
 
 1. CDS 데이터 정의 ![datadefinition](images/adt_ddls.png)**`ZRAP100_R_TRAVELTP_###`** 에 머무르면서 _select_ 목록의 다음 요소들을 살펴보십시오:

      - **`Attachment`** - LOB(stream)을 저장하는 데 사용됩니다. CDS 어노테이션 `@Semantics.largeObject`를 사용하여 적절하게 어노테이션되어야 합니다. 기술적으로 `MimeType` 필드에 바인딩됩니다.
      - **`MimeType`** - 첨부 파일의 콘텐츠 유형을 나타내는 데 사용됩니다. CDS 어노테이션 `@Semantics.mimeType`을 사용하여 적절하게 태그되어야 합니다.
      - **`FileName`** - LOB(stream)의 파일 이름을 저장하는 데 사용됩니다. 이는 선택 사항입니다. 이 요소에는 특정 어노테이션이 필요하지 않습니다.

 2. 아래 제공된 코드 스니펫을 사용하여 스크린샷과 같이 요소에 어노테이션을 추가하십시오.

     - 요소 **`MimeType`** 에 대해:
     ```ABAP
        @Semantics.mimeType: true
     ```

     - 요소 **`Attachment`** 에 대해:
     ```ABAP
       @Semantics.largeObject: { mimeType: 'MimeType',   //case-sensitive
                                 fileName: 'FileName',   //case-sensitive
                                 acceptableMimeTypes: ['image/png', 'image/jpeg'],
                                 contentDispositionPreference: #ATTACHMENT }
     ```

    ![association](images/new3b.png)

    **간단한 설명**: 어노테이션 `@Semantics.largeObject`의 속성들
     - `mimeType`: MIME 객체의 유형을 포함하는 필드의 이름을 나타냅니다. ⚠ 값은 대소문자를 구분합니다.
     - `fileName`: MIME 객체의 파일 이름을 포함하는 필드의 이름을 나타냅니다. ⚠ 값은 대소문자를 구분합니다.
     - `acceptableMimeTypes`: 관련 스트림 속성에 대해 허용되는 MIME 유형 목록을 제공하여 사용자 입력을 제한하거나 확인합니다. 하위 유형이 허용되는 경우 *로 표시할 수 있습니다.
     - `contentDispositionPreference`: 콘텐츠가 브라우저에 인라인으로 표시될 것으로 예상되는지(즉, 웹 페이지 또는 웹 페이지의 일부로) 또는 첨부 파일로(즉, 로컬에 다운로드하여 저장) 표시될 것으로 예상되는지를 나타냅니다.

 3. 변경 사항을 저장![save icon](images/adt_save.png) (**Ctrl+S**)하고 활성화![activate icon](images/adt_activate.png) (**Ctrl+F3**)합니다.

</details>

## Exercise 2.3: Projected BO 데이터 모델 개선
[^페이지 상단으로](#)

> CDS Projection View ![datadefinition](images/adt_ddls.png)**`ZRAP100_C_TRAVELTP_###`** (Consumption View라고도 함)에 정의된 Projected BO 데이터 모델을 개선합니다.
> 예를 들어, 일부 요소에 대한 전체 텍스트 검색을 허용하고, 언어 종속적인 설명 텍스트를 위한 새로운 요소를 추가하며, Value Help를 정의합니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

 1. 데이터 정의 ![datadefinition](images/adt_ddls.png)**`ZRAP100_C_TRAVELTP_###`** 를 열고 생성된 소스 코드의 서식을 **Pretty Printer**(**Shift+F1**)로 지정합니다.
    
    아래 스크린샷과 같이 다음 View Annotation을 추가하여 Projection View를 검색 가능하도록 지정합니다:
    ```ABAP
     @Search.searchable: true
    ```
 
    > **정보**:
    > 생성된 데이터 정의에서, 요소 `TravelID`는 View Annotation `@ObjectModel.semanticKey: ['TravelID']`를 사용하여 _Travel_ 엔터티의 Semantic Key로 지정되고, CDS Projection View는 `DEFINE ROOT VIEW ENTITY` 문에 `provider contract transactional_query` 추가를 통해 BO Projection으로 지정됩니다.
 
    최종 사용자 라벨 텍스트를 교체합니다:
    ```ABAP
     @EndUserText.label: '##GENERATED Travel App (###)'
    ```
 
    소스 코드는 다음과 같아야 합니다:
     
    <!-- ![association](images/new4.png) -->
    <img src="images/new4.png" alt="table" width="50%">

 2. ⚠ 아직 하지 않았다면, 소스 코드의 서식을 **Pretty Printer**(**Shift+F1**)로 지정하십시오.
 
 3. 중괄호(`{...}`) 사이의 Selection List에 Agency 이름, 고객 이름, 그리고 Overall Status의 설명 텍스트를 추가하여 개선합니다.
 
    이를 위해 아래 스크린샷과 같이 적절한 코드 스니펫을 추가합니다:
 
    - `AgencyID` 뒤에 `AgencyName` 정의:
       ```ABAP
         _Agency.Name              as AgencyName,
       ```
    - `CustomerID` 뒤에 `CustomerName` 정의:
       ```ABAP
         _Customer.LastName        as CustomerName,
       ```
 
    - `OverallStatus` 뒤에 `OverallStatusText` 정의:
       ```ABAP
         _OverallStatus._Text.Text as OverallStatusText : localized,
       ```
      > 참고: `localized` 키워드는 현재 시스템 언어로 텍스트 요소를 표시하는 데 사용됩니다.

     소스 코드는 다음과 같아야 합니다:
     
     <!-- ![association](images/new5.png) -->
     <img src="images/new5.png" alt="association" width="50%">

 4. 제공된 코드 스니펫을 사용하여 아래 스크린샷과 같이 중괄호 사이에 **`TravelID`**, **`AgencyID`**, **`CustomerID`**, **`Currency Code`**, **`OverallStatus`** 요소에 대한 다양한 Element Annotation을 지정합니다.
     
    - 요소 **`TravelID`** 에 대해: 특정 유사성(오차 허용 범위)으로 전체 텍스트 검색을 활성화합니다.

       ```ABAP
       @Search.defaultSearchElement: true
       @Search.fuzzinessThreshold: 0.90
       ```
     
    - 요소 **`AgencyID`** 에 대해: 전체 텍스트 검색을 활성화하고, Value Help를 정의하며, **`AgencyName`** 을 연관 텍스트로 지정합니다. 정의된 Value Help는 Fiori elements UI에서 프론트엔드 유효성 검사에 자동으로 사용되어야 합니다.

       ```ABAP
       @Search.defaultSearchElement: true
       @ObjectModel.text.element: ['AgencyName']
       @Consumption.valueHelpDefinition: [{ entity : {name: '/DMO/I_Agency_StdVH', element: 'AgencyID' }, useForValidation: true }]
       ```
     
    - 요소 **`CustomerID`** 에 대해: 전체 텍스트 검색을 활성화하고, **`CustomerName`** 을 연관 텍스트로 지정하며, Fiori elements UI에서 프론트엔드 유효성 검사에 자동으로 사용될 Value Help를 정의합니다.
 
       ```ABAP
       @Search.defaultSearchElement: true
       @ObjectModel.text.element: ['CustomerName']
       @Consumption.valueHelpDefinition: [{ entity : {name: '/DMO/I_Customer_StdVH', element: 'CustomerID' }, useForValidation: true }]
       ```
    
    - 요소 **`CurrencyCode`** 에 대해: Fiori elements UI에서 유효성 검사에 자동으로 사용될 Value Help를 정의합니다.

       ```ABAP
       @Consumption.valueHelpDefinition: [{ entity: {name: 'I_CurrencyStdVH', element: 'Currency' }, useForValidation: true }]
       ```
     
    - 요소 **`OverallStatus`** 에 대해: **`OverallStatusText`** 를 연관 텍스트로 지정하고 Fiori elements UI에서 프론트엔드 유효성 검사에 자동으로 사용될 Value Help를 정의합니다.

       ```ABAP
       @ObjectModel.text.element: ['OverallStatusText']
       @Consumption.valueHelpDefinition: [{ entity: {name: '/DMO/I_Overall_Status_VH', element: 'OverallStatus' }, useForValidation: true }]
       ```

    또는, BO Projection View ![ddls icon](images/adt_ddls.png)**`ZRAP100_C_RAP_TRAVEL_###`** 의 소스 코드를 아래 링크된 소스 코드 문서에 제공된 코드로 간단히 교체하고, **Ctrl+F** 를 사용하여 자리 표시자 **`###`** 의 모든 발생을 그룹 ID로 교체할 수 있습니다.
      
    ![document](images/doc.png) **소스 코드 문서**: ![ddls icon](images/adt_ddls.png)[CDS projection view ZRAP100_C_TRAVELTP_###](sources/EX2_DDLS_ZRAP100_C_TRAVELTP.txt)

    **ABAP Pretty Printer**(**Shift+F1**)로 소스 코드의 서식을 지정합니다.
 
    소스 코드는 다음과 같아야 합니다:
    
    ![projected view](images/new6.png)
 
    > **힌트: 프론트엔드 유효성 검사 (Frontend Validations)**
    > 유효성 검사는 데이터 일관성을 보장하기 위해 사용됩니다.
    > 이름에서 알 수 있듯이, 프론트엔드 유효성 검사는 UI에서 수행됩니다. 더 빠른 피드백을 제공하고 불필요한 서버 왕복을 피함으로써 사용자 경험을 개선하는 데 사용됩니다. RAP 컨텍스트에서 프론트엔드 유효성 검사는 CDS 어노테이션(예: `@Consumption.valueHelpDefinition.useForValidation: true`) 또는 UI 로직을 사용하여 정의됩니다.
    
5. 변경 사항을 저장![save icon](images/adt_save.png) (**Ctrl+S**)하고 활성화![activate icon](images/adt_activate.png) (**Ctrl+F3**)합니다.
   
</details>

## Exercise 2.4: 메타데이터 확장에서 UI 시맨틱 조정
[^페이지 상단으로](#)

> Metadata Extension ![ddlx icon](images/adt_ddlx.png)을 개선하여 생성된 Fiori elements 기반 _Travel App_ 의 모양을 변경합니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>


 1. 메타데이터 확장 ![metadataextension](images/adt_ddlx.png)**`ZRAP100_C_TRAVELTP_###`** 을 열고 _Travel App_ 의 Fiori elements 기반 UI에 다음과 같은 변경 사항을 적용하기 위해 UI 어노테이션을 조정합니다.

    이를 위해, 메타데이터 확장의 생성된 소스 코드를 아래 링크된 소스 코드 문서에 제공된 코드로 교체하고, **Ctrl+F** 를 사용하여 자리 표시자 **`###`** 의 모든 발생을 그룹 ID로 교체하십시오.
     
    ![document](images/doc.png) **소스 코드 문서**: ![ddlx icon](images/adt_ddlx.png)[CDS metadata extension ZRAP100_C_TRAVELTP_###](sources/EX2_DDLX_ZRAP100_C_TRAVELTP.txt)

     소스 코드는 다음과 같을 것입니다:
     <!-- ![MetaDataExtension](images/new7.png) -->
     <img src="images/new7.png" alt="Metadata extension" width="60%">

      - **헤더 정보** 개선 - `TravelID`와 업로드된 첨부 파일(`Attachment`)이 _Travel_ 객체 페이지의 헤더에 표준 설명으로 표시되어야 합니다.
    - 요소 **`TravelID`** - 필터 바의 선택 기준이 되어야 하며 작은 창에서 높은 표시 중요도를 가져야 합니다.
    - 요소 **`AgencyID`** - 필터 바의 선택 기준이 되어야 하며 작은 창에서 높은 표시 중요도를 가져야 합니다.
    - 요소 **`CustomerID`** - 필터 바의 선택 기준이 되어야 하며 작은 창에서 높은 표시 중요도를 가져야 합니다.
    - 요소 **`BeginDate`** - (변경 없음)
    - 요소 **`EndDate`** - (변경 없음)
    - 요소 **`BookingFee`** - 목록 테이블이 아닌 객체 페이지만 표시되어야 합니다.
    - 요소 **`TotalPrice`** - 목록 테이블이 아닌 객체 페이지만 표시되어야 합니다.
    - 요소 **`CurrencyCode`** - 목록 테이블이나 객체 페이지에 명시적으로 표시되어서는 안 됩니다.
      > 참고: 통화 코드는 BO Projection View에서 `CurrencyCode` 요소에 대해 지정된 `@consumption` 어노테이션 덕분에 UI에 자동으로 표시됩니다.
    - 요소 **`Description`** - 목록 테이블이 아닌 객체 페이지만 표시되어야 합니다.
    - 요소 **`OverallStatus`** - 작은 창에서 높은 표시 중요도를 가져야 하며, 연관된 설명 텍스트만 UI에 표시되어야 합니다.
    - 요소 **`Attachment`** - 목록 테이블이 아닌 객체 페이지만 표시되어야 합니다.
    - 요소 **`MimeType`** - 숨겨져야 합니다.
    - 요소 **`FileName`** - 숨겨져야 합니다.
    
   2. 변경 사항을 저장![save icon](images/adt_save.png)하고 활성화![activate icon](images/adt_activate.png)합니다.
   
</details>

## Exercise 2.5: 개선된 Travel 앱 미리보기 및 테스트
[^페이지 상단으로](#)

> 개선된 SAP Fiori elements 애플리케이션을 테스트합니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

 1. 서비스 바인딩 ![servicebinding](images/adt_srvb.png) **`ZRAP100_UI_TRAVEL_O4_###`** 을 열고 _**Travel**_ 엔터티 세트를 더블 클릭하여 SAP Fiori elements 미리보기를 엽니다.
 
 2. 앱에서 **Go** 를 클릭하고 결과를 확인합니다.
    
 3. 앱에서 여러 기능을 시험해 보십시오. 예를 들어, 항목을 필터링하고 새 항목을 만들거나 기존 항목을 편집하여 정의된 Value Help를 테스트합니다.

    a. 목록 보고서 페이지에 멋진 설명과 결과를 필터링할 수 있는 옵션이 있음을 알 수 있습니다.
      <!-- ![Preview app - list page](images/preview.png)  -->
      <img src="images/preview.png" alt="Preview" width="70%">
 
    b. 새 항목을 만들거나 기존 항목을 변경할 때 **Agency ID** 및 **Customer ID** 필드에 대한 Value Help가 즉시 사용 가능한 **프론트엔드 유효성 검사** 를 제공하는 것을 볼 수 있습니다.
    <!-- ![Preview app - object page](images/preview2.png)   -->
      <img src="images/preview2.png" alt="Preview app - object page" width="70%">
 
    c. 또한 애플리케이션에서는 **jpg** 및 **png** 유형의 사진을 업로드할 수 있습니다.
      <!-- ![Preview app - upload images](images/preview3.png)    -->
      <img src="images/preview3.png" alt="Preview app - upload images" width="60%">

</details>

## 요약
[^페이지 상단으로](#)

이제 여러분은...
- 인터페이스 뷰에 Association을 추가하고 노출했으며,
- 파일 업로드 기능을 추가했고,
- Consumption View의 Selection List에 요소를 추가하고 Element/View Annotation을 추가했으며,
- 메타데이터 확장에 UI 어노테이션을 추가했고,
- 미리보기를 확인했습니다.

다음 실습으로 계속 진행할 수 있습니다 – **[Exercise 3: BO Behavior 개선 – Early Numbering](../ex03/README.md)**

---

<!--
## 부록
[^페이지 상단으로](#)

기본 BO 뷰, Projected BO 뷰(BO Projection View라고도 함) 및 CDS 메타데이터 확장에 대한 소스 코드는 [sources](sources) 폴더에서 찾을 수 있습니다. 자리 표시자 `###`의 모든 발생을 그룹 ID로 바꾸는 것을 잊지 마십시오.

- ![document](images/doc.png) [CDS view ZRAP100_R_TRAVELTP_###](sources/EX2_DDLS_ZRAP100_R_TRAVELTP.txt)
- ![document](images/doc.png) [CDS projection view ZRAP100_C_TRAVELTP_###](sources/EX2_DDLS_ZRAP100_C_TRAVELTP.txt)
- ![document](images/doc.png) [CDS metadata extension ZRAP100_C_TRAVELTP_###](sources/EX2_DDLX_ZRAP100_C_TRAVELTP.txt)
-->
