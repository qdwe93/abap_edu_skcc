[홈 - RAP100](../../#exercises)

# 연습문제 5: BO 기능 개선 – Validations

## 소개

이전 연습에서는 BO 엔티티 _Travel_ 의 새 인스턴스를 생성하는 동안 `OverallStatus` 필드의 초기값을 `Open` (`O`)으로 설정하기 위한 determination을 정의하고 구현했습니다( [연습 4](../ex04/README.md) 참조).

이번 연습에서는 소비자가 입력한 고객 ID가 유효한지, 시작일이 미래인지, 종료일 값이 시작일 이후인지 각각 확인하기 위해 두 개의 백엔드 validation, `validateCustomer`와 `validateDates`를 정의하고 구현할 것입니다. 이러한 validation은 UI가 아닌 백엔드에서만 수행되며, Fiori UI 또는 EML API와 같은 호출자와 독립적으로 트리거됩니다.

> ℹ **Frontend validation & Backend validations**
> Validation은 데이터 일관성을 보장하는 데 사용됩니다.
> 이름에서 알 수 있듯이, **frontend validations** 은 UI에서 수행됩니다. 더 빠른 피드백을 제공하고 불필요한 왕복(roundtrip)을 피하여 사용자 경험을 개선하는 데 사용됩니다. RAP 컨텍스트에서 frontend validations은 CDS annotation 또는 UI 로직을 사용하여 정의됩니다.
> 반면에, **backend validations** 은 백엔드에서 수행됩니다. BO behavior definition에 정의되고 각각의 behavior pool에서 구현됩니다.
> Frontend validations은 RAP 컨텍스트에서 EML API를 사용하는 등 쉽게 우회할 수 있습니다. 따라서 **backend validations은 데이터 일관성을 보장하기 위해 필수** 입니다.

- [5.1 - Validation `validateCustomer` 및 `validateDates` 정의](#연습-51-validation-validatecustomer-및-validatedates-정의)
- [5.2 - Validation `validateCustomer` 구현](#연습-52-validation-validatecustomer-구현)
- [5.3 - Validation `validateDates` 구현](#연습-53-validation-validatedates-구현)
- [5.4 - 향상된 Travel 앱 미리보기 및 테스트](#연습-54-향상된-travel-앱-미리보기-및-테스트)
- [요약](#요약)

> **알림**: 아래 연습 단계에서 접미사 자리 표시자 **`###`** 를 선택하거나 할당된 그룹 ID로 바꾸는 것을 잊지 마십시오.

### Validations에 대하여

Validation은 트리거 조건에 따라 비즈니스 오브젝트 인스턴스의 일관성을 확인하는 비즈니스 오브젝트 behavior의 선택적 부분입니다.

Validation의 트리거 조건이 충족되면 비즈니스 오브젝트의 프레임워크에 의해 암시적으로 호출됩니다. 트리거 조건은 `MODIFY` 작업 및 수정된 필드가 될 수 있습니다. 트리거 조건은 BO 런타임 동안 미리 정의된 시점인 트리거 시간에 평가됩니다. 호출된 validation은 실패한 인스턴스의 키를 `FAILED` 구조의 해당 테이블에 전달하여 일관성 없는 인스턴스 데이터가 저장되는 것을 거부할 수 있습니다. 또한, validation은 메시지를 `REPORTED` 구조의 해당 테이블에 전달하여 소비자에게 메시지를 반환할 수 있습니다.

> **추가 정보**: [Validations](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/171e26c36cca42699976887b4c8a83bf.html)

## 연습 5.1: Validation `validateCustomer` 및 `validateDates` 정의
[^맨 위로](#)

> `validateCustomer`와 `validateDates` validation을 정의합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  behavior definition ![behaviordefinition](images/adt_bdef.png) **`ZRAP100_R_TRAVELTP_###`** 를 엽니다.

2.  **`CustomerID`**, **`BeginDate`** 및 **`EndDate`** 필드에는 빈 값을 허용하지 않으므로, 아래 스크린샷과 같이 determination 뒤에 다음 코드 스니펫을 추가하여 _mandatory_ 필드로 지정합니다.

    ```abap
     field ( mandatory )
     CustomerID,
     BeginDate,
     EndDate;
    ```

    소스 코드는 다음과 같아야 합니다:

    <!-- ![validation](images/new18a.png)    -->
    <img src="images/new18a.png" alt="validation" width="60%">

3.  **`validateCustomer`** 와 **`validateDates`** validation을 정의합니다.

    이를 위해 아래 스크린샷과 같이 determination 뒤에 다음 코드 스니펫을 추가합니다.

    ```abap
    validation validateCustomer on save { create; field CustomerID; }
    validation validateDates on save { create; field BeginDate, EndDate; }
    ```

4.  draft 인스턴스가 validation에 의해 확인되고 determination이 활성화되기 전에 실행되도록 하려면, behavior definition에서 **`draft determine action prepare`** 에 지정해야 합니다.

    **`draft determine action Prepare;`** 코드 라인을 아래 스크린샷과 같이 다음 코드 스니펫으로 바꿉니다.

    ```abap
    draft determine action Prepare
    {
    validation validateCustomer;
    validation validateDates;    }
    ```

    소스 코드는 다음과 같아야 합니다:

    <!-- ![validation](images/new18.png) -->
    <img src="images/new18.png" alt="validation" width="60%">

    ** 가단한 설명**:
    -   Validations은 항상 저장 중에 호출되며 `validateCustomer on save` 키워드로 지정됩니다.
    -   `validateCustomer`는 트리거 작업이 `create`이고 트리거 필드가 `CustomerID`인 validation입니다.
    -   `validateDates`는 트리거 작업이 `create`이고 트리거 필드가 `BeginDate`와 `EndDate`인 validation입니다.

    **ℹ 힌트**:
    > BO 엔티티 인스턴스의 모든 변경 시 validation이 호출되어야 하는 경우, 트리거 조건 `create`와 `update`를 지정해야 합니다: 예: `validation validateCustomer on save { create; update; }`

5.  ![save icon](images/adt_save.png) 저장하고 ![activate icon](images/adt_activate.png) 변경사항을 활성화합니다.

6.  quick fix를 통해 _Travel_ BO 엔티티의 behavior pool의 로컬 핸들러 클래스에 적절한 **`FOR VALIDATE ON SAVE`** 메서드를 추가합니다.

    이를 위해 validation 이름 중 하나에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 열고 _**`Add all 2 missing methods of entity zrap100_r_travel_### ...`**_ 항목을 선택합니다.

    결과적으로 **`FOR VALIDATE ON SAVE`** 메서드 **`validateCustomer`** 와 **`validateDates`** 가 _Travel_ BO 엔티티의 behavior pool ![class icon](images/adt_class.png)`ZRAP100_BP_TRAVELTP_###`의 로컬 핸들러 클래스 `lcl_handler`에 추가됩니다.

    <!-- ![Travel BO Behavior Pool](images/new19.png)  -->
    <img src="images/new19.png" alt="validation" width="90%">

7.  ![save icon](images/adt_save.png) 저장하고 ![activate icon](images/adt_activate.png) 변경사항을 활성화합니다.

> 힌트:
> behavior implementation에서 `The entity "ZRAP100_R_TRAVELTP_###" does not have a validation "VALIDATECUSTOMER".` 오류 메시지가 표시되면 behavior definition을 다시 한번 활성화해 보십시오.

</details>

## 연습 5.2: Validation `validateCustomer` 구현
[^맨 위로](#)

> 소비자가 입력한 고객 ID(`CustomerID`)가 유효한지 확인하는 `validateCustomer` validation을 구현합니다.
> 각 유효하지 않은 값에 대해 적절한 메시지가 발생하여 UI에 표시되어야 합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  먼저, _Travel_ BO 엔티티의 behavior pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 로컬 핸들러 클래스 `lcl_handler`의 선언부에서 새 메서드의 인터페이스를 확인합니다.

    이를 위해 메서드 이름 **`validateCustomer`** 에 커서를 놓고 **F2** 를 눌러 **ABAP Element Info** 뷰를 열고 전체 메서드 인터페이스를 검사합니다.

    ![Travel BO Behavior Pool](images/v3.png)

    ** 가단한 설명**:
    -   **`FOR VALIDATE ON SAVE`** 추가 구문은 이 메서드가 저장 시 실행되는 validation의 구현을 제공함을 나타냅니다. Validations은 항상 저장 시 실행됩니다.
    -   validation 메서드의 시그니처:
        -   `IMPORTING` 매개변수 **`keys`** - validation이 수행되어야 하는 인스턴스의 키를 포함하는 내부 테이블.
        -   암시적 `CHANGING` 매개변수 (일명 _암시적 응답 매개변수_):
            -   **`failed`** - 오류가 발생한 데이터 세트를 식별하기 위한 정보가 있는 테이블
            -   **`reported`** - 인스턴스별 메시지용 데이터가 있는 테이블

    이제 validation 메서드를 구현할 수 있습니다.

2.  이제 클래스의 구현부에서 **`validateCustomer`** 메서드를 구현합니다.

    로직은 다음과 같은 주요 단계로 구성됩니다:
    1.  EML 구문 **`READ ENTITIES`** 를 사용하여 전송된 키(**`keys`**)의 travel 인스턴스를 읽습니다.
    2.  **`FIELDS`** 추가 구문은 읽을 필드를 지정하는 데 사용됩니다. 현재 validation에는 **`CustomerID`** 만 관련이 있습니다.
        `ALL FIELDS` 추가 구문을 사용하여 모든 필드를 읽을 수 있습니다.
    3.  **`IN LOCAL MODE`** 추가 구문은 기능 제어 및 권한 검사를 제외하는 데 사용됩니다.
    4.  전송된 (고유하고, 초기값이 아닌) 모든 고객 ID를 읽고 존재하는지 확인합니다.
    5.  초기값이거나 존재하지 않는 고객 ID(**`CustomerID`**)를 가진 전송된 모든 _travel_ 인스턴스에 대한 메시지를 준비/발생시키고
        changing 매개변수 **`reported`** 를 설정합니다.

    **`validateCustomer`** 의 현재 메서드 구현을 다음 코드 스니펫으로 바꾸고 자리 표시자 **`###`** 의 모든 발생을 그룹 ID로 바꿉니다.

    **F1 도움말** 을 사용하여 다른 ABAP 및 EML 구문에 대한 자세한 정보를 얻을 수 있습니다.

    ```ABAP
    **********************************************************************
    * Validation: Check the validity of the entered customer data
    **********************************************************************
      METHOD validateCustomer.
        "read relevant travel instance data
        READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
        ENTITY Travel
         FIELDS ( CustomerID )
         WITH CORRESPONDING #( keys )
        RESULT DATA(travels).

        DATA customers TYPE SORTED TABLE OF /dmo/customer WITH UNIQUE KEY customer_id.

        "optimization of DB select: extract distinct non-initial customer IDs
        customers = CORRESPONDING #( travels DISCARDING DUPLICATES MAPPING customer_id = customerID EXCEPT * ).
        DELETE customers WHERE customer_id IS INITIAL.
        IF customers IS NOT INITIAL.

          "check if customer ID exists
          SELECT FROM /dmo/customer FIELDS customer_id
                                    FOR ALL ENTRIES IN @customers
                                    WHERE customer_id = @customers-customer_id
            INTO TABLE @DATA(valid_customers).
        ENDIF.

        "raise msg for non existing and initial customer id
        LOOP AT travels INTO DATA(travel).

          APPEND VALUE #(  %tky                 = travel-%tky
                           %state_area          = 'VALIDATE_CUSTOMER'
                         ) TO reported-travel.

          IF travel-CustomerID IS  INITIAL.
            APPEND VALUE #( %tky = travel-%tky ) TO failed-travel.

            APPEND VALUE #( %tky                = travel-%tky
                            %state_area         = 'VALIDATE_CUSTOMER'
                            %msg                = NEW /dmo/cm_flight_messages(
                                                                    textid   = /dmo/cm_flight_messages=>enter_customer_id
                                                                    severity = if_abap_behv_message=>severity-error )
                            %element-CustomerID = if_abap_behv=>mk-on
                          ) TO reported-travel.

          ELSEIF travel-CustomerID IS NOT INITIAL AND NOT line_exists( valid_customers[ customer_id = travel-CustomerID ] ).
            APPEND VALUE #(  %tky = travel-%tky ) TO failed-travel.

            APPEND VALUE #(  %tky                = travel-%tky
                             %state_area         = 'VALIDATE_CUSTOMER'
                             %msg                = NEW /dmo/cm_flight_messages(
                                                                    customer_id = travel-customerid
                                                                    textid      = /dmo/cm_flight_messages=>customer_unkown
                                                                    severity    = if_abap_behv_message=>severity-error )
                             %element-CustomerID = if_abap_behv=>mk-on
                          ) TO reported-travel.
          ENDIF.

        ENDLOOP.
      ENDMETHOD.
    ```

3.  ![save icon](images/adt_save.png) 저장하고 ![activate icon](images/adt_activate.png) 변경사항을 활성화합니다.

</details>

## 연습 5.3: Validation `validateDates` 구현
[^맨 위로](#)

> 입력된 시작일(`BeginDate`)과 종료일(`EndDate`)의 유효성을 확인하는 `validateDates` validation을 구현합니다.
> 각 유효하지 않은 값에 대해 적절한 메시지가 발생하여 UI에 표시되어야 합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  구현 클래스 ![class](images/adt_class.png) **`ZRAP100_BP_TRAVELTP_###`** 에서 **`validateDates`** 의 현재 메서드 구현을 다음 코드 스니펫으로 바꾸고 자리 표시자 **`###`** 의 모든 발생을 그룹 ID로 바꿉니다.

    주요 구현 단계는 **`validateCustomer`** 메서드의 단계와 유사합니다.

    이 validation은 **`BeginDate`** 및 **`EndDate`** 필드에서 수행됩니다. 입력된 시작일(`BeginDate`)이 미래인지, 입력된 종료일(`EndDate`)의 값이 시작일(`BeginDate`) 이후인지 확인합니다.

    ```ABAP
    **********************************************************************
    * Validation: Check the validity of begin and end dates
    **********************************************************************
      METHOD validateDates.

        READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
          ENTITY Travel
            FIELDS (  BeginDate EndDate TravelID )
            WITH CORRESPONDING #( keys )
          RESULT DATA(travels).

        LOOP AT travels INTO DATA(travel).

          APPEND VALUE #(  %tky               = travel-%tky
                           %state_area        = 'VALIDATE_DATES' ) TO reported-travel.

          IF travel-BeginDate IS INITIAL.
            APPEND VALUE #( %tky = travel-%tky ) TO failed-travel.

            APPEND VALUE #( %tky               = travel-%tky
                            %state_area        = 'VALIDATE_DATES'
                             %msg              = NEW /dmo/cm_flight_messages(
                                                                    textid   = /dmo/cm_flight_messages=>enter_begin_date
                                                                    severity = if_abap_behv_message=>severity-error )
                            %element-BeginDate = if_abap_behv=>mk-on ) TO reported-travel.
          ENDIF.
          IF travel-BeginDate < cl_abap_context_info=>get_system_date( ) AND travel-BeginDate IS NOT INITIAL.
            APPEND VALUE #( %tky               = travel-%tky ) TO failed-travel.

            APPEND VALUE #( %tky               = travel-%tky
                            %state_area        = 'VALIDATE_DATES'
                             %msg              = NEW /dmo/cm_flight_messages(
                                                                    begin_date = travel-BeginDate
                                                                    textid     = /dmo/cm_flight_messages=>begin_date_on_or_bef_sysdate
                                                                    severity   = if_abap_behv_message=>severity-error )
                            %element-BeginDate = if_abap_behv=>mk-on ) TO reported-travel.
          ENDIF.
          IF travel-EndDate IS INITIAL.
            APPEND VALUE #( %tky = travel-%tky ) TO failed-travel.

            APPEND VALUE #( %tky               = travel-%tky
                            %state_area        = 'VALIDATE_DATES'
                             %msg                = NEW /dmo/cm_flight_messages(
                                                                    textid   = /dmo/cm_flight_messages=>enter_end_date
                                                                    severity = if_abap_behv_message=>severity-error )
                            %element-EndDate   = if_abap_behv=>mk-on ) TO reported-travel.
          ENDIF.
          IF travel-EndDate < travel-BeginDate AND travel-BeginDate IS NOT INITIAL
                                               AND travel-EndDate IS NOT INITIAL.
            APPEND VALUE #( %tky = travel-%tky ) TO failed-travel.

            APPEND VALUE #( %tky               = travel-%tky
                            %state_area        = 'VALIDATE_DATES'
                            %msg               = NEW /dmo/cm_flight_messages(
                                                                    textid     = /dmo/cm_flight_messages=>begin_date_bef_end_date
                                                                    begin_date = travel-BeginDate
                                                                    end_date   = travel-EndDate
                                                                    severity   = if_abap_behv_message=>severity-error )
                            %element-BeginDate = if_abap_behv=>mk-on
                            %element-EndDate   = if_abap_behv=>mk-on ) TO reported-travel.
          ENDIF.
        ENDLOOP.

      ENDMETHOD.
    ```

2.  ![save icon](images/adt_save.png) 저장하고 ![activate icon](images/adt_activate.png) 변경사항을 활성화합니다.

</details>

## 연습 5.4: 개선된 Travel 앱 미리보기 및 테스트
[^맨 위로](#)

이제 SAP Fiori elements 앱을 테스트할 수 있습니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

브라우저가 아직 열려 있는 경우 브라우저에서 **F5** 를 사용하여 애플리케이션을 새로 고치거나, service binding **`ZRAP100_UI_TRAVEL_O4_###`** 로 이동하여 **`Travel`** 엔티티 세트에 대한 Fiori elements 앱 미리보기를 시작할 수 있습니다.

1.  **Create** 를 클릭하여 새 항목을 만듭니다.

2.  Agency ID로 `Sunshine Travel (70001)`을 선택하고, 이름 `Theresia`를 입력하여 고객을 선택하고, 시작일로 2022년 11월 20일, 종료일로 2022년 11월 15일을 선택합니다. draft가 업데이트됩니다.

    <!-- ![Preview](images/preview3.png) --Y
    <img src="images/preview3.png" alt="Preview" width="90%">        
     
3. Now click **Create**. You should get following error messages displayed:  
   *Begin Date 11/20/2022 must not be after End Date 11/16/2022* .

    <!-- ![Preview](images/preview4.png)  -->
    <img src="images/preview3.png" alt="Preview" width="90%">

    ![Preview](images/preview5.png)

</details>

## 요약
[^맨 위로](#)

이제 여러분은...
-   behavior definition에서 두 개의 validation을 정의했고,
-   behavior pool에서 이를 구현했으며,
-   향상된 Fiori elements 앱을 미리보고 테스트했습니다.

다음 연습으로 계속 진행할 수 있습니다 – **[연습문제 6: BO 기능 개선 – Actions](../ex06/README.md)**

---
