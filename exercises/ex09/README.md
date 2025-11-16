[Home - RAP100](../../#exercises)

# \[탐색\] 연습문제 9: Entity Manipulation Language (EML)를 이용한 RAP BO 외부 API 기반 접근

## 소개
지금까지 Entity Manipulation Language (EML)는 소위 `IN LOCAL MODE`에서 _Travel_ BO 엔티티의 트랜잭션 동작을 behavior pool에 구현하는 데 사용되었습니다.

이전 실습에서는 EML을 사용하여 _Travel_ BO에 대한 ABAP Unit 테스트를 작성했습니다. ([실습 8](../ex08/README.md) 참조).

이번 실습에서는 RAP 컨텍스트 외부에서 _Travel_ BO 엔티티의 인스턴스를 소비(즉, 읽기, 업데이트, 생성 및 삭제)하기 위해 EML을 사용하는 방법을 탐색합니다. 이를 위해 _Travel_ BO 엔티티에 대한 샘플 EML 기반 접근 구현을 포함하는 ABAP 클래스가 제공됩니다.

이 실습은 EML을 탐색하고 더 잘 이해하는 것에 관한 것입니다. 아래 지침을 따르고 원하는 대로 샘플 구현을 자유롭게 복사하고 수정하십시오.

- [9.1 - ABAP 클래스 생성](#exercise-91-create-the-abap-class)
- [9.2 - _Travel_ BO 엔티티 인스턴스 READ](#exercise-92-read-a-travel-bo-entity-instance)
- [9.3 - _Travel_ BO 엔티티 인스턴스 UPDATE](#exercise-93-update-a-travel-bo-entity-instance)
- [9.4 - Travel_ BO 엔티티 인스턴스 CREATE](#exercise-94-create-a-travel-bo-entity-instance)
- [9.5 - _Travel_ BO 엔티티 인스턴스 DELETE](#exercise-95-delete-a-travel-bo-entity-instance)
- [9.6 - Draft _Travel_ BO 엔티티 인스턴스 ACTIVATE](#exercise-96-activate-a-draft-travel-bo-entity-instance)
- [9.7 - Draft _Travel_ BO 엔티티 인스턴스 DISCARD](#exercise-97-discard-a-draft-travel-bo-entity-instance)
- [9.8 - EML 가지고 놀기](#exercise-98-play-around-with-eml)
- [요약](#summary)


> **알림**:
> 아래 실습 단계에서 접미사 자리 표시자 **`###`** 를 선택하거나 할당받은 그룹 ID로 바꾸는 것을 잊지 마십시오.
> ABAP 편집기에서 각 ABAP 및 EML 구문에 대한 자세한 정보를 얻으려면 클래식 **F1 도움말** 을 활용하십시오.


### Entity Manipulation Language (EML)에 대하여
> Entity Manipulation Language (EML)는 ABAP 언어의 확장으로, ABAP을 직접 사용하여 RAP 비즈니스 객체에 대한 타입-안전(type-safe), API 기반 접근을 제공합니다. EML은 SQL과 유사한 구문을 가지고 있습니다.
>
> EML은 RAP BO의 트랜잭션 동작을 구현하고 RAP 컨텍스트 외부에서 기존 RAP BO에 접근하는 데 사용됩니다.
> EML은 지정된 엔티티에 대한 작업을 트리거하여 비즈니스 객체와 상호 작용합니다. EML에 의해 작업이 트리거되려면 해당 작업이 behavior definition에서 관련 엔티티에 대해 지정되고 그에 따라 구현되어야 합니다.
>
> EML을 통해 RAP BO 인스턴스를 소비할 때, 소비자 애플리케이션은 `MODIFY` 구문 이후에 `COMMIT` 작업을 트리거하여 트랜잭션 버퍼에 임시 저장된 변경 사항을 SAP HANA 데이터베이스에 영구 저장할 책임이 있습니다.
>
> EML 참조 문서는 ABAP Keyword Documentation에서 제공됩니다. 클래식 **F1 도움말** 을 사용하여 ABAP 편집기에서 **F1** 키를 눌러 각 구문에 대한 자세한 정보를 얻을 수 있습니다.
>
> **추가 정보**: [EML@RAP Development Guide](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/af7782de6b9140e29a24eae607bf4138.html) | [EML@ABAP Keyword Documentation](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abeneml.htm) | [ABAP for RAP Business Objects](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenabap_for_rap_bos.htm) | [RAP BO Contract](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/3a402c5cf6a74bc1a1de080b2a7c6978.html)

## Exercise 9.1: ABAP 클래스 생성
[^Top of page](#)

> 이 단계에서는 EML을 가지고 놀기 위해 클래스 ![class icon](images/adt_class.png)**`ZRAP100_CL_EML_###`** 를 생성합니다. 여기서 **`###`** 는 당신의 그룹 ID입니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1.	패키지 ![package icon](images/adt_package.png)**`ZRAP100_###`** 를 마우스 오른쪽 버튼으로 클릭하고 _**New > ABAP Class**_ 를 선택합니다.

2.	필요한 정보(여기서 `###`는 당신의 그룹 ID)를 유지하고 **Next >** 를 선택하여 계속합니다:

     * Name: **`ZRAP100_CL_EML_###`**
     * Description: _**`EML Playground`**_
     * Interfaces: **`if_oo_adt_classrun`**
       이를 위해 _**Add**_ 를 누르고, 필터 영역에 인터페이스 이름을 입력하고, 목록에서 올바른 항목을 선택한 다음 **OK** 로 확인합니다.

    전송 요청을 할당하고 **Finish** 를 선택하여 클래스를 생성합니다.

    ABAP 클래스 스켈레톤이 생성되어 클래스 편집기에 표시됩니다.

    ![ABAP Class](images/emlclass01.png)

3.	생성된 전체 코드를 삭제하고 아래에 링크된 소스 코드 문서에 제공된 코드로 교체합니다.
   _Replace All_ 기능(**Ctrl+F**)을 사용하여 자리 표시자 **`###`** 의 모든 발생을 당신의 그룹 ID로 바꾸는 것을 잊지 마십시오.

   ![document](images/doc.png) **소스 코드 문서**: ![ddls icon](images/adt_class.png)[ABAP Class ZRAP100_CL_EML_###](sources/EX9_CLASS_ZRAP100_CL_EML.txt)

4.	클래스를 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

6.	클래스를 살펴보고 어떻게 작동하는지 이해합니다.

    **클래스 정의에 대한 간략한 설명**
    1. **(1)** 허용된 인스턴스 상태에 대한 상수 데이터
       - **`is_draft`**: draft 인스턴스용 (`if_abap_behv=>mk-on` = **`01`**)
       - **`is_active`**: active 인스턴스용 (`if_abap_behv=>mk-off` = **`00`**)

    3. **(2)** 클래스 내 어디서나 접근 가능한 정적 속성
       - **`travel_id`**: travel 인스턴스의 ID를 지정하는 데 사용됨
       - **`instance_state`**: 인스턴스 상태(draft 또는 active)를 지정하는 데 사용됨
       - **`console_output`**: _Console_ 뷰에 출력을 쓰는 데 사용되는 참조 객체

    4. **(3)** EML 기반 접근의 샘플 구현을 포함하는 다른 메소드들:
       - **`read_travel( )`**: travel BO 엔티티 인스턴스를 읽기 위함
       - **`update_travel( )`**: Travel BO 엔티티 인스턴스를 업데이트하기 위함
       - **`create_travel( )`**: Travel BO 엔티티 인스턴스를 생성하기 위함
       - **`delete_travel( )`**: Travel BO 엔티티 인스턴스를 삭제하기 위함
       - **`activate_travel_draft( )`**: draft Travel BO 엔티티 인스턴스를 활성화하기 위함
       - **`discard_travel_draft( )`**: draft Travel BO 엔티티 인스턴스를 폐기하기 위한 메소드

    **클래스 구현에 대한 간략한 설명**
    1. 메소드 **`if_oo_adt_classrun~main( )`** 은 **F9** 를 누를 때 실행됩니다.
       그 로직은 매우 간단합니다: 메소드는 변수 **`execute`** 에 설정된 값에 따라 실행됩니다:
       - READ: **`1`** | UPDATE: **`2`** | CREATE: **`3`** | DELETE: **`4`** | ACTIVATE draft: **`5`** | DISCARD draft: **`6`** | 모든 메소드: **`55`**

    2. EML 구문 [**`READ ENTITY, ENTITIES`**](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abapread_entity_entities.htm)와
[**`MODIFY ENTITY, ENTITIES`**](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abapmodify_entity_entities.htm)는 다른 작업을 구현하는 데 사용됩니다.
       - **`READ ENTITY, ENTITIES`** 는 RAP BO 인스턴스에 대한 읽기 전용 작업을 수행하는 데 사용됩니다.
       - **`MODIFY ENTITY, ENTITIES`** 는 RAP BO 인스턴스에 대한 트랜잭션 수정 작업을 수행하는 데 사용됩니다.
         여기에는 표준 작업(`CREATE`, `CREATE BY`, `UPDATE`, `DELETE`)뿐만 아니라 표준 draft 작업(`EDIT`, `ACTIVATE`, `DISCARD`)
         및 `EXECUTE` 키워드를 사용하는 비표준 작업(action)이 포함됩니다.

    3. 아래 실습에서 다른 메소드 구현을 살펴보고 시도해 볼 것입니다.

</details>


## Exercise 9.2: _Travel_ BO 엔티티 인스턴스 READ
[^Top of page](#)

> 클래스 ![class icon](images/adt_class.png)**`ZRAP100_CL_EML_###`** 의 메소드 **`read_travel( )`** 은 draft 또는 active Travel BO 엔티티 인스턴스를 읽는 방법에 대한 샘플 구현을 제공합니다.
> 이 메소드는 메소드 **`if_oo_adt_classrun~main( )`** 에서 변수 **`execute`** 가 **`1`** 또는 **`55`** 로 설정될 때 실행됩니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 메소드 **`read_travel( )`** 의 구현으로 이동하여 살펴봅니다.

   1. **(1)** 내부 테이블 **`travels`**
      - BDEF 파생 타입(`TYPE TABLE FOR READ IMPORT`)을 사용하여 선언됩니다. `READ` 요청을 지정하는 데 사용됩니다.
        테이블 이름에 커서를 놓고 **F2** 를 눌러 요소 정보를 볼 수 있습니다.
        > [BDEF 파생 타입](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenrpm_derived_types.htm)에 대해 더 읽어보기
      - 요청을 위한 테이블은 관련 인스턴스에 대한 정보로 채워집니다.
   3. **(2)** **`READ ENTITIES`** 가 지정되고 관련 정보가 있는 내부 테이블 `travels`가 지정됩니다.
      - 내부 테이블을 선언하고 전달하는 대신 `VALUE` 생성자를 사용하여 값을 인라인으로 선언할 수 있습니다. 다른 메소드에서는 그렇게 할 것입니다.
      - `ALL FIELDS` 대신, `FIELDS ( FieldName1, FieldName2, FieldName3 )` 추가 항목을 사용하여 읽을 필드의 특정 목록을 지정할 수 있습니다.

   4. **(3)** 콘솔 출력: READ 구문 실행 후 일부 정보가 _Console_ 뷰에 표시됩니다.

    ![ABAP Class](images/emlclass02.png)

2. 이제 메소드 **`if_oo_adt_classrun~main( )`** 의 구현으로 이동하여 변수 **`execute`** 를 **`1`** 로 설정합니다.
   또한 읽을 travel 인스턴스의 ID(**`travel_id`**)와 상태(**`instance_state`**)를 지정합니다.
   이 정보는 _Travel_ Fiori elements 앱에서 얻을 수 있습니다.

   ![ABAP Class](images/emlclass03.png)

3. 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

4. **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고 _Console_ 뷰에서 결과를 확인합니다.
   결과를 _Travel_ Fiori elements 앱의 값과 비교합니다.
   _ABAP Debugger_ 를 사용하여 응답 매개변수를 조사할 수도 있습니다.

   ![ABAP Class](images/emlclass04.png)

5. 원하는 경우 다른 값(travel ID 및 인스턴스 상태)으로 테스트를 반복합니다.

> **추가 정보**: [READ](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/1d01904bf0524ca2b5c6fd1d8158115d.html)
</details>


## Exercise 9.3: _Travel_ BO 엔티티 인스턴스 UPDATE
[^Top of page](#)

> 클래스 ![class icon](images/adt_class.png)**`ZRAP100_CL_EML_###`** 의 메소드 **`update_travel( )`** 은 active Travel BO 엔티티 인스턴스를 업데이트하는 방법에 대한 샘플 구현을 제공합니다. 필드 **`Description`** 이 업데이트됩니다.
> 이 메소드는 메소드 **`if_oo_adt_classrun~main( )`** 에서 변수 **`execute`** 가 **`2`** 또는 **`55`** 로 설정될 때 실행됩니다.
>
> 이 예제에서는 표준 작업 **`UPDATE`** 와 함께 [EML 구문 **`MODIFY ENTITIES`**](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abapmodify_entity_entities.htm)가 사용됩니다.
> 참고: EML API를 사용할 때는 자신의 Draft 인스턴스만 업데이트할 수 있습니다. 연결된 draft 인스턴스가 없는 active BO 엔티티 인스턴스를 조작할 때는 먼저 표준 draft 작업 **`EXECUTE Edit`** 를 사용해야 합니다.

>> **정보**:
>> `UPDATE` 구문은 엔티티 인스턴스의 편집을 가능하게 합니다.
>>
>> **더 읽어보기**: [MODIFY >> General Information: UPDATE](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/9000a42dcd604a7799527cbb00bc4a69.html)

  <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 메소드 **`update_travel( )`** 의 구현을 확인합니다.
   **`UPDATE`** 요청을 위한 데이터(즉, 내부 테이블)가 **`VALUE`** 연산자를 사용하여 인라인으로 채워지는 것을 볼 수 있습니다.
   ABAP 편집기에서 각 ABAP 및 EML 구문에 대한 자세한 정보를 얻으려면 클래식 **F1 도움말** 을 활용하십시오.

2. 이제 메소드 **`if_oo_adt_classrun~main( )`** 의 구현으로 이동하여 변수 **`execute`** 를 **`2`** 로 설정합니다.
   또한 업데이트할 travel 인스턴스의 ID(**`travel_id`**)와 상태(**`instance_state`**)를 지정합니다.
   이 정보는 _Travel_ Fiori elements 앱에서 얻을 수 있습니다.

   클래스를 실행하기 **전에** 앱에서 현재 설명을 확인하십시오.

   위에서 언급했듯이 draft 인스턴스는 이 메소드로 업데이트할 수 **없지만**, 어떤 일이 일어나는지 시도해 볼 수 있습니다.

   ```ABAP
     "specify the operation to be executed
     DATA(execute) = 2.
     ...
     "UPDATE a Travel BO entity instance
     IF execute = 2 OR execute = 55.
       travel_id      = '0000000'.
       instance_state = is_active.
       update_travel( ).
     ENDIF.
   ```

3. 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

4. **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고 _Console_ 뷰에서 결과를 확인합니다.

   ![ABAP Class](images/emlclass05.png)

5. _Travel_ Fiori elements 앱에서 업데이트된 **description** 을 확인합니다.

6. 설명이 업데이트되지 않은 것을 알 수 있습니다.
   ADT에서 **F9** 를 눌러 클래스를 다시 실행할 수 있습니다.

   구현을 자세히 살펴보십시오.

   **질문:** 이 잘못된 동작의 원인을 찾았습니까?

   <details>
     <summary>해결 방법을 보려면 클릭하여 펼치기!</summary>

      > **`COMMIT ENTITIES`** 구문이 현재 주석 처리되어 있습니다. 즉, 업데이트된 설명은 트랜잭션 버퍼에서만 사용할 수 있고 데이터베이스에는 지속되지 않습니다!
      >
      > 이 문제를 해결하려면 소스 코드에서 각 줄 시작 부분의 **`*`** 를 제거하여 `COMMIT ENTITIES` 블록의 주석을 해제하십시오.
      >
      > ```ABAP
      >  "persist the transactional buffer
      >  COMMIT ENTITIES
      >    RESPONSE OF ZRAP100_R_TravelTP_###
      >    FAILED DATA(failed_commit)
      >    REPORTED DATA(reported_commit).
      > ```
      > 또한 _console output_ 섹션에서 다음 줄의 주석을 해제하십시오:
      >
      > ```ABAP
      > ELSEIF failed_commit IS NOT INITIAL.
      >  console_output->write( |- Cause for failed commit = { failed_commit-travel[ 1 ]-%fail-cause } | ).
      > ```
      >
      > 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.
      >
      > **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고
      > _Travel_ Fiori elements 앱에서 업데이트된 **description** 을 확인하십시오.
      >
      > 소스 코드는 다음과 같아야 합니다:
      > ![ABAP Class](images/emlclass05b.png)
      >

   </details>

7. 원하는 경우 다른 값(travel ID 및 인스턴스 상태)으로 테스트를 반복합니다.

</details>


## Exercise 9.4: _Travel_ BO 엔티티 인스턴스 CREATE
[^Top of page](#)

> 클래스 ![class icon](images/adt_class.png)**`ZRAP100_CL_EML_###`** 의 메소드 **`create_travel( )`** 은 새로운 draft 또는 active Travel BO 엔티티 인스턴스를 생성하는 방법에 대한 샘플 구현을 제공합니다. action **`rejectTravel`** 도 인스턴스에서 수행됩니다.
> 이 메소드는 메소드 **`if_oo_adt_classrun~main( )`** 에서 변수 **`execute`** 가 **`3`** 또는 **`55`** 로 설정될 때 실행됩니다.
>
> 이 구현에서는 표준 작업 **`CREATE`** 와 비표준 작업 **`EXECUTE`** _**`action`**_ 과 함께 EML 구문 **`MODIFY ENTITIES`** 가 사용됩니다.

>>
>> **정보**:
>> `CREATE` 구문은 엔티티 인스턴스의 생성을 가능하게 합니다. `CREATE BY` association 구문은 EML 구문에 지정된 엔티티의 association을 사용하여 자식 엔티티의 인스턴스를 생성합니다.
>> 현재 실습에서는 `CREATE` 구문만 사용합니다.
>> **더 읽어보기**: [MODIFY >> General Information: CREATE and CREATE BY Association](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/9000a42dcd604a7799527cbb00bc4a69.html)
>>
>> `EXECUTE Action` 구문은 action의 실행을 가능하게 합니다.
>> **더 읽어보기**: [MODIFY >> General Information: EXECUTE Action](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/9000a42dcd604a7799527cbb00bc4a69.html)

  <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 메소드 **`if_oo_adt_classrun~main( )`** 의 구현으로 이동하여 변수 **`execute`** 를 **`3`**으로 설정합니다.
   또한 생성될 새 travel 인스턴스의 상태(**`instance_state`**)를 지정합니다. ID는 내부 early numbering에 의해 자동으로 할당됩니다.

   ```ABAP
     "specify the operation to be executed
     DATA(execute) = 3.
     ...
     "CREATE a Travel BO entity instance
     IF execute = 3 OR execute = 55.
       instance_state = is_active.
       create_travel( ).
     ENDIF.
   ```

2. 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

3. **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고 _Console_ 뷰에서 결과를 확인합니다.
   _Travel_ Fiori elements 앱에서 새로 생성된 인스턴스를 확인합니다.
   _ABAP Debugger_ 를 사용하여 응답 매개변수를 조사할 수도 있습니다.

   ![ABAP Class](images/emlclass06.png)

5. 원하는 경우 다른 인스턴스 상태로 테스트를 반복합니다.

</details>


## Exercise 9.5: _Travel_ BO 엔티티 인스턴스 DELETE
[^Top of page](#)

> 메소드 **`delete_travel( )`** 은 active Travel BO 엔티티 인스턴스를 삭제하는 방법에 대한 샘플 구현을 제공합니다.
> 이 메소드는 메소드 **`if_oo_adt_classrun~main( )`** 에서 변수 **`execute`** 가 **`4`** 또는 **`55`** 로 설정될 때 실행됩니다.
>
> 이 구현에서는 표준 작업 **`DELETE`** 와 함께 EML 구문 **`MODIFY ENTITIES`** 가 사용됩니다.
> 참고: Draft 인스턴스는 이 EML 구문으로 삭제할 수 **없습니다**. 대신 표준 draft 작업 `EXECUTE Discard`를 사용해야 합니다.

>>
>> **정보**:
>> `DELETE` 구문은 엔티티 인스턴스의 삭제를 가능하게 합니다.
>>
>> **더 읽어보기**: [MODIFY >> General Information: DELETE](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/9000a42dcd604a7799527cbb00bc4a69.html)

  <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 메소드 **`if_oo_adt_classrun~main( )`** 의 구현으로 이동하여 변수 **`execute`** 를 **`4`** 로 설정합니다.
   또한 삭제할 travel 인스턴스의 ID(**`travel_id`**)와 상태(**`instance_state`**)를 지정합니다.
   이 정보는 _Travel_ Fiori elements 앱에서 얻을 수 있습니다.

   위에서 언급했듯이 draft 인스턴스는 이 메소드로 삭제할 수 **없지만**, 어떤 일이 일어나는지 시도해 볼 수 있습니다.

   ```ABAP
     "specify the operation to be executed
     DATA(execute) = 4.
     ...
     "DELETE a Travel BO entity instance
     IF execute = 4 OR execute = 55.
       travel_id      = '00000000'.
       instance_state = is_active.
       delete_travel( ).
     ENDIF.
   ```

2. 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

3. **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고 _Console_ 뷰에서 결과를 확인합니다.
   _Travel_ Fiori elements 앱에서 삭제된 인스턴스를 확인합니다.
   _ABAP Debugger_ 를 사용하여 응답 매개변수를 조사할 수도 있습니다.

   ![ABAP Class](images/emlclass07.png)

5. 원하는 경우 다른 값(travel ID 및 인스턴스 상태)으로 테스트를 반복합니다.

</details>

## Exercise 9.6: Draft _Travel_ BO 엔티티 인스턴스 ACTIVATE
[^Top of page](#)

> 메소드 **`activate_travel_draft( )`** 는 draft Travel BO 엔티티 인스턴스를 활성화하는 방법에 대한 샘플 구현을 제공합니다.
> 이 메소드는 메소드 **`if_oo_adt_classrun~main( )`** 에서 변수 **`execute`** 가 **`5`** 또는 **`55`** 로 설정될 때 실행됩니다.
>
> 이 구현에서는 표준 draft 작업 **`EXECUTE Activate`** 와 함께 EML 구문 **`MODIFY ENTITIES`** 가 사용됩니다.

>>
>> **정보**:
>> draft 인스턴스에서 draft action `ACTIVATE`를 실행하면 draft 인스턴스를 active 애플리케이션 버퍼로 복사합니다. 이는 `PREPARE`와 draft 인스턴스의 상태에 따라 active BO 인스턴스를 변경하는 modify 요청을 호출합니다. active 인스턴스가 성공적으로 생성되거나 업데이트되면 draft 인스턴스는 폐기됩니다. activate action은 active 인스턴스를 데이터베이스에 저장하지 않습니다. 실제 저장은 EML을 통한 `COMMIT ENTITIES` 또는 OData의 경우 save sequence를 호출하여 별도로 실행됩니다. OData 요청의 경우 이는 RAP 프레임워크에 의해 자동으로 수행됩니다.
>> activate action은 draft 인스턴스에서만 가능합니다.
>>
>> **더 읽어보기**: [Creating Active Data](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/e61754e947724d1c90676b2605ee453f.html)

  <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 메소드 **`if_oo_adt_classrun~main( )`** 의 구현으로 이동하여 변수 **`execute`** 를 **`5`** 로 설정합니다.
   또한 활성화할 draft travel 인스턴스의 ID(**`travel_id`**)를 지정합니다.
   이 정보는 _Travel_ Fiori elements 앱에서 얻을 수 있습니다. 필요한 경우 draft 인스턴스를 생성하십시오.

   ```ABAP
     "specify the operation to be executed
     DATA(execute) = 5.
     ...
    "ACTIVATE a draft Travel BO entity instance
    IF execute = 5 OR execute = 55.
      travel_id      = '00000000'.
      activate_travel_draft( ).
    ENDIF.
   ```

2. 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

3. **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고 _Console_ 뷰에서 결과를 확인합니다.

   _Travel_ Fiori elements 앱에서 지정된 draft 인스턴스가 활성화되었는지 확인합니다.

   _ABAP Debugger_ 를 사용하여 응답 매개변수를 조사할 수도 있습니다.

   ![ABAP Class](images/emlclass08.png)

5. 원하는 경우 다른 값(travel ID 및 인스턴스 상태)으로 테스트를 반복합니다.

</details>

## Exercise 9.7: Draft _Travel_ BO 엔티티 인스턴스 DISCARD
[^Top of page](#)

> 메소드 **`discard_travel_draft( )`** 는 draft Travel BO 엔티티 인스턴스를 폐기하는 방법에 대한 샘플 구현을 제공합니다.
> 이 메소드는 메소드 **`if_oo_adt_classrun~main( )`** 에서 변수 **`execute`** 가 **`6`** 또는 **`55`** 로 설정될 때 실행됩니다.
>
> 이 구현에서는 표준 draft 작업 **`EXECUTE Discard`** 와 함께 EML 구문 **`MODIFY ENTITIES`** 가 사용됩니다.

>>
>> **정보**:
>> draft action `DISCARD`를 실행하면 draft 인스턴스가 draft 데이터베이스 테이블에서 삭제됩니다. 또한 가능한 exclusive lock이 해제됩니다. `DISCARD`는 new-draft 및 edit-draft에서 실행될 수 있습니다. lock을 해제하는 것 외에 이 action은 active 인스턴스에 영향을 미치지 않습니다. 삭제되는 것은 draft 데이터뿐입니다.
>>
>> **더 읽어보기**: [Deleting Instances of a Draft BO >> Activate Action](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/ef0f5136cb084dd4ab4fb19128b2ab13.html)

  <details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 메소드 **`if_oo_adt_classrun~main( )`** 의 구현으로 이동하여 변수 **`execute`** 를 **`6`**으로 설정합니다.
   또한 폐기할 draft travel 인스턴스의 ID(**`travel_id`**)를 지정합니다.
   이 정보는 _Travel_ Fiori elements 앱에서 얻을 수 있습니다. 필요한 경우 draft 인스턴스를 생성하십시오.

   ```ABAP
     "specify the operation to be executed
     DATA(execute) = 6.
     ...
    "DISCARD a draft Travel BO entity instance
    IF execute = 6 OR execute = 55.
      travel_id      = '00000000'.
      discard_travel_draft( ).
    ENDIF.
   ```

2. 변경 사항을 저장 ![save icon](images/adt_save.png)하고 활성화 ![activate icon](images/adt_activate.png)합니다.

3. **F9** 를 눌러 클래스를 콘솔 애플리케이션으로 실행하고 _Console_ 뷰에서 결과를 확인합니다.
  _Travel_ Fiori elements 앱에서 폐기된 draft 인스턴스를 확인합니다.
  _ABAP Debugger_ 를 사용하여 응답 매개변수를 조사할 수도 있습니다.

   ![ABAP Class](images/emlclass09.png)

5. 원하는 경우 다른 draft 인스턴스로 테스트를 반복합니다.

</details>


## Exercise 9.8: EML 가지고 놀기
[^Top of page](#)

제목에서 알 수 있듯이: EML을 가지고 놀아보세요!

예를 들어,
- 원하는 메소드를 복사하고 원하는 대로 조정하십시오.
- 모든 작업을 실행하십시오: 이를 위해 변수 **`execute`** 를 **`55`** 로 설정하고 다른 작업에 필요한 값(**`travel_id`** 및 **`instance_state`**)을 지정하십시오.
  <pre> DATA(execute) = 55. </pre>

자유롭게 질문하십시오.

## 요약
[^Top of page](#)

이제 다양한 EML 구문을 사용하여 다음을 수행해 보았습니다:
- _Travel_ 엔티티 인스턴스 읽기,
- _Travel_ 엔티티 인스턴스 업데이트,
- 새로운 _Travel_ 엔티티 인스턴스 생성,
- _Travel_ 엔티티 인스턴스 삭제,
- draft _Travel_ 엔티티 인스턴스 활성화, 그리고
- draft _Travel_ 엔티티 인스턴스 폐기,

이 실습을 모두 마쳤습니다. **축하합니다!**

이 실습 세션에서는 ABAP RESTful Application Programming Model (RAP)의 _managed_ 구현 유형으로 간단한 Fiori elements 앱을 구축하는 방법과 ABAP Unit으로 BO 테스트를 작성하는 방법, 그리고 Entity Manipulation Language (EML)를 사용하여 RAP 컨텍스트 외부에서 BO에 접근하는 방법을 배웠습니다!

이 워크숍에 참석해 주셔서 감사합니다!

---
<!--
## 부록
[^Top of page](#)

[sources](sources) 폴더에서 플레이그라운드 ABAP 클래스의 소스 코드를 찾을 수 있습니다. 자리 표시자 `###`의 모든 발생을 그룹 ID로 바꾸는 것을 잊지 마십시오.

- ![document](images/doc.png) [Class ZRAP100_CL_EML_###](sources/EX9_CLASS_ZRAP100_CL_EML.txt)

-->
