[Home - RAP100](../../#exercises)

# [선택 사항] 연습문제 8: RAP BO를 위한 ABAP Unit Test 작성

## 소개
이전 연습에서는 _Travel_ 엔티티의 일부 표준 및 비표준 작업에 대한 동적 인스턴스 기능 제어를 구현했습니다. (참고: [연습 7](../ex07/README.md)).

이번 연습에서는 비즈니스 오브젝트의 트랜잭션 동작을 전체적으로 검증하기 위해 시나리오 테스트를 작성합니다. Entity Manipulation Language (EML)를 사용하여 ABAP Unit 테스트를 작성하게 됩니다. 본 연습에서 테스트할 시나리오는 새로운 _travel_ 인스턴스의 생성 및 조작입니다.

- [8.1 - ABAP Unit Test 클래스 생성](#81---abap-unit-test-클래스-생성)
- [8.2 - 테스트 클래스 정의 강화](#82---테스트-클래스-정의-강화)
- [8.3 - 특별 메소드 구현](#83---특별-메소드-구현)
- [8.4 - `create_with_action` 테스트 메소드 구현](#84---create_with_action-테스트-메소드-구현)
- [8.5 - ABAP Unit Test 실행](#85---abap-unit-test-실행)
- [요약](#요약)

> **알림**: 아래 연습 단계에서 접미사 플레이스홀더 **`###`** 를 선택하거나 할당받은 **그룹 ID** 로 반드시 교체하십시오.

### ABAP Unit Testing에 대하여

애플리케이션의 높은 품질을 보장하는 것은 전체 소프트웨어 개발 생명주기에서 매우 높은 우선순위를 가집니다. 애플리케이션 개발자로서, 예를 들어, 단위, 시나리오 및 통합 테스트를 작성하여 애플리케이션 동작을 전체적으로 검증할 수 있기를 원합니다. ABAP 플랫폼은 이를 달성하기 위해 다양한 메커니즘과 프레임워크를 제공합니다. 여기서 주요 옵션은 ABAP Unit Tests와 ABAP Test Cockpit입니다.

ABAP Unit 테스트를 작성하는 것은 시간이 지나도 회귀(regression)를 유발하지 않고 쉽게 발전할 수 있는 고품질 소프트웨어를 제공하는 방법입니다. 익스트림 프로그래밍 및 테스트 주도 개발과 같은 방법론에서는 단위 테스트의 역할이 훨씬 더 중요합니다.

ABAP Unit은 ABAP을 위한 최신 단위 테스트 프레임워크입니다. 이는 ABAP 프로그래밍 언어에 내장되어 단위 테스트 작성을 지원합니다. ADT에서는 단위 테스트를 실행하고 기능적 정확성 및 코드 커버리지에 대한 결과를 평가할 수 있는 다양한 기능이 있습니다.

**추가 자료**: [Test@RAP Development Guide](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/1fa88de357464d98a08165cb5830c0ad.html) | [Testing the RAP Business Object](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/600245bbe0204b34b4cd7626339fd56b.html) | [Ensuring Quality of ABAP Code](https://help.sap.com/viewer/5371047f1273405bb46725a417f95433/Cloud/en-US/4ec7641e6e391014adc9fffe4e204223.html)

### ABAP Unit Test 클래스의 단순화된 런타임 흐름

<details>
  <summary>클릭하여 펼치기!</summary>

ABAP Unit 프레임워크에는 주어진 테스트 구성을 설정하고 해제하기 위해 구현해야 하는 표준 특별 메소드들이 있습니다.
- 테스트 구성의 표준 특별 정적 메소드 **`class_setup()`** 및 **`class_teardown()`**.
  - **`class_setup()`**: 이 setup 메소드는 클래스의 모든 테스트 전에 한 번 실행됩니다. 테스트 환경을 설정하는 데 사용됩니다.
  - **`class_teardown()`**: 이 teardown 메소드는 테스트 클래스의 모든 테스트가 실행된 후 한 번 실행됩니다. 테스트 환경을 파괴하는 데 사용됩니다.
- 테스트 구성의 표준 특별 인스턴스 메소드: **`setup()`** 및 **`teardown()`**.
  - **`setup()`**: 이 메소드는 각 개별 테스트 전 또는 각 테스트 메소드의 실행 전에 실행됩니다. 예를 들어, 각 테스트 메소드 실행 전에 테스트 더블(test doubles)을 재설정하는 데 사용됩니다.
  - **`teardown()`**: 이 메소드는 각 개별 테스트 후 또는 각 테스트 메소드의 실행 후에 실행됩니다. 관련된 엔티티의 변경 사항을 롤백하는 데 사용됩니다.

아래는 ABAP Unit 테스트 클래스의 런타임 흐름을 단순화하여 표현한 것입니다.

ABAP Unit 테스트 메소드: **`<test method>()`** 는 테스트 대상 코드(CUT)에 대한 각 단위 테스트 메소드를 나타냅니다. 이러한 메소드는 메소드 인터페이스에 **`FOR TESTING`** 추가 구문으로 식별할 수 있습니다.

 ![BO Test – Adjust Test Class](images/testclassruntimeflow01.png)

</details>

## 연습 8.1: ABAP Unit Test 클래스 생성
[^맨 위로](#)

> 이전에 연습한 패키지에 테스트 클래스 ![class icon](images/adt_class.png)**`ZRAP100_TC_TRAVEL_EML_###`** 를 생성합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  패키지 ![package icon](images/adt_package.png)**`ZRAP100_###`** 를 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **New > ABAP Class** 를 선택합니다.

2.  필요한 정보를 유지하고(`###`는 그룹 ID) **Next >** 를 클릭합니다.
    *   Name: **`ZRAP100_TC_TRAVEL_EML_###`**
    *   Description: _**`BO Test with EML`**_

3.  전송 요청(transport request)을 할당하고 **Finish** 를 선택하여 새 ABAP 클래스를 생성합니다.

    ![Test Class](images/testclass01.png)

4.  새로운 글로벌 ABAP 클래스를 ABAP Unit 테스트 클래스로 지정하고, _Travel_ BO 엔티티 **`ZRAP100_R_TravelTP_###`** 의 behavior definition (**`BDEF`**)과의 테스트 관계도 지정합니다.

    이를 위해 클래스 정의의 **`CREATE PUBLIC`** 추가 구문 뒤(바로 **`.`** 앞)에 **`FOR TESTING RISK LEVEL HARMLESS DURATION SHORT`** 를 삽입하여 클래스를 ABAP Unit 테스팅용으로 활성화합니다.

    ```ABAP
    FOR TESTING
    RISK LEVEL HARMLESS
    DURATION SHORT
    ```

    그런 다음, 아래 제공된 코드 스니펫을 클래스 에디터 상단에 ABAP Doc 주석으로 추가하여 behavior definition **`ZRAP100_R_TravelTP_###`**(TADIR 엔트리: `R3TR BDEF`)와의 테스트 관계를 지정합니다. 플레이스홀더 `###`를 그룹 ID로 교체하십시오.

    > **정보**: ABAP Unit 테스팅에서 테스트 관계는 주어진 객체의 테스트를 참조된 객체에서 실행할 수 있게 합니다. 이 예에서는 별도의 테스트 클래스에서 RAP BO에 대한 테스트를 실행하게 됩니다. behavior 구현 클래스에서 직접 ABAP Unit 테스트를 작성하는 것도 가능합니다.

    ```ABAP
    "! @testing BDEF:ZRAP100_R_TravelTP_###
    ```

    소스 코드는 다음과 같아야 합니다:

    ![Test Class](images/ex8_1.png)

5.  변경 사항을 저장 ![save icon](images/adt_save.png)합니다.

</details>

## 연습 8.2: 테스트 클래스 정의 강화
[^맨 위로](#)

> 필요한 모든 메소드로 ABAP Unit 테스트 클래스 정의를 강화합니다.
> 필요한 특별 ABAP unit 인스턴스 및 정적 메소드, 그리고 테스트 대상 코드(_CUT_)를 위한 테스트 메소드를 선언할 것입니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  아래 제공된 코드 스니펫을 클래스 정의의 **`PRIVATE SECTION.`** 문 아래에 삽입합니다.

    현재 테스트 시나리오에서는 _Agency_ 및 _Customer_ 엔티티에 대한 모의(Mock) 데이터가 필요합니다.

    ```ABAP
        CLASS-DATA:
          cds_test_environment TYPE REF TO if_cds_test_environment,
          sql_test_environment TYPE REF TO if_osql_test_environment,
          begin_date           TYPE /dmo/begin_date,
          end_date             TYPE /dmo/end_date,
          agency_mock_data     TYPE STANDARD TABLE OF /dmo/agency,
          customer_mock_data   TYPE STANDARD TABLE OF /dmo/customer.

        CLASS-METHODS:
          class_setup,    " setup test double framework
          class_teardown. " stop test doubles
       METHODS:
          setup,          " reset test doubles
          teardown.       " rollback any changes

       METHODS:
          " CUT: create with action call and commit
          create_with_action FOR TESTING RAISING cx_static_check.
    ```

    클래스 구현부에 메소드 본문이 없기 때문에 ABAP 에디터에 오류가 표시됩니다.

2.  ADT 빠른 수정(quick fix)을 통해 메소드 본문을 추가합니다.

    이를 위해 커서를 메소드 이름 **`class_setup`** 에 놓고, **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 표시한 다음, 팝업 메뉴에서 _**`+ Add 5 unimplemented methods`**_ 항목을 선택하여 빈 메소드 본문을 클래스 구현 섹션에 추가합니다.

    이제 소스 코드는 다음과 같아야 합니다:

    ![Test Class](images/testclass03.png)

    <details>
      <summary>ℹ️ 간략한 설명 - 클릭하여 펼치기!</summary>

      - 테스트 더블(test doubles) 및 모의(mock) 데이터를 위한 다양한 정적 속성
      - **`cds_test_environment`**: CDS TDF(**`if_cds_test_environment`**)를 위한 참조 객체로, 기본 BO 뷰의 _travel_ CDS 엔티티에 대한 테스트 더블을 제공하는 데 사용됩니다. CDS 테스트 더블은 _read_ 작업에 사용됩니다.
      - **`sql_test_environment`**: ABAP SQL TDF(**`if_osql_test_environment`**)를 위한 참조 객체로, 추가적으로 필요한 데이터베이스 테이블을 스터빙(stubing)하는 데 사용됩니다. 데이터베이스 테스트 더블은 _write_ 작업에 사용됩니다.
      - 테스트 구성의 ABAP unit 프레임워크 표준 특별 메소드가 지정됩니다: **`setup`**, **`teardown`**, **`class_setup`**, **`class_teardown`**.
      - 메소드 **`create_with_action()`** 는 우리의 CUT를 위한 단위 테스트 메소드입니다. 테스트 메소드는 메소드 시그니처에 **`FOR TESTING`** 추가 구문으로 쉽게 식별할 수 있습니다.
    </details>

2.  변경 사항을 저장 ![save icon](images/adt_save.png)합니다.

 </details>

## 연습 8.3: 특별 메소드 구현
[^맨 위로](#)

> ABAP unit 프레임워크에서 요구하는 특별 정적 메소드 **`class_setup`** 과 **`class_teardown`**, 그리고 특별 인스턴스 메소드 **`setup`** 을 구현합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  정적 메소드 **`class_setup`** 을 구현합니다. 이 메소드는 테스트 더블 환경을 설정하고 테스트 데이터를 준비하는 데 사용됩니다.

    메소드 본문을 아래 코드 스니펫으로 교체하고 모든 **`###`** 플레이스홀더를 그룹 ID로 교체하십시오.

    **Pretty Printer**(**Shift+F1**)를 사용하여 소스 코드의 서식을 지정하고 변경 사항을 저장 ![save icon](images/adt_save.png)합니다.

    ```ABAP
     METHOD class_setup.
       " create the test doubles for the underlying CDS entities
       cds_test_environment = cl_cds_test_environment=>create_for_multiple_cds(
                         i_for_entities = VALUE #(
                           ( i_for_entity = 'ZRAP100_R_TravelTP_###' ) ) ).

       " create test doubles for additional used tables.
       sql_test_environment = cl_osql_test_environment=>create(
       i_dependency_list = VALUE #( ( '/DMO/AGENCY' )
                                    ( '/DMO/CUSTOMER' ) ) ).

       " prepare the test data
       begin_date = cl_abap_context_info=>get_system_date( ) + 10.
       end_date   = cl_abap_context_info=>get_system_date( ) + 30.

       agency_mock_data   = VALUE #( ( agency_id = '070041' name = 'Agency 070041' ) ).
       customer_mock_data = VALUE #( ( customer_id = '000093' last_name = 'Customer 000093' ) ).
     ENDMETHOD.
    ```

    소스 코드는 다음과 같아야 합니다:

    ![Test Class](images/ex8_2.png)

2.  변경 사항을 저장 ![save icon](images/adt_save.png)합니다.

3.  ABAP Unit 프레임워크 표준 정적 메소드 **`class_teardown`** 을 구현합니다. 이 메소드는 테스트 클래스 실행이 끝날 때 테스트 더블을 중지하는 데 사용됩니다.

    메소드 본문을 아래 제공된 코드 스니펫으로 교체하고, 소스 코드의 서식을 지정한 후 변경 사항을 저장 ![save icon](images/adt_save.png)합니다.

    ```ABAP
     METHOD class_teardown.
       " remove test doubles
       cds_test_environment->destroy(  ).
       sql_test_environment->destroy(  ).
     ENDMETHOD.
    ```

    소스 코드는 다음과 같아야 합니다:

    ![Test Class](images/testclass05.png)


4.	특별 인스턴스 메소드 **`setup`** 을 구현합니다. 이 메소드는 테스트 메소드 실행 전 - 또는 일반적으로 테스트 클래스의 각 테스트 메소드 실행 전에 테스트 더블을 재설정하고 테스트 데이터를 삽입하는 데 사용됩니다.

    아래 코드 스니펫을 스크린샷에 보이는 것처럼 적절한 메소드 구현부에 삽입하십시오.

    ```ABAP
        METHOD setup.
          " clear the test doubles per test
          cds_test_environment->clear_doubles(  ).
          sql_test_environment->clear_doubles(  ).
          " insert test data into test doubles
          sql_test_environment->insert_test_data( agency_mock_data   ).
          sql_test_environment->insert_test_data( customer_mock_data ).
        ENDMETHOD.
    ```

    소스 코드는 다음과 같아야 합니다:

    ![Test Class](images/testclass06.png)


5. 특별 인스턴스 메소드 **`teardown`** 을 구현합니다. 이 메소드는 테스트 메소드 실행 후 관련된 엔티티의 모든 변경 사항을 롤백하는 데 사용됩니다.

    메소드 본문을 아래 제공된 코드 스니펫으로 교체하고 소스 코드의 서식을 지정하십시오.

    ```ABAP
      METHOD teardown.
        " clean up any involved entity
        ROLLBACK ENTITIES.
      ENDMETHOD.
    ```

    소스 코드는 다음과 같아야 합니다.

    ![Test Class](images/testclass07.png)


6. 변경 사항을 저장 ![save icon](images/adt_save.png)합니다.

 </details>

 ## 연습 8.4: `create_with_action` 테스트 메소드 구현
[^맨 위로](#)

> BO 테스트를 구현합니다.
> 현재 테스트 대상 코드(CUT)는 _Travel_ 인스턴스를 생성하고 그 위에서 `acceptTravel` 액션을 실행하는 EML 구문입니다. 이 시나리오에는 `CREATE`, `EXECUTE`, `COMMIT` EML 구문이 포함됩니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  테스트 메소드 **`create_with_action`** 을 구현합니다.

    아래 제공된 코드 스니펫을 메소드 본문에 삽입하고 모든 **`###`** 를 그룹 ID로 교체하십시오.

    ⚠**주의**⚠:
    만약 [연습 6](../ex6/readme.md)에서 인스턴스 액션 **`acceptTravel`** 을 정의하고 구현했다면, 삽입된 소스 코드에서 관련된 여섯 (6) 줄의 코드 주석을 해제하십시오.

    ```ABAP
       METHOD create_with_action.
         " create a complete composition: Travel (root)
         MODIFY ENTITIES OF ZRAP100_R_TravelTP_###
          ENTITY Travel
          CREATE FIELDS ( AgencyID CustomerID BeginDate EndDate Description TotalPrice BookingFee CurrencyCode )
            WITH VALUE #( (  %cid = 'ROOT1'
                             AgencyID      = agency_mock_data[ 1 ]-agency_id
                             CustomerID    = customer_mock_data[ 1 ]-customer_id
                             BeginDate     = begin_date
                             EndDate       = end_date
                             Description   = 'TestTravel 1'
                             TotalPrice    = '1100'
                             BookingFee    = '20'
                             CurrencyCode  = 'EUR'
                          ) )

    *        " execute action `acceptTravel`
    *        ENTITY Travel
    *          EXECUTE acceptTravel
    *            FROM VALUE #( ( %cid_ref = 'ROOT1' ) )

         " execute action `deductDiscount`
          ENTITY Travel
            EXECUTE deductDiscount
              FROM VALUE #( ( %cid_ref = 'ROOT1'
                              %param-discount_percent = '20' ) )   "=> 20%

          " result parameters
          MAPPED   DATA(mapped)
          FAILED   DATA(failed)
          REPORTED DATA(reported).

         " expect no failures and messages
         cl_abap_unit_assert=>assert_initial( msg = 'failed'   act = failed ).
         cl_abap_unit_assert=>assert_initial( msg = 'reported' act = reported ).

         " expect a newly created record in mapped tables
         cl_abap_unit_assert=>assert_not_initial( msg = 'mapped-travel'  act = mapped-travel ).

         " persist changes into the database (using the test doubles)
         COMMIT ENTITIES RESPONSES
           FAILED   DATA(commit_failed)
           REPORTED DATA(commit_reported).

         " no failures expected
         cl_abap_unit_assert=>assert_initial( msg = 'commit_failed'   act = commit_failed ).
         cl_abap_unit_assert=>assert_initial( msg = 'commit_reported' act = commit_reported ).

         " read the data from the persisted travel entity (using the test doubles)
         SELECT * FROM ZRAP100_R_TravelTP_### INTO TABLE @DATA(lt_travel). "#EC CI_NOWHERE
         " assert the existence of the persisted travel entity
         cl_abap_unit_assert=>assert_not_initial( msg = 'travel from db' act = lt_travel ).
         " assert the generation of a travel ID (key) at creation
         cl_abap_unit_assert=>assert_not_initial( msg = 'travel-id' act = lt_travel[ 1 ]-TravelID ).
    *     " assert that the action has changed the overall status
    *     cl_abap_unit_assert=>assert_equals( msg = 'overall status' exp = 'A' act = lt_travel[ 1 ]-OverallStatus ).
         " assert the discounted booking_fee
         cl_abap_unit_assert=>assert_equals( msg = 'discounted booking_fee' exp = '16' act = lt_travel[ 1 ]-BookingFee ).

       ENDMETHOD.
    ```

    소스 코드는 다음과 같아야 합니다:

    ![Test Class](images/ex8_3.png)

    <details>
      <summary>ℹ️ 간략한 설명 - 클릭하여 펼치기!</summary>

    - 전체 CUT는 **`MODIFY ENTITIES`** 구문에 **`CREATE`** 와 **`EXECUTE`** 추가 구문, 그리고 **`COMMIT ENTITIES`** 구문을 포함하는 복잡한 EML 구문입니다.
    - 클래스 **`CL_ABAP_UNIT_ASSERT`** 는 테스트 메소드 구현에서 테스트 가정을 확인/단언(check/assert)하는 데 사용됩니다. 이 클래스는 `assert_equals()`, `assert_initial()`, `assert_not_initial()`, `assert_differs()`와 같은 다양한 정적 메소드를 제공합니다.
    - 블록 (1)
      - CUT: **`MODIFY ENTITIES`** 구문, 다음을 포함:
        - _travel_ 인스턴스(루트)를 생성하는 **`CREATE`** 추가 구문.
        - _travel_ 인스턴스에서 액션 **`acceptTravel`** 을 호출하는 **`EXECUTE`** 추가 구문.
        - _travel_ 인스턴스에서 액션 **`deductDiscount`** 를 호출하는 **`EXECUTE`** 추가 구문.
      - 변경 사항을 커밋하기 전, 결과 파라미터에 대한 첫 번째 단언 블록
        - 인스턴스 생성 및 액션 실행 중 발생할 수 있는 실패와 메시지를 확인하는 데 사용되는 단언.
        - 생성된 _travel_ 및 _booking_ 인스턴스에 대해 반환된 **`mapped`** 테이블을 확인하는 데 사용되는 단언.
    - 블록 (2)
        - CUT: **`COMMIT ENTITIES`** 구문, 변경 사항을 데이터베이스(테스트 더블)에 영속화합니다.
        - 두 번째 단언 블록은 반환 파라미터 **`commit_failed`** 와 **`commit_reported`** 를 검사하여 수행된 커밋의 실패 여부를 확인합니다.
    - 블록 (3)
        - 세 번째 단언 블록은 데이터가 테스트 더블에 성공적으로 영속화되었는지 확인합니다.
        - 이를 위해, 먼저 기본 BO 뷰 **`ZRAP100_R_TravelTP_####`** 에 대한 **`SELECT`** 구문을 통해 커밋된 데이터를 읽습니다; 데이터는 구성된 테스트 더블에서 읽어옵니다.
        - 다양한 단언 확인이 수행됩니다. 자세한 설명은 코드 스니펫의 주석 라인을 참조하십시오.

    </details>


8.	변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

BO 테스트 작성을 마쳤습니다. 이제 실행해 보십시오.
 </details>


## 연습 8.5: ABAP Unit Test 실행
[^맨 위로](#)

> 구현한 ABAP Unit 테스트를 실행합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.  구현된 단위 테스트를 실행합니다.

    이를 위해, **Outline** 또는 **Project Explorer** 뷰에서 테스트 메소드 이름 **`create_with_action`** 또는 테스트 클래스 이름을 마우스 오른쪽 버튼으로 클릭하고, 컨텍스트 메뉴에서 **Run as > ABAP Unit Test** 를 선택하거나 간단히 **Ctrl+Shift+F10** 을 누릅니다.

    결과는 다음과 같아야 합니다:

    ![Test Class](images/testclass09.png)

2.  이제, 관련된 behavior definition **`ZRAP100_R_TravelTP_###`** 에서 단위 테스트를 실행합니다.

    이 문서의 [8.1 - ABAP Unit Test 클래스 생성](#81---abap-unit-test-클래스-생성)에서, 특별 ABAP Doc 주석 **`"! @testing BDEF:ZRAP100_R_TravelTP_###`** 을 사용하여 behavior definition **`ZRAP100_R_TravelTP_###`** 에 대한 테스트 관계를 지정했습니다. 여기서 **`###`** 는 그룹 ID입니다.

    이는 이 테스트 클래스에 구현된 모든 ABAP Unit 테스트가 behavior definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TravelTP_####`** 에서 실행될 수 있음을 의미합니다.

    **Project Explorer** 에서 BO behavior definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TravelTP_###`** 를 선택하고, 마우스 오른쪽 버튼으로 클릭한 후 컨텍스트 메뉴에서 **Run as > ABAP Unit Test**(**Ctrl+Shift+F10**)를 선택하여 관련된 모든 단위 테스트를 실행합니다; 현재는 하나뿐입니다 :-).

    이 behavior definition과 관련된 모든 단위 테스트가 실행되고 _**ABAP Unit**_ 뷰의 _**Foreign Tests**_ 아래에 표시됩니다.

    ![Test Class](images/ex8_6.png)

    이것으로 이번 연습은 끝입니다!

 </details>

## 요약
[^맨 위로](#)

이번 연습에서 당신은...
- EML을 사용하여 _Travel_ BO 엔티티에 대한 ABAP unit 테스트를 작성했습니다.
- 구현된 ABAP Unit 테스트를 실행했습니다.

> **추가 자료**: RAP로 빌드된 앱에 대한 ABAP Unit 테스트 작성에 대해 더 배우려면 여기를 참조하십시오: [RAP400](../../../../rap4xx/rap400)

다음 연습으로 계속할 수 있습니다 – **[탐색] [연습문제 9: EML을 이용한 RAP BO 외부 API 기반 접근](../ex09/README.md)**

---
<!--
## 부록
[^맨 위로](#)

글로벌 ABAP 테스트 클래스의 소스 코드는 [sources](sources) 폴더에서 찾을 수 있습니다. 모든 `###` 플레이스홀더를 그룹 ID로 교체하는 것을 잊지 마십시오.
-->
> ℹ **정보**:
> 이 솔루션은 액션, 즉 `deductDiscount`와 `acceptTravel`에 대한 단위 테스트 구현을 포함합니다.

- ![document](images/doc.png) [CLASS ZRAP100_TC_TRAVEL_EML_###](sources/EX8_CLASS_ZRAP100_TC_TRAVEL_EML.txt)
