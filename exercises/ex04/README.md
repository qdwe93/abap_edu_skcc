[Home - RAP100](../../#exercises)

# 연습문제 4: BO 기능 개선 – Determinations

## 소개

이전 연습에서는 BO 엔터티 _Travel_ 의 새 인스턴스에 대한 식별자(ID)를 자동으로 할당하기 위해 early numbering을 정의하고 구현했습니다(참조: [연습 3](../ex03/README.md)).

이번 연습에서는 _Travel_ 엔터티 인스턴스의 전체 상태에 대한 기본값을 설정하는 데 사용될 determination, `setStatusToOpen`을 정의하고 구현할 것입니다. 여러분은 Entity Manipulation Language (EML)를 사용하여 _Travel_ 비즈니스 오브젝트의 트랜잭션 behavior를 구현하게 됩니다.

- [4.1 - Determination `setStatusToOpen` 정의하기](#exercise-41-define-the-determination-setstatustoopen)
- [4.2 - Determination `setStatusToOpen` 구현하기](#exercise-42-implement-the-determination-setstatustoopen)
- [4.3 - 향상된 Travel 앱 미리보기 및 테스트](#exercise-43-preview-and-test-the-enhanced-travel-app)
- [요약](#summary)


> **알림**: 아래 연습 단계에서 접미사 플레이스홀더 **`###`** 를 여러분이 선택했거나 할당받은 그룹 ID로 반드시 교체하십시오.

### Determinations에 대하여
> Determination은 트리거 조건에 따라 비즈니스 오브젝트의 인스턴스를 수정하는 비즈니스 오브젝트 behavior의 선택적 부분입니다. Determination의 트리거 조건이 충족되면 RAP 프레임워크에 의해 암시적으로 호출됩니다. 트리거 조건은 modify 작업과 수정된 필드가 될 수 있습니다.
>
> **추가 정보**: [Determinations](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/6edb0438d3e14d18b3c403c406fbe209.html)

### Entity Manipulation Language (EML)에 대하여
> Entity Manipulation Language (EML)는 RAP 비즈니스 오브젝트에 대한 API 기반 접근을 제공하는 ABAP 언어의 확장입니다. EML은 RAP BO의 트랜잭션 behavior를 구현하고 RAP 컨텍스트 외부에서 기존 RAP BO에 접근하는 데 사용됩니다.
>
> PS: 일부 EML 구문은 소위 local mode에서 사용할 수 있습니다 - [**`IN LOCAL MODE`** 추가 구문](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abapin_local_mode.htm)을 사용하여 - 기능 제어 및 권한 검사를 제외할 수 있습니다. 이 추가 구문은 특정 RAP BO의 behavior implementation(일명 behavior pool)에서 자체 인스턴스에 접근할 때만 사용할 수 있으며, 다른 RAP BO의 인스턴스에 접근하는 데는 사용할 수 없습니다.
>
> EML 참조 문서는 ABAP Keyword Documentation에서 제공됩니다.
> ABAP 편집기에서 **F1** 키를 눌러 클래식 **F1 Help** 를 사용하여 각 구문에 대한 자세한 정보를 얻을 수 있습니다.
>
> **추가 정보**: [Entity Manipulation Language (EML)](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/af7782de6b9140e29a24eae607bf4138.html) | [ABAP for RAP Business Objects](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenabap_for_rap_bos.htm)

## 연습 4.1: Determination `setStatusToOpen` 정의하기
[^맨 위로](#)

> _Travel_ 엔터티의 behavior definition에서 determination **`setStatusToOpen`** 을 정의합니다. 이 determination은 새로운 _Travel_ 인스턴스를 생성할 때 `OverallStatus` 필드의 기본값을 `open` (`O`)으로 설정하는 데 사용됩니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. _Travel_ BO 엔터티의 behavior definition ![bdef icon](images/adt_bdef.png)**`ZRAP100_R_TravelTP_###`** 로 이동하여 **`delete;`** 구문 뒤에 아래 스크린샷과 같이 다음 구문을 삽입하십시오.

   ```ABAP
     determination setStatusToOpen on modify { create; }
   ```

   <!-- ![Travel BO Definition](images/new14.png) -->
   <img src="images/new14.png" alt="Travel BO Definition" width="60%">

   **간단한 설명**:
   이 구문은 새로운 determination의 이름 `setStatusToOpen`과 새로운 _travel_ 인스턴스를 생성할 때(`{ create }`)의 determination 시간으로 `on modify`를 지정합니다.

2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))하십시오.

3. 이제 ADT Quick Fix를 사용하여 behavior implementation 클래스에 필요한 메서드를 선언합니다.

   determination 이름 **`setStatusToOpen`** 에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 연 다음, 뷰에서 _`Add method for determination setstatustoopen of entity zrap100_r_travel_### ...`_ 항목을 선택하십시오.

   그 결과, `FOR DETERMINE` 메서드 **`setStatusToOpen`** 이 _Travel_ BO 엔터티의 behavior pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 로컬 핸들러 클래스 **`lcl_handler`** 에 추가됩니다.

   <!-- ![Travel BO Behavior Pool](images/new15.png) -->
   <img src="images/new15.png" alt="Travel BO Behavior Pool" width="60%">

이제 determination의 정의를 마쳤습니다.

</details>

## 연습 4.2: Determination `setStatusToOpen` 구현하기
[^맨 위로](#)

이제 정의된 determination의 로직을 behavior pool에서 구현합니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 먼저 로컬 핸들러 클래스 `lcl_handler`의 선언부에서 메서드 **`setStatusToOpen`** 의 인터페이스를 확인하십시오.

   메서드 이름 **`setStatusToOpen`** 에 커서를 놓고 **F2** 를 눌러 **ABAP Element Info** 뷰를 열어 전체 메서드 인터페이스를 살펴보십시오.

   ![Travel BO Behavior Pool](images/new16.png)

   **간단한 설명**:
   - **`FOR DETERMINE`** 추가 구문은 이 메서드가 determination의 구현을 제공함을 나타내고, **`ON MODIFY`** 추가 구문은 지정된 트리거 시간을 나타냅니다.
   - `IMPORTING` 파라미터 **`keys`** - determination이 실행될 인스턴스의 키를 포함하는 인터널 테이블입니다.
   - 암시적 **`CHANGING`** 파라미터 **`reported`** - 실패 시 메시지를 반환하는 데 사용됩니다.

    이제 로컬 핸들러 클래스의 구현부에서 메서드를 구현해 보겠습니다.

2. _Travel_ 인스턴스의 전체 상태에 허용된 값을 저장하기 위해 로컬 상수 **`travel_status`** 를 정의합니다.

    아래 스크린샷과 같이 로컬 핸들러 클래스 **`lcl_handler`** 의 정의부에 다음 코드 스니펫을 삽입하십시오.

    ```ABAP
    CONSTANTS:
      BEGIN OF travel_status,
        open     TYPE c LENGTH 1 VALUE 'O', "Open
        accepted TYPE c LENGTH 1 VALUE 'A', "Accepted
        rejected TYPE c LENGTH 1 VALUE 'X', "Rejected
      END OF travel_status.
    ```

    ![Travel BO Behavior Pool](images/s3.png)

3. 이제 클래스의 구현부에서 **`setStatusToOpen`** 메서드를 구현합니다.

   로직은 다음 단계로 구성됩니다:
     1. EML 구문 **`READ ENTITIES`** 를 사용하여 전달된 키(**`keys`**)의 travel 인스턴스를 읽습니다.
     2. **`IN LOCAL MODE`** 추가 구문은 기능 제어 및 권한 검사를 제외하는 데 사용됩니다.
     3. 전체 상태가 이미 설정된 _Travel_ 인스턴스는 모두 제거합니다.
     4. EML 구문 **`MODIFY ENTITIES`** 를 사용하여 나머지 항목의 전체 상태를 **`open`**(**`O`**)으로 설정합니다.
     5. changing 파라미터 **`reported`** 를 설정합니다.

   메서드에 다음 코드 스니펫을 삽입하고 모든 플레이스홀더 `###`를 자신의 그룹 ID로 교체하십시오.
   **F1 help** 를 사용하여 각 EML 구문에 대한 자세한 정보를 얻을 수 있습니다.

   **ABAP Pretty Printer**(**Shift+F1**)를 사용하여 소스 코드 서식을 지정하십시오.

   ```ABAP
    "Read travel instances of the transferred keys
    READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
     ENTITY Travel
       FIELDS ( OverallStatus )
       WITH CORRESPONDING #( keys )
     RESULT DATA(travels)
     FAILED DATA(read_failed).

    "If overall travel status is already set, do nothing, i.e. remove such instances
    DELETE travels WHERE OverallStatus IS NOT INITIAL.
    CHECK travels IS NOT INITIAL.

    "else set overall travel status to open ('O')
    MODIFY ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
      ENTITY Travel
        UPDATE FIELDS ( OverallStatus )
        WITH VALUE #( FOR travel IN travels ( %tky          = travel-%tky
                                              OverallStatus = travel_status-open ) )
    REPORTED DATA(update_reported).

    "Set the changing parameter
    reported = CORRESPONDING #( DEEP update_reported ).
   ```

   소스 코드는 다음과 같아야 합니다:

   ![Travel BO Behavior Pool](images/new17.png)

4. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))하십시오.

</details>

## 연습 4.3: 향상된 Travel 앱 미리보기 및 테스트
[^맨 위로](#)

> 이제 Travel 앱에서 새로운 travel 인스턴스를 생성하여 변경 사항을 미리보고 테스트할 수 있습니다.

<details>
  <summary>🔵 클릭하여 펼치기!</summary>

1. 브라우저가 아직 열려 있다면 **F5** 를 사용하여 애플리케이션을 새로고침하십시오.
   또는 service binding **`ZRAP100_UI_TRAVEL_O4_###`** 로 이동하여 **`Travel`** 엔터티 셋에 대한 Fiori elements 앱 미리보기를 시작하십시오.

2. 새로운 _Travel_ 인스턴스를 생성하십시오. 이제 방금 구현한 로직에 의해 전체 상태가 자동으로 설정되어야 합니다.
   생성된 인스턴스의 초기 전체 상태는 **`open`**(**`O`**)으로 설정되어야 합니다.

   <!-- ![Travel App Preview](images/overallstatus.png) -->
   <!--<img src="images/overallstatus.png" alt="Travel App Preview" width="80%">!-->
   ![Travel App Preview](images/overallstatus.png)

</details>

## 요약
[^맨 위로](#)

이제 여러분은...
- behavior definition에서 determination을 정의하고,
- behavior implementation에서 이를 구현했으며,
- 향상된 Fiori elements 앱을 미리보고 테스트했습니다.

다음 연습으로 계속 진행할 수 있습니다 – **[연습 5: BO Behavior 향상 – Validations](../ex05/README.md)**

---
<!--
## Appendix
[^Top of page](#)

Find the source code for the behavior definition and behavior implementation class (aka behavior pool) in the [sources](sources) folder. Don't forget to replace all occurences of the placeholder `###` with your group ID.

- ![document](images/doc.png) [CDS BDEF ZRAP100_R_TRAVELTP_###](sources/EX4_BDEF_ZRAP100_R_TRAVELTP.txt)
- ![document](images/doc.png) [Class ZRAP100_BP_TRAVELTP_###](sources/EX4_CLASS_ZRAP100_BP_TRAVELTP.txt)
-->
