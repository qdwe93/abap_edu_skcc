[홈 - RAP100](../../#exercises)

# 연습문제 3: BO 기능 개선 – Early Numbering

## 소개

이전 Exercise에서는 Business Object (BO) entity _Travel_ 의 데이터 모델을 향상시켰습니다 (참고: [Exercise 2](../ex02/README.md)).

이번 Exercise에서는 _unmanaged internal early numbering_ 을 정의하고 구현하여, 애플리케이션에서 새로운 _Travel_ instance 생성 시 primary key인 `TravelID`를 설정하게 됩니다. 또한 static field control을 사용하여 일부 필드를 read-only로 지정할 것입니다.
고유한 travel 식별자를 결정하기 위해 number range object가 사용됩니다.

- [3.1 - Internal Early Numbering 정의](#exercise-31-define-the-internal-early-numbering)
- [3.2 - Internal Early Numbering 구현](#exercise-32-implement-the-internal-early-numbering)
- [3.3 - 향상된 Travel 앱 미리보기 및 테스트](#exercise-33-preview-and-test-the-enhanced-travel-app)
- [요약](#summary)

> **알림**: 아래 Exercise 단계에서 접미사 플레이스홀더 **`###`** 를 본인이 선택했거나 할당받은 그룹 ID로 반드시 교체하는 것을 잊지 마십시오.

### 보충 설명: Numbering

> Numbering은 런타임 중에 entity instance의 primary key 필드 값을 설정하는 것에 관한 것입니다. RAP에서는 다양한 유형의 numbering을 지원하며, 이는 두 가지 주요 카테고리로 나눌 수 있습니다:
> - **Early numbering**: Early numbering 시나리오에서는 `CREATE`에 대한 modify 요청이 실행된 직후에 primary key 값이 설정됩니다. key 값은 consumer에 의해 외부에서 전달될 수도 있고, 프레임워크나 `FOR NUMBERING` 메소드의 구현에 의해 내부적으로 설정될 수도 있습니다. 후자를 이번 Exercise에서 구현할 것입니다.
> - **Late numbering**: Late numbering 시나리오에서는 상호작용 단계에서 되돌릴 수 없는 시점(point of no return)이 지나고 `SAVE` 시퀀스가 트리거된 후, consumer 상호작용 없이 key 값이 항상 내부적으로 할당됩니다.
>
> **추가 정보**: [Numbering](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/d85aec25222145f0b0cbbe8b02db51f0.html)

## Exercise 3.1: Internal Early Numbering 정의
[^Top of page](#)

> _Travel_ entity의 behavior definition ![bdef icon](images/adt_bdef.png)에서 (unmanaged) internal early numbering을 정의합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  _Travel_ entity의 behavior definiton ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TravelTP_###`** 을 엽니다.

2.  아래에 제공된 구문을 `authorization master( global )` 구문 바로 뒤, 여는 중괄호 `{` 바로 앞에 스크린샷과 같이 명시합니다.

    ```ABAP
    early numbering
    ```

    이제 **`create;`** 구문에 대해 _`Early Numbering for CREATE ZRAP100_R_TRAVELTP_### is not implemented`_ 라는 경고 메시지가 표시됩니다.
    노란색 밑줄이 있는 구문 위로 마우스를 가져가 메시지를 확인하거나 **Problems** 뷰를 살펴볼 수 있습니다.

    지금은 이 경고를 무시해도 됩니다. 나중에 처리할 것입니다.

    <!-- ![Travel BO Behavior Definition](images/new7.png) -->

    <img src="images/new7.png" alt="BO Behavior Definition" width="60%">

    behavior definition에서 볼 수 있듯이, administrative fields인 `CreatedAt`, `CreatedBy`, `LocalLastChangedAt`, `LastChangedAt`, `LastChangedBy`는 서비스 생성 중에 read-only로 설정되었습니다. 이 값들은 기본 CDS 뷰 entity ![ddls icon](images/adt_ddls.png)`ZRAP100_R_Travel_###`에 명시된 element annotation 덕분에 ABAP 런타임에 의해 자동으로 설정됩니다.

    _Travel_ BO는 이 시나리오에서 early numbering을 사용합니다. 새로운 _travel_ instance를 생성할 때 primary key 필드인 `TravelID`가 채워지도록 보장하고, 이 instance들을 추가로 처리할 때는 read-only가 되도록 하기 위해, operation에 종속적인 필드 접근 제한인 `field (mandatory:create)`와 `field (read-only:update)`가 각각 사용됩니다.

3.  다음 구문을 삭제합니다:

    ```ABAP
    field ( mandatory : create )
    TravelID;
    ```

4.  **`TravelID`** 필드는 internal early numbering에 의해 런타임에 설정될 것이므로 read-only 필드로 지정합니다.

    > **정보**: **static field control** 은 특정 필드의 속성을 제한하는 데 사용됩니다.

    이를 위해, behavior definition에서 아래 제공된 코드 스니펫으로 교체합니다.

    <!-- ```ABAP
    field ( mandatory : create )
    TravelID;
    ```

    with the code snippet provided below in the behavior definition as shown on the screenshot.

    ```ABAP
    field ( readonly : update )
    TravelID;
    ```  !-->

    ```ABAP
    field ( readonly )
    TravelID;
    ```

    **ABAP Pretty Printer** 기능(**Shift+F1**)을 사용하여 소스 코드의 서식을 맞출 수 있습니다.

    <!--  ![Travel BO Behavior Definition](images/field.png)   !-->

    <img src="images/readonly.png" alt="Travel BO Behavior Definition" width="60%">

    <!-- As you can seen in the behavior definition, the administrative fields `CreatedAt`, `CreatedBy`, `LocalLastChangedAt`, `LastChangedAt`, and `LastChangedBy` have been set to read-only during the service generation. Their values are automatically set by the ABAP runtime thanks to element annotations specified in the base CDS view entity ![ddls icon](images/adt_ddls.png)`ZRAP100_R_Travel_###`.  !-->

5.  변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

6.  정의를 완료하려면, behavior implementation class에 필요한 메소드를 선언해야 합니다. 이를 위해 ADT Quick Fix를 사용할 수 있습니다.

    **`create;`** 구문에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 엽니다.

    대화 상자에서 **`Add earlynumbering method for create of entity zrap100_i_travel_### in local handler ...`** 항목을 선택하여 `FOR NUMBERING` 메소드 **`earlynumbering_create`** 를 behavior pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 local handler class **`lcl_handler`** 에 추가합니다.

    <!-- ![Travel BO Behavior Definition](images/create.png) -->
    <img src="images/create.png" alt="Travel BO Behavior Definition" width="60%">

    behavior implementation class ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVEL_###`** 는 적절하게 향상될 것입니다.

    이제 early numbering의 정의를 마쳤으며, 그 로직을 구현하는 단계로 넘어갈 수 있습니다.

7.  변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>

## Exercise 3.2: Internal Early Numbering 구현
[^Top of page](#)

> 이제 _Travel_ BO entity의 behavior implementation class (aka behavior pool) ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 에서 unmanaged internal early numbering 로직을 구현합니다. 새로운 _Travel_ BO entity instance의 ID를 결정하기 위해 number range object가 사용됩니다.

> **참고**:
> 시간 제약 (및 단순화 이유)으로 인해, number range를 사용할 때의 적절한 오류 처리는 이 구현 예제에 포함되지 않습니다.
> 그럼에도 불구하고, number range object를 사용하는 managed BO의 더 포괄적인 구현 예제는 `/DMO/FLIGHT_MANAGED` 패키지에 있는 behavior implementation class `/DMO/BP_TRAVEL_M`에서 찾을 수 있습니다. 이 구현에 대한 설명은 SAP Help Portal의 RAP 개발 가이드 [Developing Managed Transactional Apps](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/b5bba99612cf4637a8b72a3fc82c22d9.html)에 제공되어 있습니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  local handler class `lcl_handler`의 선언부에서 **`earlynumbering_create`** 메소드의 인터페이스를 확인합니다.

    이를 위해, 메소드 이름에 커서를 놓고 **F2** 를 눌러 **ABAP Element Info** 뷰를 열고, importing 및 changing 파라미터와 같은 전체 메소드 인터페이스를 검토합니다. 다양한 (파생) 타입으로 이동할 수 있습니다.

    ![Travel BO Behavior Pool](images/new10.png)

    > managed BO를 위한 `FOR NUMBERING` 메소드의 시그니처:
    > - `IMPORTING` 파라미터 **`entities`** - key를 할당해야 하는 모든 entity를 포함합니다.
    > - 암시적 `CHANGING` 파라미터 (리턴 파라미터):
    >   - **`mapped`** - consumer에게 ID 매핑 정보를 제공하는 데 사용됩니다.
    >   - **`failed`** - 오류가 발생한 데이터 셋을 식별하는 데 사용됩니다.
    >   - **`reported`** - 실패 시 메시지를 반환하는 데 사용됩니다.
    >
    > **추가 정보**: [Implicit Response Parameters](https://help.sap.com/viewer/fc4c71aa50014fd1b43721701471913d/202110.000/en-US/aceaf8453d4b4e628aa29aa7dfd7d948.html)

2.  이제 implementation class의 구현부에서 **`earlynumbering_create`** 메소드를 구현합니다.

    먼저, import된 _Travel_ entity instance에 아직 ID가 설정되지 않았는지 확인해야 합니다. 이는 특히 BO가 draft-enabled일 때 반드시 확인해야 합니다.

    이를 위해, key가 할당되어야 하는 모든 _Travel_ entity를 포함하는 imported 파라미터 **`entities`** 에서 **`TravelID`**가 initial이 아닌 모든 instance를 제거합니다. 아래 제공된 코드 스니펫을 메소드 구현에 삽입하고, 플레이스홀더 `###`의 모든 발생을 자신의 그룹 ID로 교체하십시오.

    ```ABAP
     DATA:
       entity           TYPE STRUCTURE FOR CREATE ZRAP100_R_TravelTP_###,
       travel_id_max    TYPE /dmo/travel_id,
       " ABAP 런타임 에러 'BEHAVIOR_ILLEGAL_STATEMENT'가 발생하면 abap_false로 변경하세요
       use_number_range TYPE abap_bool VALUE abap_true.
 
     "Travel ID가 아직 설정되지 않았는지 확인 (멱등성)- BO가 draft-enabled일 때 반드시 확인해야 함
     LOOP AT entities INTO entity WHERE TravelID IS NOT INITIAL.
       APPEND CORRESPONDING #( entity ) TO mapped-travel.
     ENDLOOP.
 
     DATA(entities_wo_travelid) = entities.
     "기존 Travel ID가 있는 엔트리 제거
     DELETE entities_wo_travelid WHERE TravelID IS NOT INITIAL.
    ```

    ![Travel BO Behavior Pool](images/new11.png)

3.  Number Range API를 사용하여 **`entities_wo_travelid`** 의 엔트리를 기반으로 사용 가능한 번호 집합을 검색하고, 첫 번째 사용 가능한 travel ID를 결정합니다.

    아래 제공된 예제 구현에서는 _ABAP Flight Reference Scenario_ (패키지 `/DMO/FLIGHT_REUSE`에 위치)의 number range object **`/DMO/TRV_M`** 이 사용됩니다.

    > **참고**: 모든 참가자가 동일한 number range object **`/DMO/TRV_M`** 을 사용하므로, 할당된 Travel ID는 연속적이지 않을 수 있습니다.

    이를 위해, 아래 스크린샷과 같이 제공된 코드 스니펫으로 메소드 구현을 향상시킵니다. 이미 언급했듯이, 여기서 오류 처리는 최소한으로 유지됩니다.

    ```ABAP
      IF use_number_range = abap_true.
       "번호 가져오기
       TRY.
           cl_numberrange_runtime=>number_get(
             EXPORTING
               nr_range_nr       = '01'
               object            = '/DMO/TRV_M'
               quantity          = CONV #( lines( entities_wo_travelid ) )
             IMPORTING
               number            = DATA(number_range_key)
               returncode        = DATA(number_range_return_code)
               returned_quantity = DATA(number_range_returned_quantity)
           ).
         CATCH cx_number_ranges INTO DATA(lx_number_ranges).
           LOOP AT entities_wo_travelid INTO entity.
             APPEND VALUE #(  %cid      = entity-%cid
                              %key      = entity-%key
                              %is_draft = entity-%is_draft
                              %msg      = lx_number_ranges
                           ) TO reported-travel.
             APPEND VALUE #(  %cid      = entity-%cid
                              %key      = entity-%key
                              %is_draft = entity-%is_draft
                           ) TO failed-travel.
           ENDLOOP.
           EXIT.
       ENDTRY.
 
       "number range에서 첫 번째 비어있는 travel ID 결정
       travel_id_max = number_range_key - number_range_returned_quantity.
     ELSE.
       "number range 없이 첫 번째 비어있는 travel ID 결정
       "active 테이블에서 최대 travel ID 가져오기
       SELECT SINGLE FROM zrap100_atrav### FIELDS MAX( travel_id ) AS travelID INTO @travel_id_max.
       "draft 테이블에서 최대 travel ID 가져오기
       SELECT SINGLE FROM zrap100_dtrav### FIELDS MAX( travelid ) INTO @DATA(max_travelid_draft).
       IF max_travelid_draft > travel_id_max.
         travel_id_max = max_travelid_draft.
       ENDIF.
     ENDIF.
    ```

    ![Travel BO Behavior Pool](images/new12.png)

> ⚠ 다음과 같은 오류 메시지가 발생하면:
> **ABAP Runtime error 'BEHAVIOR_ILLEGAL_STATEMENT'**
> `use_number_range` 변수의 값을 `abap_false`로 변경하십시오.
> `use_number_range TYPE abap_bool VALUE abap_true.`

4.  식별자가 없는 새로운 _Travel_ instance에 대한 Travel ID를 설정합니다.

    아래 스크린샷과 같이 다음 코드 스니펫으로 메소드 구현을 향상시킵니다.

    ```ABAP
     "ID가 없는 새 instance에 Travel ID 설정
     LOOP AT entities_wo_travelid INTO entity.
       travel_id_max += 1.
       entity-TravelID = travel_id_max.
 
       APPEND VALUE #( %cid      = entity-%cid
                       %key      = entity-%key
                       %is_draft = entity-%is_draft
                     ) TO mapped-travel.
     ENDLOOP.
    ```

    정기적으로 **ABAP Pretty Printer** 기능(**Shift+F1**)을 사용하여 소스 코드의 서식을 맞추는 것을 잊지 마십시오.

    ![Travel BO Behavior Pool](images/new13.png)

5.  변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))합니다.

</details>

## Exercise 3.3: 향상된 Travel 앱 미리보기 및 테스트
[^Top of page](#)

> 이제 Travel 앱에서 새로운 travel instance를 생성하여 변경 사항을 미리 보고 테스트할 수 있습니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  브라우저가 아직 열려 있다면 **F5** 키를 사용하여 애플리케이션을 새로 고침하거나,
    service binding ![srvb icon](images/adt_srvb.png)**`ZRAP100_UI_TRAVEL_O4_###`** 로 이동하여 **`Travel`** entity set에 대한 Fiori elements App preview를 시작하십시오.

2.  새로운 _Travel_ instance를 생성합니다.

    ![Travel App Preview](images/preview2.png)

    이제 Travel ID를 수동으로 입력하는 대화 상자가 표시되지 않아야 합니다. Travel ID는 방금 구현한 로직에 의해 자동으로 할당될 것입니다.

    ![Travel App Preview](images/preview3.png)

</details>

## 요약
[^Top of page](#)

이제 여러분은...
- early numbering과 static feature control을 정의하고,
- early numbering을 구현했으며,
- 향상된 Fiori elements 앱을 미리 보고 테스트했습니다.

다음 Exercise로 계속 진행할 수 있습니다 – **[연습문제 4: BO 기능 개선 – Determinations](../ex04/README.md)**

---

<!--
## Appendix
[^Top of page](#)

Find the source code for the behavior definition and behavior implementation class (aka behavior pool) of the _Travel_ entity in the [sources](sources) folder. Don't forget to replace all occurences of the placeholder `###` with your group ID.

- ![document](images/doc.png) [CDS BDEF ZRAP100_R_TRAVELTP_###](sources/EX3_BDEF_ZRAP100_R_TRAVELTP.txt)
- ![document](images/doc.png) [Class ZRAP100_BP_TRAVELTP_###](sources/EX3_CLASS_ZRAP100_BP_TRAVELTP.txt)
-->
