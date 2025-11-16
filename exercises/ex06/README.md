[Home - RAP100](../../#exercises)

# [선택사항] 연습문제 6: BO 기능 개선 – Actions

## 소개

이전 연습문제에서는 두 개의 Validation을 정의하고 구현했습니다 (참조: [연습문제 5](../ex05/README.md)).

이번 연습문제에서는 애플리케이션에 다양한 Instance-bound Action (인스턴스 액션)을 추가하는 방법을 배우게 됩니다.
먼저, _Travel_ 인스턴스의 할인된 예약 수수료를 결정하기 위해 입력 파라미터가 없는 경우와 있는 경우의 non-factory 인스턴스 액션 **`deductDiscount`** 를 생성하고, _Travel_ 인스턴스를 복사하기 위한 factory 인스턴스 액션 **`copyTravel`** 을 생성합니다.

*선택* 연습문제로, _Travel_ 인스턴스의 전체 상태를 각각 _accepted_ (`A`) 또는 _rejected_ (`X`)로 설정하기 위해 두 개의 non-factory 인스턴스 액션 **`acceptTravel`** 과 **`rejectTravel`** 을 추가로 구현할 수 있습니다.

> **참고**: 여러 연습문제의 목적은 완벽한 비즈니스 시나리오를 만드는 것보다 다양한 액션 유형을 구현하는 방법을 보여주는 데 있습니다.

- [6.1: Instance-bound Action `deductDiscount` 추가하기](#exercise-61-add-the-instance-bound-action-deductdiscount)
  - [6.1.1: 인스턴스 액션 정의하기](#exercise-611-define-an-instance-action)
  - [6.1.2: 액션 메소드 구현하기](#exercise-612-implement-the-action-method)
  - [6.1.3: 액션 노출 및 테스트하기](#exercise-613-expose-and-test-the-action)
  - [6.1.4: 입력 파라미터 추가하기](#exercise-614-add-an-input-parameter)
  - [6.1.5: 액션 메소드 조정하기](#exercise-615-adjust-the-action-method)
  - [6.1.6: 파라미터가 있는 액션 테스트하기](#exercise-616-test-the-action-with-parameter)
- [6.2: Instance-bound Factory Action `copyTravel` 추가하기](#exercises-62-add-the-instance-bound-factory-action-copytravel)
  - [6.2.1: Factory Action 정의하기](#exercise-621-define-the-factory-action)
  - [6.2.2: Factory Action 구현하기](#exercise-622-implement-the-factory-action)
  - [6.2.3: Factory Action 노출 및 테스트하기](#exercise-623-expose-and-test-the-factory-action)
- [**선택사항**] [6.3: Instance-bound Actions `acceptTravel`과 `rejectTravel` 추가하기](#optional-exercise-63-add-the-instance-actions-accepttravel-and-rejecttravel)
  - [6.3.1: 액션 정의하기](#exercise-631-define-the-actions)
  - [6.3.2: 액션 메소드 구현하기](#exercise-632-implement-the-action-methods)
  - [6.3.3: 액션 노출 및 테스트하기](#exercise-633-expose-and-test-the-actions)
- [요약](#summary)

> **알림**: 아래 연습문제 단계에서 접미사 플레이스홀더 **`###`** 를 선택하거나 할당받은 그룹 ID로 반드시 교체하십시오.

### 정보: Actions
> RAP 컨텍스트에서 Action은 BO 인스턴스의 데이터를 변경하는 비표준 작업입니다.
>
> Action은 Behavior Definition에 명시되고 ABAP Behavior Pool에서 구현됩니다.
> 기본적으로 Action은 BO 엔티티의 인스턴스와 관련이 있습니다. `static` 추가 구문을 사용하면 특정 인스턴스에 국한되지 않고 전체 엔티티와 관련된 정적 액션을 정의할 수 있습니다.
>
> RAP에서 구현할 수 있는 Action의 두 가지 주요 카테고리는 다음과 같습니다:
> - **Non-factory actions**: 비표준 Behavior를 제공하는 RAP Action을 정의합니다. 커스텀 로직은 RAP 핸들러 메소드 `FOR MODIFY`에서 구현해야 합니다. Action은 기본적으로 RAP BO 엔티티 인스턴스와 관련이 있으며 인스턴스의 상태를 변경합니다. Non-factory Action은 Instance-bound (기본값)이거나 static일 수 있습니다.
> - **Factory actions**: Factory Action은 RAP BO 엔티티 인스턴스를 생성하는 데 사용됩니다. Factory Action은 Instance-bound (기본값)이거나 static일 수 있습니다. Instance-bound Factory Action은 인스턴스의 특정 값을 복사할 수 있습니다. Static Factory Action은 미리 채워진 기본값으로 인스턴스를 생성하는 데 사용할 수 있습니다.
>
> ℹ **추가 정보**: [Actions](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/83bad707a5a241a2ae93953d81d17a6b.html) **|** [CDS BDL - non-standard operations](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenbdl_nonstandard.htm) **|** [ABAP EML - response_param](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abapeml_response.htm)
> ℹ **추가 정보**: [RAP BO Contract](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/3a402c5cf6a74bc1a1de080b2a7c6978.html) **|** [RAP BO Provider API (derived types, %cid, implicit response parameters,...)](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/2a3da8a5b19e4f6b953e9a11fb5cc747.html?version=Cloud)


## Exercise 6.1: Instance-bound Action `deductDiscount` 추가하기
[^맨 위로](#introduction)

> 이제 non-factory instance-bound action인 **`deductDiscount`** 를 정의, 구현 및 노출할 것입니다. 이 액션은 자기 자신을 반환하며, _Travel_ 인스턴스의 **`BookingFee`** 에서 특정 비율을 차감하는 기능을 제공합니다.
>
> 할인율은 액션 구현에서 고정값(이 연습문제에서는 30%)으로 하거나, 최종 사용자나 호출하는 API가 입력 파라미터가 있는 액션을 통해 자유롭게 지정할 수 있도록 할 수 있습니다.
>
> 이번 연습문제에서는 입력 파라미터가 없는 액션과 있는 액션 두 가지 구현 방식에 모두 익숙해질 것입니다.

### Exercise 6.1.1: 인스턴스 액션 정의하기

> 먼저, _Travel_ 엔티티의 Behavior Definition에서 입력 파라미터가 없는 non-factory 인스턴스 액션 **`deductDiscount`** 를 정의합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. Behavior Definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TRAVELTP_###`** 로 이동하여 입력 파라미터가 없는 인스턴스 액션을 정의합니다.

   이를 위해, 아래 스크린샷과 같이 정의된 Validation 뒤에 다음 코드 스니펫을 삽입합니다.

   ```
     action deductDiscount result [1] $self;
   ```

   결과는 다음과 같아야 합니다:
   <!-- ![CDS BO Behavior Definition](images/b10.png)  -->
   <img src="images/b10.png" alt="CDS BO Behavior Definition" width="60%">

   **간단한 설명**:
   - 인스턴스 액션의 이름은 키워드 **`action`** 뒤에 명시됩니다.
   - 키워드 **`result`** 는 액션의 출력 파라미터를 정의합니다.
      - Cardinality는 대괄호(`[cardinality]`) 사이에 명시되며, 필수 항목입니다.
      - **`$self`** 는 결과 파라미터의 타입이 액션이나 함수가 정의된 엔티티와 동일한 타입임을 명시합니다. 이 연습문제에서는 _Travel_ 엔티티 타입입니다. 결과 파라미터의 반환 타입은 엔티티 또는 구조체가 될 수 있습니다.
    - **참고**: 출력 파라미터 **`result`** 는 액션이나 함수의 결과를 내부 테이블에 저장하는 데 사용할 수 있습니다. 그러나 데이터베이스에 커밋되는 액션이나 함수의 결과에는 영향을 미치지 않습니다.

    > ℹ **추가 정보**: [Action Definition](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/14ddc6b2442b4b97842af9158a1c9c44.html)

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

3. 이제 ADT Quick Fix를 사용하여 Behavior Implementation 클래스에 필요한 메소드를 선언합니다.

   액션 이름 **`deductDiscount`** 에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 엽니다.

    뷰에서 _**`Add method for action deductDiscount of entity zrap100_r_traveltp_### ...`**_ 항목을 선택하여 필요한 메소드를 로컬 핸들러 클래스에 추가합니다.

    <!-- ![Travel BO Behavior Definition](images/nn.png) -->
   <img src="images/nn.png" alt="CDS BO Behavior Definition" width="60%">

4. 변경 사항을 저장(![save icon](images/adt_save.png))합니다.

5. 메소드 이름 **`deductDiscount`** 에 커서를 놓고 **F3** 을 눌러 Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 로컬 핸들러 클래스 선언부로 이동합니다.

   ![Travel BO Behavior Pool](images/b12a.png)
   <!-- <img src="images/b12a.png" alt="CDS BO Behavior Pool" width="60%">   -->

6. 선언부에서 메소드 이름 **`deductDiscount`** 에 커서를 놓고 **F2** 를 눌러 전체 메소드 인터페이스를 확인합니다.

   <!-- ![Travel BO Behavior Pool](images/b12b.png)  -->
   <img src="images/b12b.png" alt="CDS BO Behavior Pool" width="60%">

   **간단한 설명**:
   - 메소드 이름 뒤의 **`FOR MODIFY`** 추가 구문과 importing 파라미터 뒤의 **`FOR ACTION`** 추가 구문은 이 메소드가 액션의 구현을 제공함을 나타냅니다.
   - non-factory 인스턴스 액션 `deductDiscount`의 메소드 시그니처:
     - `IMPORTING` 파라미터 **`keys`** - 액션이 실행되어야 할 인스턴스의 키를 담고 있는 테이블
     - 암묵적 `CHANGING` 파라미터 (일명 _implicit response parameters_):
       - **`result`** - 수행된 액션의 결과를 저장하는 데 사용됩니다.
       - **`mapped`** - 소비자에게 ID 매핑 정보를 제공하는 테이블.
       - **`failed`** - 오류가 발생한 데이터셋을 식별하기 위한 정보가 담긴 테이블.
       - **`reported`** - 인스턴스별 메시지를 위한 데이터가 담긴 테이블.

    >
    > **참고**:
    > 액션은 **`FOR ACTION`** 추가 구문과 함께 **`FOR MODIFY`** 메소드에서 구현됩니다. 액션 메소드의 시그니처는 항상 액션의 유형(factory 또는 non-factory, instance 또는 static)에 따라 달라집니다.
    > RAP 비즈니스 오브젝트에서 액션 작업을 구현하는 규칙은 해당 _**Implementation Contract**_ 에 설명되어 있습니다.

    > ℹ **추가 정보**: [Action Implementation](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/6edad7d113394602b4bfa37e07f37764.html)  **|**  [Implementation Contract: Action](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/de6569d4b92e40a0911c926170140beb.html)

    액션 메소드 구현을 계속 진행하십시오.

   </details>

### Exercise 6.1.2: 액션 메소드 구현하기

> 이제 _Travel_ 엔티티의 Behavior Pool에 있는 적절한 메소드에서 액션 Behavior를 구현합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 액션 메소드 **`deductDiscount`** 를 구현합니다.

   구현된 비즈니스 로직의 주요 단계:
   1. 새로운 값을 결정하기 위한 커스텀 로직 구현: 각 인스턴스에 대해 할인된 예약 수수료를 계산해야 합니다.
   2. 현재 단계에서 할인율은 30%로 고정됩니다.
   3. EML 구문 **`MODIFY`** 를 사용하여 인스턴스의 관련 필드를 수정합니다: 여기서는 **`BookingFee`** 필드만 업데이트해야 합니다.
   4. EML 구문 **`READ`** 를 사용하여 버퍼에서 데이터를 읽어 액션 결과 파라미터 **`result`** 를 채웁니다.
   5. 필요한 경우 암묵적 응답 파라미터가 채워집니다:
      - **`failed`** - 오류가 발생한 데이터셋을 식별하기 위한 정보 포함.
      - **`mapped`** - 소비자에게 ID 매핑 정보를 제공하는 테이블.
      - **`reported`** - 실패 시 인스턴스별 메시지를 위한 데이터 포함.

   현재 메소드 구현을 아래 제공된 코드 스니펫으로 교체하고, 플레이스홀더 **`###`** 의 모든 발생을 그룹 ID로 교체하십시오.

   **ABAP Pretty Printer**(**Ctrl+F1**)를 사용하여 소스 코드 서식을 지정할 수 있습니다.


   <pre lang="ABAP">
   **************************************************************************
   * Instance-bound non-factory action:
   * 지정된 할인을 예약 수수료(BookingFee)에서 차감합니다.
   **************************************************************************
   METHOD deductDiscount.
     DATA travels_for_update TYPE TABLE FOR UPDATE ZRAP100_R_TravelTP_###.
     DATA(keys_with_valid_discount) = keys.

     " 관련된 travel 인스턴스 데이터 읽기 (booking fee만)
     READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
         ENTITY Travel
         FIELDS ( BookingFee )
         WITH CORRESPONDING #( keys_with_valid_discount )
         RESULT DATA(travels).

     LOOP AT travels ASSIGNING FIELD-SYMBOL(<travel>).
         DATA(reduced_fee) = <travel>-BookingFee * ( 1 - 3 / 10 ) .

         APPEND VALUE #( %tky       = <travel>-%tky
                       BookingFee = reduced_fee
                     ) TO travels_for_update.
     ENDLOOP.

     " 할인된 수수료로 데이터 업데이트
     MODIFY ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
         ENTITY Travel
         UPDATE FIELDS ( BookingFee )
         WITH travels_for_update.

     " 액션 결과를 위해 변경된 데이터 읽기
     READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
         ENTITY Travel
         ALL FIELDS WITH
         CORRESPONDING #( travels )
         RESULT DATA(travels_with_discount).

     " 액션 결과 설정
     result = VALUE #( FOR travel IN travels_with_discount ( %tky   = travel-%tky
                                                               %param = travel ) ).
   ENDMETHOD.
   </pre>

   결과는 다음과 같아야 합니다:

   <!-- ![Travel BO Behavior Pool](images/n9a.png) -->
   <img src="images/n9a.png" alt="CDS BO Behavior Pool" width="60%">

   **간단한 설명**:
   - 제공된 구현은 대량 처리가 가능하도록 작성되었습니다. 이는 권장 사항입니다.
   - EML 구문 **`MODIFY ENTITIES ... UPDATE FIELDS`** 는 인스턴스의 특정 필드를 업데이트하는 데 사용됩니다.
   - 내부 테이블은 생성자 연산자 **`VALUE`** 를 사용하여 인라인으로 채워지므로 명시적인 선언이 필요 없습니다.
   - EML 구문 **`READ ENTITIES ... ALL FIELDS WITH CORRESPONDING`** 은 입력 파라미터 `result`를 채우기 위해 버퍼에서 업데이트된 인스턴스의 모든 필드를 읽는 데 사용됩니다.

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>

### Exercise 6.1.3: 액션 노출 및 테스트하기

> 지정된 트리거 시간에 RAP 런타임에 의해 자동으로 호출되는 Determination 및 Validation과 달리, Action은 BO Projection 레이어에서 명시적으로 노출되고 UI나 EML 구문을 통해 직접 소비자에 의해 호출되어야 합니다.
>
> 이제 BO Behavior Projection에서 액션을 노출하고 CDS Metadata Extension에서 UI 시맨틱을 강화하여 _Travel_ 앱에 적절한 버튼을 추가할 것입니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. BO Behavior Projection에서 액션을 노출합니다.

   Behavior Projection ![bdef icon](images/adt_bdef.png)**`ZRAP100_C_TRAVELTP_###`** 로 이동하여 아래 스크린샷과 같이 다음 코드 스니펫을 삽입합니다.

   키워드 **`use action`** 은 기본 BO의 Behavior가 Projection 레이어에서 사용됨을 나타냅니다.

   ```
   use action deductDiscount;
   ```

   결과는 다음과 같아야 합니다:

   ![Travel BO Behavior Projection](images/b14.png)

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

3. 액션 **`deductDiscount`** 가 Object Page에서만 _**Deduct Discount**_라는 레이블로 보이도록 UI 시맨틱을 향상시킵니다.

   이를 위해 Metadata Extension ![ddlx icon](images/adt_ddlx.png)**`ZRAP100_C_TRAVELTP_###`** 를 열고, **`OverallStatus`** 요소 앞에 위치한 기존의 모든 **`@UI`** 어노테이션 블록을 아래 스크린샷과 같이 제공된 코드 스니펫으로 교체합니다. 이 목적을 위해 **`@UI.identification`** 어노테이션의 시맨틱이 향상될 것입니다.

   **참고**: 제공된 코드 스니펫의 일부 라인은 시작 부분에 **`//`** 를 사용하여 주석 처리되어 있습니다. **이것들을 제거하지 마십시오**. 다음 연습문제 단계에서 이 라인들의 주석을 해제할 것입니다.

   <pre lang="ABAP CDS">
     @UI: {
         lineItem:       [ { position: 100, importance: #HIGH }
                           //,{ type: #FOR_ACTION, dataAction: 'copyTravel', label: 'Copy Travel' }
                           //,{ type: #FOR_ACTION, dataAction: 'acceptTravel', label: 'Accept Travel' }
                           //,{ type: #FOR_ACTION, dataAction: 'rejectTravel', label: 'Reject Travel' }
              ],
         identification: [ { position: 100 }
                          ,{ type: #FOR_ACTION, dataAction: 'deductDiscount', label: 'Deduct Discount' }
                          //,{ type: #FOR_ACTION, dataAction: 'acceptTravel', label: 'Accept Travel' }
                          //,{ type: #FOR_ACTION, dataAction: 'rejectTravel', label: 'Reject Travel' }
              ],
           textArrangement: #TEXT_ONLY
         }
   </pre>

   결과는 다음과 같아야 합니다:

   <!-- ![Travel CDS Metadata Extension](images/b15.png) -->
   <img src="images/b15.png" alt="Travel CDS Metadata Extension" width="60%">

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

5. 향상된 Fiori Elements 앱을 미리 보고 테스트합니다.

   예를 들어, _Travel_ 항목을 선택하고 Object Page로 이동한 다음, 새로운 액션 버튼 _**Deduct Discount**_ 를 클릭합니다. 액션 결과를 확인하십시오: 예약 수수료가 액션 메소드에 지정된 비율만큼 감소해야 합니다.

   ![Travel App Preview](images/preview7.png)
   <img src="images/preview7.png" alt="Travel App Preview" width="70%">

   원한다면, CDS Metadata Extension에서 List Report Page에 버튼을 정의하고, 변경 사항을 활성화한 후 앱을 다시 테스트할 수도 있습니다.

</details>


### Exercise 6.1.4: 입력 파라미터 추가하기
[^맨 위로](#introduction)

> 최종 사용자나 호출하는 API가 런타임에 _Travel_ 인스턴스의 예약 수수료(**`BookingFee`**)에서 차감할 비율을 자유롭게 지정할 수 있도록 액션 **`deductDiscount`** 에 입력 파라미터(**`discount_percent`**)를 추가할 것입니다.
>
> 액션 입력 파라미터는 Abstract CDS Entity(_abstract entities_)로 모델링됩니다. 이 예에서는 이 목적을 위해 단 하나의 필드 **`discount_percent`** 를 포함하는 구조체를 정의하는 abstract entity **`/dmo/a_travel_discount`** 를 사용할 것입니다. 이 엔티티는 _Flight Reference Scenario_ 의 `/DMO/FLIGHT_DRAFT` 패키지에 있습니다.

> ℹ **정보**: Abstract CDS Entity는 CDS 엔티티의 타입 속성을 정의합니다. 결과적으로, CDS 어노테이션을 사용하여 요소 수준이나 파라미터 수준에서 메타데이터를 제공하며, 해당 구현이나 기본 지속성(persistency)은 없습니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 데이터 정의 ![ddls icon](images/adt_ddls.png)**`/DMO/A_Travel_Discount`** 에 정의된 abstract entity를 살펴보겠습니다. 단축키 **Ctrl+Shift+A** 를 사용하여 데이터 정의를 열 수 있습니다.

   <!-- ![CDS Abstract entity](images/b9.png) -->
   <img src="images/b9.png" alt="CDS Abstract entity" width="60%">

   **간단한 설명**:
   - Abstract entity는 **`define abstract entity`** 구문 다음에 CDS 엔티티 이름을 사용하여 정의됩니다.
   - 현재 abstract entity는 단 하나의 필드 또는 요소를 가진 구조체를 정의합니다. 요소 이름(**`discount_percent`**)과 요소 타입(**`abap.int1`**)이 명시되어 있습니다.
   - 여기서는 해당되지 않지만, 필요한 경우 다음이 가능합니다...
      - 요소 어노테이션 `@EndUserText.label`을 사용하여 레이블을 지정합니다.
      - 요소 어노테이션 `@Consumption.valueHelpDefinition`을 사용하여 값 도움말을 지정합니다.
      - 요소 어노테이션 `@UI.hidden`을 사용하여 요소를 숨깁니다.

2. Behavior Definition ![bdef icon](images/adt_bdef.png) **`ZRAP100_R_TRAVELTP_###`** 로 이동하여 액션 정의에 **` parameter /dmo/a_travel_discount `** 추가 구문을 추가합니다.

   소스 코드는 이제 다음과 같아야 합니다:

   ```
   action deductDiscount parameter /dmo/a_travel_discount result [1] $self;
   ```

   Abstract entity **`/dmo/a_travel_discount`** 는 키워드 **`parameter`** 뒤에 사용되어 파라미터 구조를 명시합니다. 현재 액션은 abstract entity에 정의된 대로 단 하나의 파라미터(**`discount_percent`**)만 가집니다.

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>


### Exercise 6.1.5: 액션 메소드 조정하기

> 이제 Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 에서 입력 파라미터 **`deduct_discount`** 를 사용하는 인스턴스 non-factory 액션 **`deductDiscount`** 의 비즈니스 로직 구현을 조정할 것입니다.
>
> `0`보다 크고 `100`보다 작은 입력값만 허용됩니다.

> ℹ **정보**:
> 액션 유형에 따라 `IMPORTING` 파라미터 **`keys`** 는 다른 구성요소를 가집니다.
> 파라미터 입력을 위한 파라미터 구조체 **`%param`** 은 파라미터가 있는 액션에 의해 임포트됩니다. 이 파라미터 구조체는 전달된 입력 파라미터의 값에 접근하는 데 사용됩니다: 현재 시나리오에서는 **`deduct_discount`** 입니다 - 즉, *`%param-deduct_discount`*.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 로 이동하여 액션 메소드 **`deductDiscount`** 를 조정합니다.

   현재 로직을 조정하려면, 파라미터 구조체 **`%param`** 에서 전달된 할인 값을 가져와 유효성을 검사해야 합니다.

   현재 비즈니스 로직의 필요한 조정 사항:
   1. 전달된 모든 _Travel_ 인스턴스에 대해: 파라미터 구조체 **`%param`** 에서 지정된 할인 값의 유효성을 읽고 확인한 후, 유효하지 않은 값(0 < `%param-deduct_discount` <= 100을 만족하지 않는 값)을 제거합니다.
   2. 새로운 값을 결정하기 위한 커스텀 로직 구현: 각 인스턴스에 대해 할인된 예약 수수료는 고정된 할인율(30%) 대신 해당 **`%param-deduct_discount`** 값에 따라 계산되어야 합니다.

   현재 메소드 구현을 아래 제공된 코드 스니펫으로 교체하십시오. 플레이스홀더 **`###`** 의 모든 발생을 그룹 ID로 교체하는 것을 잊지 마십시오.

   **ABAP Pretty Printer**(**Ctrl+F1**)를 사용하여 소스 코드 서식을 지정할 수 있습니다.

   <pre lang="ABAP">
   **************************************************************************
   * 파라미터 `deductDiscount`를 사용하는 Instance-bound non-factory action:
   * 지정된 할인을 예약 수수료(BookingFee)에서 차감합니다.
   **************************************************************************
    METHOD deductDiscount.
      DATA travels_for_update TYPE TABLE FOR UPDATE ZRAP100_R_TravelTP_###.
      DATA(keys_with_valid_discount) = keys.

      " 유효하지 않은 할인 값 확인 및 처리
      LOOP AT keys_with_valid_discount ASSIGNING FIELD-SYMBOL(<key_with_valid_discount>)
        WHERE %param-discount_percent IS INITIAL OR %param-discount_percent > 100 OR %param-discount_percent <= 0.

        " 유효하지 않은 할인 값을 적절하게 보고
        APPEND VALUE #( %tky                       = <key_with_valid_discount>-%tky ) TO failed-travel.

        APPEND VALUE #( %tky                       = <key_with_valid_discount>-%tky
                        %msg                       = NEW /dmo/cm_flight_messages(
                                                         textid = /dmo/cm_flight_messages=>discount_invalid
                                                         severity = if_abap_behv_message=>severity-error )
                        %element-TotalPrice        = if_abap_behv=>mk-on
                        %op-%action-deductDiscount = if_abap_behv=>mk-on
                      ) TO reported-travel.

        " 유효하지 않은 할인 값 제거
        DELETE keys_with_valid_discount.
      ENDLOOP.

      " 유효한 할인 값이 있는지 확인하고 진행
      CHECK keys_with_valid_discount IS NOT INITIAL.

      " 관련된 travel 인스턴스 데이터 읽기 (booking fee만)
      READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
        ENTITY Travel
          FIELDS ( BookingFee )
          WITH CORRESPONDING #( keys_with_valid_discount )
        RESULT DATA(travels).

      LOOP AT travels ASSIGNING FIELD-SYMBOL(<travel>).
        DATA percentage TYPE decfloat16.
        DATA(discount_percent) = keys_with_valid_discount[ key draft %tky = <travel>-%tky ]-%param-discount_percent.
        percentage =  discount_percent / 100 .
        DATA(reduced_fee) = <travel>-BookingFee * ( 1 - percentage ) .

        APPEND VALUE #( %tky       = <travel>-%tky
                        BookingFee = reduced_fee
                      ) TO travels_for_update.
      ENDLOOP.

      " 할인된 수수료로 데이터 업데이트
      MODIFY ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
        ENTITY Travel
         UPDATE FIELDS ( BookingFee )
         WITH travels_for_update.

      " 액션 결과를 위해 변경된 데이터 읽기
      READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
        ENTITY Travel
          ALL FIELDS WITH
          CORRESPONDING #( travels )
        RESULT DATA(travels_with_discount).

      " 액션 결과 설정
      result = VALUE #( FOR travel IN travels_with_discount ( %tky   = travel-%tky
                                                              %param = travel ) ).
    ENDMETHOD.
   </pre>

   결과는 다음과 같아야 합니다:

   ![Travel BO Behavior Pool](images/n9b.png)

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>

### Exercise 6.1.6: 파라미터가 있는 액션 테스트하기

> 이제 향상된 _Travel_ 앱에서 _**Deduct Discount**_ 액션 버튼의 새로운 동작을 테스트할 수 있습니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 향상된 Fiori elements _Travel_ 앱을 미리 보고 테스트합니다.

   특정 항목의 Object Page로 이동하여 새로운 액션 버튼 _**Deduct Discount**_ 를 클릭합니다.

   이제 유효한 할인 값(즉, > 0 이고 <= 100)을 입력하고 확인하라는 대화 상자가 나타납니다.

   결과를 확인하십시오.

   <!-- ![Travel App Preview](images/preview7a.png)  -->
   <img src="images/preview7a.png" alt="Travel App Preview" width="80%">

   유효하지 않은 값으로도 테스트를 반복할 수 있습니다.

   ![Travel App Preview](images/preview7b.png)
   <!-- <img src="images/preview7b.png" alt="Travel App Preview" width="40%">         -->

</details>

## Exercise 6.2: Instance-bound Factory Action `copyTravel` 추가하기
[^맨 위로](#introduction)

> 이제 하나 이상의 `travel` 인스턴스를 복사하고 복사된 데이터를 기반으로 새 인스턴스를 생성하는 데 사용되는 instance-bound factory action인 **`copyTravel`** 을 정의, 구현 및 노출할 것입니다. 새로운 travel ID는 unmanaged internal early numbering에 의해 새 travel 인스턴스에 할당됩니다.

### Exercise 6.2.1: Factory Action 정의하기

> Behavior Definition에서 인스턴스 factory action **`copyTravel`** 을 정의합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. Behavior Definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TRAVELTP_###`** 로 이동하여 이전 단계에서 정의한 액션 뒤에 다음 코드 스니펫을 삽입합니다.

   ```ABAP
   factory action copyTravel [1];
   ```

   결과는 다음과 같아야 합니다:

   <!-- ![Travel BO Behavior Definition](images/n11a.png) -->
   <img src="images/n11a.png" alt="Travel BO Behavior Definition" width="60%">

   **간단한 설명**:
   Factory action에는 다음과 같은 차이점을 제외하고 instance non-factory action과 동일한 규칙이 적용됩니다:
   - 인스턴스 factory action은 이름 앞에 키워드 **`factory action`** 으로 명시됩니다.
   - 출력 파라미터는 허용되지 않습니다. Factory action은 항상 하위 엔티티 인스턴스를 포함할 수 있는 하나의 새로운 BO 엔티티 인스턴스를 생성합니다. 따라서 **`result`** 파라미터를 명시할 필요가 없습니다.
   - Factory action의 Cardinality는 항상 **`[1]`**이어야 합니다.
   - Factory action의 결과는 BDEF 파생 타입 **`%cid`** 와 새로 생성된 엔티티 인스턴스의 키 간의 매핑을 통해 암묵적 응답 파라미터 **`mapped`** 에서 반환됩니다.

  > ℹ 추가 정보는 여기에서 찾을 수 있습니다: [CDS BDL - action, factory](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenbdl_action_factory.htm)

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>

### Exercise 6.2.2: Factory Action 구현하기

> 기본 BO Behavior Pool에서 인스턴스 factory action **`coyTravel`** 을 구현합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 먼저 Behavior Pool에 필요한 메소드를 선언합니다.

   Behavior Definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TRAVELTP_###`** 로 이동하여 액션 이름 **`copyTravel`** 에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 엽니다.

   뷰에서 _**`Add method for action copyTravel of entity zrap100_r_traveltp_### ...`**_ 항목을 선택하여 필요한 메소드를 로컬 핸들러 클래스에 추가합니다.

   결과는 다음과 같아야 합니다:

   ![Travel BO Behavior Definition](images/n12a.png)

2. Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 로컬 핸들러 클래스 선언부로 이동하여 메소드 이름 **`copyTravel`** 에 커서를 놓고 **F2** 를 누른 다음 전체 메소드 인터페이스를 확인합니다.

   ![Travel BO Behavior Pool](images/n13.png)

3. Behavior Pool ![class icon](images/adt_class.png) **`ZRAP100_BP_TRAVELTP_###`** 에서 factory action **`copyTravel`** 을 구현합니다.

   > ℹ **정보**:
   > Factory action의 구현 메소드는 파라미터 구조체 **`%param`** 을 임포트하며, 이 구조체는 **`%is_draft`** 구성요소를 가집니다. 이 구성요소는 호출하는 EML API가 생성될 새 인스턴스의 상태를 나타내는 데 사용할 수 있습니다:
   > - `%is_draft` = `00` : 새 인스턴스는 active 인스턴스로 생성되어야 합니다. 즉, 영속화되어야 합니다.
   > - `%is_draft` = `01` : 새 인스턴스는 draft 인스턴스로 생성되어야 합니다. 즉, 먼저 draft 테이블에만 저장됩니다.

   로직은 다음 단계로 구성됩니다:
   1. ID가 초기값인 모든 _travel_ 인스턴스를 제거하고 복사할 전송된 _travel_ 키에서 데이터를 읽습니다.
   2. 생성될 모든 새로운 _travel_ 인스턴스를 담을 travel 컨테이너(itab)를 채웁니다. 복사된 데이터는 필요에 따라 조정됩니다.
      - 새로운 엔티티의 상태를 나타내는 구성요소 **`%param-%is_draft`** 를 평가하고, 그에 따라 새 인스턴스의 상태를 설정해야 합니다.
      - 이 연습문제에서는 구현된 validation `validateDates` 때문에 시작일(`BeginDate`)과 종료일(`EndDate`)을 조정하고, 새로운 `travel` 인스턴스의 전체 상태를 `Open`(`O`)으로 설정합니다.
   3. EML 구문 **`MODIFY ENTITIES...CREATE`** 를 사용하여 새로운 _Travel_ 인스턴스를 생성하고, 이는 매핑된 데이터를 반환합니다.
   4. **`mapped`** 구조체에 결과 집합을 설정합니다 - 특히 이 예에서는 내부 테이블 **`mapped-travel`** 에 설정합니다.

   이를 위해 현재 메소드 구현을 아래 제공된 코드 스니펫으로 교체하고, 플레이스홀더 **`###`** 의 모든 발생을 그룹 ID로 교체하십시오.

   <pre lang="ABAP">
   **************************************************************************
   * Instance-bound factory action `copyTravel`:
   * 기존 travel 인스턴스 복사하기
   **************************************************************************
    METHOD copyTravel.
       DATA:
         travels       TYPE TABLE FOR CREATE zrap100_r_traveltp_###\\travel.

       " %cid가 초기값인 travel 인스턴스 제거 (즉, 호출 API에 의해 설정되지 않음)
       READ TABLE keys WITH KEY %cid = '' INTO DATA(key_with_inital_cid).
       ASSERT key_with_inital_cid IS INITIAL.

       " 복사할 travel 인스턴스에서 데이터 읽기
       READ ENTITIES OF zrap100_r_traveltp_### IN LOCAL MODE
         ENTITY travel
          ALL FIELDS WITH CORRESPONDING #( keys )
       RESULT DATA(travel_read_result)
       FAILED failed.

       LOOP AT travel_read_result ASSIGNING FIELD-SYMBOL(<travel>).
         " 새로운 travel 인스턴스 생성을 위한 travel 컨테이너 채우기
         APPEND VALUE #( %cid      = keys[ KEY entity %key = <travel>-%key ]-%cid
                         %is_draft = keys[ KEY entity %key = <travel>-%key ]-%param-%is_draft
                         %data     = CORRESPONDING #( <travel> EXCEPT TravelID )
                      )
           TO travels ASSIGNING FIELD-SYMBOL(<new_travel>).

         " 복사된 travel 인스턴스 데이터 조정
         "" BeginDate는 시스템 날짜와 같거나 그 이후여야 함
         <new_travel>-BeginDate     = cl_abap_context_info=>get_system_date( ).
         "" EndDate는 BeginDate 이후여야 함
         <new_travel>-EndDate       = cl_abap_context_info=>get_system_date( ) + 30.
         "" 새 인스턴스의 OverallStatus는 open ('O')으로 설정해야 함
         <new_travel>-OverallStatus = travel_status-open.
       ENDLOOP.

       " 새로운 BO 인스턴스 생성
       MODIFY ENTITIES OF zrap100_r_traveltp_### IN LOCAL MODE
         ENTITY travel
           CREATE FIELDS ( AgencyID CustomerID BeginDate EndDate BookingFee
                           TotalPrice CurrencyCode OverallStatus Description )
             WITH travels
         MAPPED DATA(mapped_create).

       " 새로운 BO 인스턴스 설정
       mapped-travel   =  mapped_create-travel .
     ENDMETHOD.
   </pre>

   소스 코드는 다음과 같아야 합니다:

   <!-- ![Travel BO Behavior Pool](images/n14.png)   -->
   <img src="images/n14.png" alt="Travel BO Behavior Pool" width="70%">

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>

### Exercise 6.2.3: Factory Action 노출 및 테스트하기

> BO Behavior Projection과 CDS Metadata Extension에서 인스턴스 factory action을 노출하고, 향상된 Fiori elements 앱을 테스트합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 새로운 액션 **`copyTravel`** 을 BO Behavior Projection에 노출합니다.

   이를 위해 Behavior Projection ![bdef icon](images/adt_bdef.png) **`ZRAP100_C_TRAVELTP_###`** 을 열고 이전에 추가된 액션들 뒤에 다음 코드 스니펫을 삽입합니다.

   ```
   use action copyTravel;
   ```

   결과는 다음과 같아야 합니다:

   <!-- ![Travel BO Behavior Projection](images/n15.png) -->
   <img src="images/n15.png" alt="Travel BO Behavior Projection" width="60%">

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.


3. 액션 **`copyTravel`** 이 List Report Page에서만 _**Copy Travel**_ 레이블로 보이도록 UI 서비스의 UI 시맨틱을 향상시킵니다.

   이를 위해 CDS Metadata Extension ![ddlx icon](images/adt_ddlx.png)**`ZRAP100_C_TRAVELTP_###`** 을 열고, **`OverallStatus`** 요소 앞에 위치한 **`@UI.lineItem`** 어노테이션 블록에서 다음 코드 라인의 주석을 해제합니다.

   ```
   ,{ type: #FOR_ACTION, dataAction: 'copyTravel', label: 'Copy Travel' }
   ```

   결과는 다음과 같아야 합니다:

   <!-- ![Travel CDS Metadta Extension](images/b21.png) -->
   <img src="images/b21.png" alt="Travel CDS Metadta Extension" width="60%">

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

5. 향상된 Fiori elements 앱을 미리 보고 테스트합니다.

   Travel 인스턴스를 선택하고 **Copy** 를 선택합니다.

   ![Travel App Preview](images/copy.png)

   <!--  (PS: 스팀펑크 2211 버전부터 팝업이 더 이상 제공되지 않습니다.)
   복사 작업을 확인합니다.

   ![Travel App Preview](images/copy2.png)
   -->

   새로운 Travel 인스턴스가 있는 Object Page가 열립니다.

   ![Travel App Preview](images/copy3.png)

</details>

## [⚠선택사항] 연습문제 6.3: 인스턴스 액션 `acceptTravel` 및 `rejectTravel` 추가하기
[^맨 위로](#introduction)

이 단계에서는 `Travel` 엔티티에 대해 두 개의 instance-bound non-factory action인 `acceptTravel`과 `rejectTravel`을 정의, 구현 및 노출할 것입니다. 이 액션들은 주어진 하나 이상의 _Travel_ 인스턴스의 전체 상태를 각각 `Accepted`(`A`) 및 `Rejected`(`X`)로 설정하는 데 사용됩니다.

> ⚠ **선택 연습문제**:
> 이 연습문제에서 정의하고 구현하는 non-factory instance action `acceptTravel`과 `rejectTravel`은 연습문제 6.1에서 구현한 것(`deductDiscount`)과 유사합니다. 이들은 _Travel_ 앱에서 더 많은 기능을 가지고 놀 수 있도록 이 문서에 추가되었습니다.
>
> 시간이 부족하다면 다음 연습문제로 넘어가거나 [**부록** 섹션](#Appendix)에 제공된 솔루션 객체에서 소스 코드를 복사하는 것을 권장합니다.

### Exercise 6.3.1: 액션 정의하기

> 먼저, _Travel_ 엔티티의 Behavior Definition에서 인스턴스 non-factory action인 **`acceptTravel`** 과 **`rejectTravel`** 을 정의합니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. Behavior Definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TRAVELTP_###`** 로 이동하여 두 액션을 모두 정의합니다.

   이를 위해 아래 스크린샷과 같이 정의된 validation 뒤에 다음 코드 스니펫을 삽입합니다.

   ```
   action acceptTravel result [1] $self;
   action rejectTravel result [1] $self;
   ```

   ![Travel BO Behavior Definition](images/n.png)

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

3. 이제 ADT Quick Fix를 사용하여 Behavior Implementation 클래스에 필요한 메소드를 선언합니다.

   액션 이름 중 하나인 **`acceptTravel`** 또는 **`rejectTravel`** 에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 엽니다.

   **`Add all 2 missing methods of entity zrap100_r_traveltp_### ...`** 항목을 선택하여 두 메소드를 모두 Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 로컬 핸들러 클래스 `lcl_handler`에 추가합니다.

   ![Travel BO Behavior Pool](images/n2.png)

이제 두 액션의 정의가 완료되었습니다. Behavior Pool에서 두 개의 삽입된 메소드의 구현을 계속 진행하십시오.

   </details>

### Exercise 6.3.2: 액션 메소드 구현하기

> 이제 _Travel_ 엔티티의 Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 에서 필요한 액션 메소드를 구현합니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. Behavior Pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 로컬 핸들러 클래스 선언부에서 **`acceptTravel`** 과 **`rejectTravel`** 메소드의 인터페이스를 확인할 수 있습니다. 이들은 액션 메소드 **`deductDiscount`** 의 인터페이스와 유사합니다.

   이를 위해 메소드 이름 중 하나에 커서를 놓고 **F2** 를 눌러 **ABAP Element Info** 뷰를 연 다음 전체 메소드 인터페이스를 확인합니다.

   ![Travel BO Behavior Pool](images/n3.png)

    구현을 계속 진행합니다.

2. 로컬 핸들러 클래스의 구현부에서 액션 **`acceptTravel`** 을 구현합니다.
   이 액션은 **`OverallStatus`** 필드의 값을 **_Accepted_**(**`A`**)로 설정하는 데 사용됩니다.

   로직은 다음 단계로 구성됩니다:
   1. 새로운 값을 결정하기 위한 커스텀 로직을 구현합니다. 현재 시나리오에서는 **_Accepted_**(**`A`**)입니다.
   2. _travel_ 인스턴스의 관련 필드를 수정합니다. 여기서는 `OverallStatus` 필드만 업데이트하면 됩니다.
   3. 액션 결과 파라미터를 채우기 위해 버퍼에서 업데이트된 인스턴스의 전체 데이터를 읽습니다.

   이를 위해 현재 메소드 구현을 아래 제공된 코드 스니펫으로 교체하고, 플레이스홀더 **`###`** 의 모든 발생을 그룹 ID로 교체하십시오. EML 구문 및 기타 ABAP 구문에 대한 자세한 정보는 **F1 도움말** 을 사용할 수 있습니다.

   <pre lang="ABAP">
   *************************************************************************************
   * Instance-bound non-factory action: 전체 travel 상태를 'A' (accepted)로 설정
   *************************************************************************************
     METHOD acceptTravel.
       " travel 인스턴스 수정
       MODIFY ENTITIES OF zrap100_r_traveltp_### IN LOCAL MODE
         ENTITY Travel
           UPDATE FIELDS ( OverallStatus )
           WITH VALUE #( FOR key IN keys ( %tky          = key-%tky
                                           OverallStatus = travel_status-accepted ) )  " 'A'
       FAILED failed
       REPORTED reported.

       " 액션 결과를 위해 변경된 데이터 읽기
       READ ENTITIES OF zrap100_r_traveltp_### IN LOCAL MODE
         ENTITY Travel
           ALL FIELDS WITH
           CORRESPONDING #( keys )
         RESULT DATA(travels).

       " 액션 결과 파라미터 설정
       result = VALUE #( FOR travel IN travels ( %tky   = travel-%tky
                                                 %param = travel ) ).
     ENDMETHOD.
   </pre>

   소스 코드는 다음과 같아야 합니다:

   ![Travel BO Behavior Pool](images/n4.png)

   **간단한 설명**:
   - 제공된 구현은 대량 처리가 가능하도록 작성되었습니다. 이는 권장 사항입니다.
   - EML 구문 **`MODIFY ENTITIES ... UPDATE FIELDS`** 는 인스턴스의 특정 필드를 업데이트하는 데 사용됩니다.
   - 내부 테이블은 생성자 연산자 **`VALUE`** 를 사용하여 인라인으로 채워지므로 명시적인 선언이 필요 없습니다.
   - EML 구문 **`READ ENTITIES ... ALL FIELDS WITH CORRESPONDING`** 은 입력 파라미터 `result`를 채우기 위해 버퍼에서 업데이트된 인스턴스의 모든 필드를 읽는 데 사용됩니다.

3. **`OverallStatus`** 필드의 값을 **`Rejected`**(**`X`**)로 설정하는 데 사용되는 액션 **`rejectTravel`** 을 구현합니다. 비즈니스 로직은 `acceptTravel` 메소드의 로직과 유사합니다.

   이를 위해 현재 메소드 구현을 아래 제공된 코드 스니펫으로 교체하고, 플레이스홀더 **`###`** 의 모든 발생을 그룹 ID로 교체하십시오.

   <pre lang="ABAP">
   *************************************************************************************
   * Instance-bound non-factory action: 전체 travel 상태를 'X' (rejected)로 설정
   *************************************************************************************
     METHOD rejectTravel.
       " travel 인스턴스(들) 수정
       MODIFY ENTITIES OF zrap100_r_traveltp_### IN LOCAL MODE
         ENTITY Travel
           UPDATE FIELDS ( OverallStatus )
           WITH VALUE #( FOR key IN keys ( %tky          = key-%tky
                                           OverallStatus = travel_status-rejected ) )  " 'X'
       FAILED failed
       REPORTED reported.

       " 액션 결과를 위해 변경된 데이터 읽기
       READ ENTITIES OF zrap100_r_traveltp_### IN LOCAL MODE
         ENTITY Travel
           ALL FIELDS WITH
           CORRESPONDING #( keys )
         RESULT DATA(travels).

       " 액션 결과 파라미터 설정
       result = VALUE #( FOR travel IN travels ( %tky   = travel-%tky
                                                 %param = travel ) ).
     ENDMETHOD.
   </pre>

   소스 코드는 다음과 같아야 합니다:

   ![Travel BO Behavior Pool](images/n5.png)

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>


### Exercise 6.3.3: 액션 노출 및 테스트하기
> 이제 BO Behavior Projection에서 액션을 노출하고, CDS Metadata Extension에서 UI 시맨틱을 강화하여 _Travel_ 앱에 적절한 버튼을 추가할 것입니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. BO Behavior Projection에서 액션을 노출합니다.

   Behavior Projection ![bdef icon](images/adt_bdef.png)**`ZRAP100_C_TRAVELTP_###`** 로 이동하여
아래 스크린샷과 같이 다음 코드 스니펫을 삽입합니다.

   ```
   use action acceptTravel;
   use action rejectTravel;
   ```

   소스 코드는 다음과 같아야 합니다:

   ![Travel BO Behavior Projection](images/b6.png)

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

   이제 액션은 UI에서 사용할 준비가 되었지만, UI에 명시적으로 배치해야 합니다.

3. List Report Page와 Object Page에서 `Accept Travel` 및 `Reject Travel` 레이블로 액션이 보이도록 UI 서비스의 UI 시맨틱을 향상시킵니다.

   이를 위해 CDS Metadata Extension ![ddlx icon](images/adt_ddlx.png)**`ZRAP100_C_TRAVELTP_###`** 로 이동하여
   **`OverallStatus`** 요소 앞에 위치한 `@UI` 어노테이션 블록에서 관련 코드 라인의 주석을
   아래 스크린샷과 같이 해제합니다.

   ![Travel Metadata Extension](images/b7.png)

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

5. 이제 향상된 Fiori elements 앱을 미리 보고 테스트할 수 있습니다. 액션이 이제 UI에 나타나야 합니다.

   예를 들어, 전체 상태가 _Open_인 `Travel` 레코드를 선택하고 액션 버튼 _**Accept Travel**_ 또는 _**Reject Travel**_ 을 누릅니다. 전체 상태는 이제 _**Accepted**_ 또는 _**Rejected**_ 가 되어야 합니다.

   ![Travel App Preview](images/preview9.png)

</details>


## 요약
[^맨 위로](#introduction)

이제 여러분은...
- Behavior Definition에서 다양한 유형의 인스턴스 액션(단순 액션, 입력 파라미터가 있는 액션, factory action)을 정의하고,
- Behavior Implementation에서 이를 구현하고,
- BO Projection 레이어(Behavior Projection 및 Metadata Extension)에 노출하고,
- 향상된 Fiori elements 앱을 미리 보고 테스트했습니다.

다음 연습문제인 **[선택사항] [연습문제 7: BO 기능 개선 – Dynamic Feature Control](../ex07/README.md)** 로 계속 진행할 수 있습니다.

---
<!--
## Appendix
[^Top of page](#introduction)

Find the source code for the behavior definition, the behavior implementation class (aka behavior pool), the behavior projection, and the metadata extension in the [sources](sources) folder. Don't forget to replace all occurences of the placeholder `###` with your group ID.

> ℹ **Please note**:  
> The solution comprises the implementation of all four actions, i.e. `deductDiscount`, `copyTravel`, `acceptTravel`, and `rejectTravel`.           

- ![document](images/doc.png) [CDS BDEF ZRAP100_R_TRAVELTP_###](sources/EX6_BDEF_ZRAP100_R_TRAVELTP.txt)
- ![document](images/doc.png) [Class ZRAP100_BP_TRAVELTP_###](sources/EX6_CLASS_ZRAP100_BP_TRAVELTP.txt)
- ![document](images/doc.png) [CDS BDEF ZRAP100_C_TRAVELTP_###](sources/EX6_BDEF_ZRAP100_C_TRAVELTP.txt)
- ![document](images/doc.png) [CDS MDE ZRAP100_C_TRAVELTP_###](sources/EX6_DDLX_ZRAP100_C_TRAVELTP.txt)
-->
