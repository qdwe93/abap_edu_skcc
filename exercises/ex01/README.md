[Home - RAP100](../../#exercises)

# 연습문제 1: 데이터베이스 테이블 생성 및 UI 서비스 생성

## 소개

이 연습에서는 기본 RAP business object와 함께 transactional UI service를 생성합니다.

먼저 ABAP package, 데이터베이스 테이블, 그리고 데이터베이스 테이블을 데모 데이터로 채울 ABAP class를 생성합니다. 그런 다음 ADT 마법사를 사용하여 데이터베이스 테이블 위에 UI service에 필요한 모든 RAP 아티팩트를 생성합니다. 여기에는 CDS data model, behavior definition, service definition, service binding이 포함됩니다. 이후에는 SAP Fiori elements preview를 사용하여 _Travel_ 애플리케이션을 게시하고 확인할 것입니다.

- [1.1 - Package 생성](#exercise-11-create-package)
- [1.2 - 데이터베이스 테이블 생성](#exercise-12-create-database-table)
- [1.3 - 데이터 생성기 클래스 생성](#exercise-13-create-data-generator-class)
- [1.4 - Transactional UI service 생성](#exercise-14-generate-the-transactional-ui-service)
- [1.5 - Metadata extension 조정](#excercise-15-adjust-metadata-extension)
- [1.6 - Travel App 게시 및 미리보기](#exercise-16-publish-and-preview-the-travel-app)
- [요약](#summary)


> **알림:**
> 아래 연습 단계에서 플레이스홀더 **`###`** 의 모든 항목을 자신의 그룹 ID로 바꾸는 것을 잊지 마십시오.
> 이를 위해 ADT의 **Replace All** (**Ctrl+F**) 기능을 사용할 수 있습니다.
> 아직 그룹 ID가 없다면 [Getting Started - Group ID](../ex0/README.md#group-id) 섹션을 확인하십시오.

## Exercise 1.1: Package 생성
[^Top of page](#)

> 연습용 package를 생성합니다![package](images/adt_package.png).
> 이 ABAP package는 이번 실습 세션의 여러 연습에서 생성할 모든 아티팩트를 포함하게 됩니다.

 <details>
  <summary>자세히 보기!</summary>

   1. ADT의 **Project Explorer** 에서 **`ZLOCAL`** package를 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **New** > **ABAP Package** 를 선택합니다.

      <!-- ![package](images/p1a.png)  -->
      <img src="images/p1a.png" alt="table" width="50%">

   2. 필요한 정보를 입력합니다 (`###`은 그룹 ID이며, 숫자와 문자의 적절한 조합을 선택하십시오. 예: **`088`** 또는 **`ZT1`**):
       - Name: **`ZRAP100_###`**
       - Description: _**`RAP100 Package ###`**_
       - **Add to favorites package** 체크박스를 선택합니다.
       - Superpackage: **`ZLOCAL`**

      **Next >** 를 클릭합니다.

      <!-- ![package](images/p1b.png)  -->
      <img src="images/p1b.png" alt="table" width="50%">

   3. transport request를 선택하고, 설명을 입력한 후(예: _**RAP100 Package ###**_), **Finish** 를 클릭합니다.

      <!-- ![package](images/p1c.png)  -->
      <img src="images/p1c.png" alt="table" width="50%">

</details>

## Exercise 1.2: 데이터베이스 테이블 생성
[^Top of page](#)

> _Travel_ 데이터를 저장할 데이터베이스 테이블![table](images/adt_tabl.png)을 생성합니다.
> Travel 엔티티는 여행사 ID나 고객 ID, 여행 예약의 전반적인 상태, 여행 가격과 같은 일반적인 여행 데이터를 정의합니다.

 <details>
  <summary>자세히 보기!</summary>

   1. ABAP package **`ZRAP100_###`** 를 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **New** > **Other ABAP Repository Object** 를 선택합니다.

      <!-- ![table](images/p2a.png) -->
      <img src="images/p2a.png" alt="table" width="50%">

   2. **database table**을 검색하여 선택한 후 **Next >** 를 클릭합니다.

      <!--  ![table](images/p2b.png) -->
      <img src="images/p2b.png" alt="table" width="50%">

   3. 필요한 정보를 입력하고 (`###`은 그룹 ID) **Next >** 를 클릭합니다.
      - Name: **`ZRAP100_ATRAV###`**
      - Description: _**`Travel data`**_

      <!-- ![table](images/p2c.png)-->
      <img src="images/p2c.png" alt="table" width="50%">

   4. transport request를 선택하고 **Finish** 를 클릭하여 데이터베이스 테이블을 생성합니다.

   5. 기본 코드를 아래 제공된 코드 스니펫으로 교체하고, **Replace All** 기능(**Ctrl+F**)을 사용하여 플레이스홀더 **`###`** 의 모든 항목을 그룹 ID로 바꿉니다.

      > **힌트**: 코드 스니펫 위에 마우스를 올리고 오른쪽 상단에 나타나는 _Copy raw contents_ 아이콘 <img src="images/copyrawcontents.png" alt="table" width="30px">을 선택하여 복사합니다.

      <pre lang="ABAP">
      @EndUserText.label : 'Travel data'
      @AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
      @AbapCatalog.tableCategory : #TRANSPARENT
      @AbapCatalog.deliveryClass : #A
      @AbapCatalog.dataMaintenance : #RESTRICTED
      define table zrap100_atrav### {
        key client            : abap.clnt not null;
        key travel_id         : /dmo/travel_id not null;
        agency_id             : /dmo/agency_id;
        customer_id           : /dmo/customer_id;
        begin_date            : /dmo/begin_date;
        end_date              : /dmo/end_date;
        @Semantics.amount.currencyCode : 'zrap100_atrav###.currency_code'
        booking_fee           : /dmo/booking_fee;
        @Semantics.amount.currencyCode : 'zrap100_atrav###.currency_code'
        total_price           : /dmo/total_price;
        currency_code         : /dmo/currency_code;
        description           : /dmo/description;
        overall_status        : /dmo/overall_status;
        attachment            : /dmo/attachment;
        mime_type             : /dmo/mime_type;
        file_name             : /dmo/filename;
        created_by            : abp_creation_user;
        created_at            : abp_creation_tstmpl;
        local_last_changed_by : abp_locinst_lastchange_user;
        local_last_changed_at : abp_locinst_lastchange_tstmpl;
        last_changed_at       : abp_lastchange_tstmpl;
      }
      </pre>

      <img src="images/Picture22x.png" alt="table" width="60%">

   6. 변경 사항을 저장![save icon](images/adt_save.png)하고 활성화![activate icon](images/adt_activate.png)합니다.

</details>

## Exercise 1.3: 데이터 생성기 클래스 생성
[^Top of page](#)

> 데모 _travel_ 데이터를 생성할 ABAP class![class](images/adt_class.png)를 생성합니다.

 <details>
  <summary>자세히 보기!</summary>

   1. ABAP package **`ZRAP100_###`** 를 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **New** > **ABAP Class** 를 선택합니다.

      <!--  ![class](images/p3a.png) -->
      <img src="images/p3a.png" alt="table" width="70%">

   2. 필요한 정보를 입력하고 (`###`은 그룹 ID) **Next >** 를 클릭합니다.
      - Name: **`ZCL_RAP100_GEN_DATA_###`**
      - Description: _**`Generate demo data`**_

      <!-- ![class](images/p3b.png) -->
      <img src="images/p3b.png" alt="table" width="60%">

   3. transport request를 선택하고 **Finish** 를 클릭하여 클래스를 생성합니다.

   4. 기본 소스 코드를 아래에 링크된 소스 코드 문서 **`ZRAP100_GEN_DATA_###`** 에 제공된 코드 스니펫으로 교체하고, **Replace All** 기능(**Ctrl+F**)을 사용하여 플레이스홀더 **`###`** 의 모든 항목을 그룹 ID로 바꿉니다.

      **Shift+F1**을 눌러 **ABAP Pretty Printer** (**ABAP Formatter**) 기능을 사용하여 소스 코드의 서식을 지정할 수 있습니다. 시스템에서 처음 사용하는 경우 구성을 요청받게 됩니다.

      **힌트**: 문서를 새 탭에서 엽니다. 문서 편집기에서 툴바의 _Copy raw contents_ 아이콘 <img src="images/copyrawcontents.png" alt="table" width="30px">을 사용하여 전체 소스 코드를 복사합니다.

       ![document](images/doc.png) **소스 코드 문서**: ![class icon](images/adt_class.png)[Class ZRAP100_GEN_DATA_###](sources/EX1_CLASS_ZCL_RAP100_GEN_DATA.txt)

   5. 변경 사항을 저장![save icon](images/adt_save.png)하고 활성화![activate icon](images/adt_activate.png)합니다.

   6. 콘솔 애플리케이션을 실행합니다.

      이를 위해, ABAP class ![class](images/adt_class.png)**`ZCL_RAP100_GEN_DATA_###`** 를 선택하고, 실행 버튼 > **Run As** > **ABAP Application (Console) F9** 를 선택하거나 **F9** 를 누릅니다.

      <!-- ![class](images/p4.png) -->
      <img src="images/p4.png" alt="table" width="70%">

      _ABAP Console_ 에 메시지가 표시됩니다.

      <!-- ![class](images/p4a.png)  -->
      <img src="images/p4a.png" alt="table" width="70%">

   7. 데이터베이스 테이블 ![table](images/adt_tabl.png)**`ZRAP100_ATRAV###`** 을 열고 **F8**을 눌러 데이터 미리보기를 시작하고 채워진 데이터베이스 항목, 즉 _travel_ 데이터를 표시합니다.

      ![class](images/p5.png)

</details>


## Exercise 1.4: Transactional UI service 생성
[^Top of page](#)

> 내장된 ADT 생성기를 사용하여 OData v4 기반 UI service와 RAP business object (BO)를 필요한 모든 ABAP 아티팩트(예: CDS view entities, behavior definition 및 implementation)와 함께 생성합니다.
> 생성된 비즈니스 서비스는 transactional, draft-enabled이며, Fiori elements 앱 생성을 위해 UI 시맨틱으로 강화됩니다.

  <details>
  <summary>자세히 보기!</summary>

   1. 데이터베이스 테이블 ![table](images/adt_tabl.png)**`ZRAP100_ATRAV###`** 를 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **Generate ABAP Repository Objects** 를 선택합니다.

       <!-- ![class](images/p6a.png)  -->
       <img src="images/p6a.png" alt="table" width="50%">





  **주의:**
  생성기 UI는 클라우드 기반 시스템(ABAP Environment)과 온프레미스 시스템에서 다르게 보입니다.

  <details>

  --------------------------------------------------------------------------------------

  <summary> **Cloud System** 의 경우 자세히 보기</summary>

   1. **OData UI Service** 를 선택하고 **Next >** 를 클릭합니다.

      <img src="images/newgenerator2.png" alt="table" width="100%">

   2. **Next >** 를 클릭합니다.

      <img src="images/newgenerator3.png" alt="table" width="100%">

   3. 설명을 입력합니다.

      <img src="images/newgenerator4.png" alt="table" width="100%">

  </details>

  --------------------------------------------------------------------------------------

  <details>
   <summary> **On Premise System** 의 경우 자세히 보기</summary>

   1. 설명을 제공하고, **ABAP RESTful Application Programmind Model: UI Service** 를 선택한 후 **Next >** 를 클릭합니다.

      <img src="images/generatorxx1.png" alt="table" width="100%">

  </details>


  --------------------------------------------------------------------------------------

   <!--
       ___________________________________________________________________________________________________________________________________________________

       | **Generator: Cloud System**          |  **Generator: On Premise System**      |
       |:------------------------------------ |:-------------------------------------- |
       |1. Select **OData UI Service** and click **Next >**.  <img src="images/newgenerator2.png" alt="table" width="100%">  |1. Provide a description, select **ABAP RESTful Application Programmind Model: UI Service** and click **Next >**. <img src="images/generatorxx1.png" alt="table" width="100%">   |
       |2. Click **Next >**.  <img src="images/newgenerator3.png" alt="table" width="100%">   |    |
       |3. Enter a description. <img src="images/newgenerator4.png" alt="table" width="100%"> |    |
       -->

   1. **Configure Generator** 대화 상자에서 필요한 정보를 유지하여 데이터 모델의 이름을 제공하고 생성합니다.

      이를 위해 마법사 트리(_Business Objects_, _Data Model_ 등)를 탐색하고, 아래 표에 제공된 아티팩트 이름을 유지한 후,
      **Next >** 를 누릅니다.

      유지된 항목을 확인하고 **Next >** 를 눌러 확인합니다. 필요한 아티팩트가 생성됩니다.

      > ℹ **네이밍 컨벤션에 대한 정보**
      > 이 연습에서는 SAP S/4HANA의 Virtual Data Model (VDM)의 네이밍 컨벤션의 주요 측면을 사용합니다.
      > VDM에 대한 자세한 정보는 SAP Help 포털에서 찾을 수 있습니다: **[여기](https://help.sap.com/docs/SAP_S4HANA_CLOUD/0f69f8fb28ac4bf48d2b57b9637e81fa/8a8cee943ef944fe8936f4cc60ba9bc1.html)**.

      <!--
      > ⚠ **주의**
      > _**Invalid XML format of the response**_ 오류 메시지가 나타나면, 이는 ADT 도구 버전 1.26의 버그일 수 있습니다.
      > ADT 플러그인을 최신 버전으로 업데이트하면 이 문제가 해결됩니다.
      -->

      > ⚠ **주의** ⚠
      > 마법사에서 **제안된 모든 이름을 아래에 제공된 이름으로 반드시 교체**하십시오.
      > 이는 다음 연습에서 제공되는 코드 스니펫의 정확성을 보장하기 위해 중요합니다.
      >


      | **RAP Layer**                          | **Artefacts**                   | **Artefact Names**                                            |
      |----------------------------------------|---------------------------------|---------------------------------------------------------------|
      | **Business Object**                    |                                 |                                                               |
      |                                        | **Data Model**                  | CDS Entity Name: **`ZRAP100_R_TRAVELTP_###`**                 |
      |                                        |                                 | CDS Entity Name Alias: **`Travel`**                           |
      |                                        | **Behavior**                    | Implementation Behavior Class: **`ZRAP100_BP_TRAVELTP_###`**  |
      |                                        |                                 | Draft Table Name: **`ZRAP100_DTRAV###`**                      |
      | **Service Projection**                 | **Service Projection Entity**   | CDS Entity Name: **`ZRAP100_C_TRAVELTP_###`**                 |
      |                                        | **Service Projection Behavior** | Behavior Implementation Class: **`ZRAP100_BP_C_TRAVELTP_###`**|
      | **Business Service**                   |                                 |                                                               |
      |                                        | **Service Definition**          | Service Definition Name: **`ZRAP100_UI_TRAVEL_###`**          |
      |                                        | **Service Binding**             | Service Binding Name: **`ZRAP100_UI_TRAVEL_O4_###`**          |
      |                                        |                                 | Binding Type: **`OData V4 - UI`**                             |

      <!-- ![generator](images/p7a.png)  -->
      <img src="images/p7a.png" alt="table" width="50%">

      <!-- ![generator](images/p7b.png)   -->
      <img src="images/p7b.png" alt="table" width="50%">

      <img src="images/p7c.png" alt="table" width="50%">

   2. **Project Explorer**로 이동하여, package ![package](images/adt_package.png)**`ZRAP100_###`** 를 선택하고, **F5** 를 눌러 새로고침한 후, 생성된 모든 ABAP repository object를 확인합니다.

      <!-- ![class](images/p7d.png) -->
      <img src="images/p7dx.png" alt="table" width="50%">

아래는 다양한 RAP 레이어(Base BO, BO Projection, Business Service)에 대해 생성된 아티팩트에 대한 간략한 설명입니다.

---
  **Base Business Object (BO) `ZRAP100_R_TRAVEL_###`**

   | **Object Name**               |  **Description**         |
   |:----------------------------- |:------------------------ |
   | ![ddls icon](images/adt_ddls.png)**`ZRAP100_R_TravelTP_###`**     | (일명 _Base BO view_): 이 **data definition**은 우리 business object의 유일한 노드인 root entity _Travel_ 의 데이터 모델을 정의합니다.  |
   | ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TravelTP_###`**   | (일명 _Base BO behavior): 이 **behavior definition**은 base _Travel_ BO entity의 표준 transactional behavior의 정의를 포함합니다. 이것은 _managed_ 및 _draft-enabled_ 구현입니다.  |
   | ![tabl icon](images/adt_tabl.png)**`ZRAP100_DTRAV###`**   | (일명 _Draft table_): 이 **database table**은 런타임에 draft _travel_ 인스턴스의 데이터를 임시로 저장하는 데 사용됩니다. RAP 프레임워크에 의해 관리됩니다.    |
   | ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`**  | (일명 _Behavior pool_): 이 **ABAP class**는 base _Travel_ BO의 behavior definition `ZRAP100_R_TravelTP_###`에 정의된 behavior의 구현을 제공합니다.   |

---
  **BO Projection `ZRAP100_C_TRAVEL_###`**

  BO projection은 BO 데이터 모델 및 behavior에 대한 소비 특정 뷰를 나타냅니다.

   | **Object Name**               |  **Description**         |
   |:----------------------------- |:------------------------ |
   | ![ddls icon](images/adt_ddls.png)**`ZRAP100_C_TravelTP_###`**   | (일명 _BO projection view_): 이 **data definition**은 현재 시나리오와 관련된 root entity _Travel_ 의 projected data model을 정의하는 데 사용됩니다. 현재 기본 base BO view의 거의 모든 필드가 노출되어 있으며, view annotation `@Metadata.allowExtensions: true`를 사용하여 metadata extension의 정의가 허용됩니다.  |
   | ![bdef icon](images/adt_bdef.png)**`ZRAP100_C_TravelTP_###`**   | (일명 _BO behavior projection_): 이 **behavior definition**은 키워드 **`use`** 를 사용하여 현재 시나리오와 관련된 기본 base _Travel_ BO entity의 일부를 노출합니다. 현재 모든 표준 CUD 작업이 노출됩니다.  |
   | ![ddlx icon](images/adt_ddlx.png)**`ZRAP100_C_TravelTP_###`**   | 이 **metadata extension**은 CDS annotation을 통해 view `ZRAP100_C_TRAVEL_###` 및 그 요소에 UI 시맨틱을 주석으로 다는 데 사용됩니다. |

---
  **Business Service**

   | **Object Name**               |  **Description**         |
   |:----------------------------- |:------------------------ |
   | ![srvd icon](images/adt_srvd.png)**`ZRAP100_UI_TRAVEL_###`**  | service definition은 우리 서비스에 대한 관련 entity set을 정의하고 필요한 경우 로컬 별칭을 제공하는 데 사용됩니다. 현재 시나리오에서는 _Travel_ entity set만 노출됩니다. |
   | ![srvb icon](images/adt_srvb.png)**`ZRAP100_UI_TRAVEL_O4_###`**  | 이 service binding은 생성된 service definition을 OData V4 기반 UI service로 노출하는 데 사용됩니다. 다른 바인딩 유형(프로토콜 및 시나리오)은 service binding 마법사에서 지원됩니다.  |

---
 </details>

<!--
## Exercise 1.4: Generate the transactional UI services
[^Top of page](#)

> Create your OData v4 based UI services with the openSource based RAP generator.
> The generated business service will be transactional, draft-enabled, and enriched with UI semantics for the generation of the Fiori elements app.

>>
>> **Please Note:** (@DSAG ABAP Development Days 2022)
>>
>> Unfortunately, there is a bug in the wizard **Generate ABAP Repository Objects** of the current version of the ABAP Development Tools (ADT) and the development team is working on quickly delivering an patch for ADT to fix this issue.
>> This wizard can be used for the end-to-end generation of a RAP service based on a database table.
>>
>> As workaround, we have provided in the system `D22` a commandline based ABAP class that generates the same objects. The class is based the openSource based RAP Generator tool.
>>

  <details>
  <summary>Click to expand!</summary>

   1. Click on the *Open ABAP Development Object* icon in the toolbar or use the short cut **Ctrl+Shift+A**.

   2. Select the class **`ZRAP100_CL_RAP_GENERATOR`** and press **OK**.

       ![select class](images/1_4_100_OpenDevelopment_Object.jpg)

   3. From the menu choose **Run** -> **Run as** -> **ABAP Application (Console)** or simply press **F9**.

       ![run class](images/1_4_100_generator_class.jpg)

   4. The class checks for the existence of the package **`ZRAP100_###`** and for the existience of a table **`ZRAP100_ATRAV###`**. The output in the *Console Window* shows that       the needed artifacts have been generated.

        ![class output](images/1_4_100_result_in_console.jpg)

   Below is a brief explanation of the generated artefacts for the different RAP layers: Base BO, BO Projection, and Business Service.

---
  **Base Business Object (BO) `ZRAP100_I_TRAVEL_###`**

   | **Object Name**               |  **Description**         |
   |:----------------------------- |:------------------------ |
   | ![ddls icon](images/adt_ddls.png)**`ZRAP100_R_TravelTP_###`**     | (aka _Base BO view_): This **data definition** defines the data model of the root entity _Travel_ which is the only  node of our business object).  |
   | ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TravelTP_###`**   | (aka _Base BO behavior**): This **behavior definition** contains the definition of the standard transactional behavior of the base _Travel_ BO entity. It is a _managed_ and _draft-enabled_ implementation.  |
   | ![tabl icon](images/adt_tabl.png)**`ZRAP100_DTRAV###`**   | (aka _Draft table_): This **database table** is used to temporary store the data from draft _travel_ instances at runtime. It is managed by the RAP framework.    |
   | ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`**  | (aka _Behavior pool_): This **ABAP class** which provides the implementation of the behavior defined in the behavior definition `ZRAP100_R_TravelTP_###` of the base _Travel_ BO.   |

---
  **BO Projection `ZRAP100_C_TRAVEL_###`**

  The BO projection represents the consumption specific view on the BO data model and behavior.

   | **Object Name**               |  **Description**         |
   |:----------------------------- |:------------------------ |
   | ![ddls icon](images/adt_ddls.png)**`ZRAP100_C_TravelTP_###`**   | (aka _BO projection view_): This **data definition** is used to define the projected data model of the root entity _Travel_ relevant for the present scenario. Currently almost all fields of the underlying base BO view are exposed and the definition of metadata extension is allowed using the view annotations `@Metadata.allowExtensions: true`.  |
   | ![bdef icon](images/adt_bdef.png)**`ZRAP100_C_TravelTP_###`**   | (aka _BO behavior projection_): This **behavior definition** exposes the part of the underlying base _Travel_ BO entity which is relevant for the present scenario with the keyword **`use`**. Currently all standard CUD operations are exposed.  |
   | ![ddlx icon](images/adt_ddlx.png)**`ZRAP100_C_TravelTP_###`**   | This **metadata extension** is used to annotate view `ZRAP100_C_TRAVEL_###` and its elements with UI semantics via CDS annotations. |

---
  **Business Service**

   | **Object Name**               |  **Description**         |
   |:----------------------------- |:------------------------ |
   | ![srvd icon](images/adt_srvd.png)**`ZRAP100_UI_TRAVEL_###`**  | A service definition is used to define the relevant entity sets for our service and also to provide local aliases if needed. Only the _Travel_ entity set is exposed in the present scenario. |
   | ![srvb icon](images/adt_srvb.png)**`ZRAP100_UI_TRAVEL_O4_###`**  | This service binding is used to expose the generated service definition as OData V4 based UI service. Other binding types (protocols and scenarios) are supported in the service binding wizard.  |

---

 </details>
-->

## Excercise 1.5: Metadata extension 조정

 <details>
  <summary>자세히 보기!</summary>

   1. metadata extension **`ZRAP100_C_TRAVELTP_###`** 를 열고 조정합니다.

      **attachment** 필드는 raw string(데이터 유형 `RAWSTRING`)이므로 필터 표시줄에서 사용할 수 없어, **`@UI.selectionField`** annotation이 이 필드에 허용되지 않으므로 제거해야 합니다. 따라서 attachment 필드에 대한 다음 annotation 블록을 제거하십시오:

      ```ABAP
      @UI.selectionField: [ {
          position: 10
        } ]
      ```
       <img src="images/adjust.png" alt="table" width="50%">

  2. 저장하고 활성화합니다.

</details>

## Exercise 1.6: Travel App 게시 및 미리보기
[^Top of page](#)

> service binding의 로컬 service endpoint를 게시하고 ![service binding](images/adt_srvb.png) _Fiori elements App Preview_ 를 시작합니다.
>
<!--
> ℹ 작업 중인 ABAP 시스템에 따라 1.5.1 또는 1.5.2 연습을 수행하십시오.
-->

### Exercise 1.6.1: SAP BTP, ABAP environment 또는 SAP S/4HANA, Public Cloud Edition에서 Travel App 게시 및 미리보기

> **Service Binding editor** 에서 service binding의 로컬 service endpoint를 게시하고 _Fiori elements App Preview_ 를 시작합니다.
>
 <details>
  <summary>자세히 보기!</summary>

   1. service binding ![service binding](images/adt_srvb.png)**`ZRAP100_UI_TRAVEL_O4_###`** 을 열고 **Publish** 를 클릭합니다.

   2. **Entity Set and Association** 섹션에서 엔티티 **`Travel`** 을 더블 클릭하여 _Fiori elements App Preview_ 를 엽니다.

       ![class](images/p8.png)

   3. _Travel_ 앱의 **Go** 버튼을 클릭하여 데이터를 로드합니다.

   4. 결과를 확인합니다.

       ![class](images/p9.png)

</details>

<!--
### Exercise 1.5.2: Publish and Preview the Travel App on SAP S/4HANA, On-Prem or Private Cloud Edition

> Publishing the local service endpoint of your service binding does not work from within the Service Binding.
> Therefore, you have to carry out this task in the SAP Gateway Service Administration Tool (transaction **/IWFND/V4_ADMIN**).

<details>
  <summary>Click to expand!</summary>

   1. In the ADT menu, click on the button *Run ABAP Development Object as ABAP Application in SAPGUI* or press **Alt+F8**

      ![start_transaction](images/100_publish_service_binding_on_prem.png)

   2. Type **/iwfnd/v4_admin** as a search string and double-click on the entry **/IWFND/V4_ADMIN (Transaction)**

      ![v4_admin](images/110_publish_service_binding_on_prem.png)

   3. Click the button **Publish Service Groups** to get a list of service groups that can be published.

      ![v4_admin](images/120_publish_service_binding_on_prem.png)

   4. Enter following values to search for the service group of your service and press the button **Get Service Groups**

      System Alias: `LOCAL`
      Service Group ID: `Z*###*`

      ![v4_admin](images/130_publish_service_binding_on_prem.png)

   5. Select the entry `ZRAP100_UI_TRAVELTP_O4_###` from the list and press the button **Publish Service Groups**

      ![v4_admin](images/140_publish_service_binding_on_prem.png)

   6. In the following popup enter a meaningful description such as `Travel App ###`

      ![v4_admin](images/150_publish_service_binding_on_prem.png)

   7. You are now asked to provide a customizing request. Choose an existing customizing request or create a new one and choose a meaningful description.

      ![v4_admin](images/160_publish_service_binding_on_prem.png)

   8. Confirm the success message and press **Enter**.

      ![v4_admin](images/170_publish_service_binding_on_prem.png)

   9. Navigate back to your service binding in the project explorer. Right click on it and choose **Refresh**

      ![v4_admin](images/180_publish_service_binding_on_prem.png)   **

   10. Check that your service bindings is now publish and choose the entity **Travel** and press the button **Preview**

</details>
-->

## 요약
[^Top of page](#)

이제 여러분은...
- ABAP package를 생성했고,
- 데이터베이스 테이블을 만들고 데모 데이터로 채웠으며,
- transactional UI service를 생성했고,
- 로컬 service point를 게시하고 ADT에서 _Fiori elements App Preview_ 를 시작했습니다.

이제 다음 연습으로 넘어갈 수 있습니다 - **[연습문제 2: BO 데이터 모델 개선 및 OData Streams 활성화](../ex02/README.md)**.

---

<!--
## Appendix
[^Top of page](#)

Find the source code for the database table definition and the data generator class in the [sources](sources) folder. Don't forget to replace all occurences of the placeholder `###` with your group ID.

- ![document](images/doc.png) [Table ZRAP100_ATRAV###](sources/EX1_TAB_ZRAP100_ATRAV.txt)
- ![document](images/doc.png) [Class ZCL_RAP100_GEN_DATA_###](sources/EX1_CLASS_ZCL_RAP100_GEN_DATA.txt)
-->
