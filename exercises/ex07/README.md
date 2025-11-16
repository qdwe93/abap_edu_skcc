[Home - RAP100](../../#exercises)

# [선택] 연습문제 7: BO 기능 개선 – Dynamic Feature Control

## 소개
이전 연습문제에서는 BO entity _Travel_ 에 대한 다양한 instance-bound action을 정의하고 구현했습니다(참조: [연습문제 6](../ex06/README.md)).

이번 연습문제에서는 _Travel_ entity의 일부 standard operation과 non-standard operation에 대한 dynamic instance feature control을 구현합니다.

- [7.1 - Dynamic Instance Feature Control 정의하기](#exercise-71-define-the-dynamic-instance-feature-control)
- [7.2 - Dynamic Instance Feature Control 구현하기](#exercise-72-implement-the-dynamic-instance-feature-control)
- [7.3 - 향상된 앱 미리보기 및 테스트](#exercise-73-preview-and-test-the-enhanced-travel-app)
- [요약](#summary)

> **알림**: 아래 연습문제 단계에서 접미사 플레이스홀더 **`###`** 를 본인이 선택했거나 할당받은 그룹 ID로 반드시 교체하세요.

### 정보: Dynamic Feature Control
> 애플리케이션 개발자로서, 비즈니스 오브젝트 엔티티의 특정 속성을 기반으로 어떤 필드를 읽기 전용(read-only) 또는 필수(mandatory)로 만들지, 또는 업데이트나 액션과 같은 기능을 허용할지 결정하고 싶을 수 있습니다. 이 속성은 해당 비즈니스 오브젝트의 인스턴스와 관련이 있으므로 Dynamic Feature Control이라고 부릅니다.
> 
> ℹ **추가 정보**: [Adding Static and Dynamic Feature Control](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/b6eb96dd784247a99cf8d70f77232ba4.html)

## Exercise 7.1: Dynamic Instance Feature Control 정의하기
[^Top of page](#)

> Standard operation인 **`update`** 와 **`delete`**, draft action인 **`Edit`**, 그리고 instance action인 **`deductDiscount`** 에 대한 dynamic instance feature control을 정의합니다.
> 
> ⚠ 이전 연습문제([참조: 연습문제 6.3 - Actions](../ex6/readme.md))에서 구현했다면, 선택 사항인 instance action **`acceptTravel`** 과 **`rejectTravel`** 에 대한 dynamic instance feature control도 정의하게 됩니다.

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>
    
1. Behavior definition ![behaviordefinition](images/adt_bdef.png)**`ZRAP100_R_TRAVELTP_###`** 을 열고, 아래 코드 스니펫과 스크린샷에 보이는 것처럼 다음 operation에 **`( features : instance )`** 구문을 추가합니다:
    - Standard operations **`update`** 및 **`delete`** 
    - Draft action **`Edit`** 
    - Instance action **`deductDiscount`** 
      
      ```ABAP
        ...
        create;
        update ( features : instance ) ;
        delete ( features : instance ) ;
        ...
        action ( features : instance ) deductDiscount parameter /dmo/a_travel_discount result [1] $self;        
        ...
        draft action ( features : instance ) Edit;
      ```
     
      ⚠**주의**⚠:  
      만약 이전 연습문제([참조: 연습문제 6.3 - Actions](../ex6/readme.md))에서 instance action **`acceptTravel`** 과 **`rejectTravel`** 을 정의하고 구현했다면, 스크린샷에 보이는 것처럼 아래 제공된 코드 스니펫도 추가하세요.   

       ```ABAP
        action ( features : instance ) acceptTravel result [1] $self;
        action ( features : instance ) rejectTravel result [1] $self;        
      ```
       
       작성된 소스 코드는 다음과 같을 것입니다: 
 
       ![Travel Behavior Definition](images/f.png)
    
2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))하세요.

3. Behavior definition의 상단에서 BO entity 이름 **`ZRAP100_R_TRAVELTP_###`** 에 커서를 놓고 **Ctrl+1** 을 눌러 **Quick Assist** 뷰를 엽니다.
  
   _**`Add method for operation instance_features of entity zrap100_r_traveltp_### ...`**_ 항목을 선택하여 필요한 메서드를 Behavior pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVELTP_###`** 의 local handler class `lcl_handler`에 추가합니다. 
   
   결과는 다음과 같아야 합니다:
   
   ![Travel BO Behavior Pool](images/l.png)
    
4. Behavior pool ![class icon](images/adt_class.png)**`ZRAP100_BP_TRAVEL_###`** 에 있는 local handler class의 선언부에서 **`get_instance_features`** 메서드의 인터페이스를 확인하세요.  
  
   메서드 이름 중 하나에 커서를 놓고 **F2** 를 눌러 **ABAP Element Info** 뷰를 열어 전체 메서드 인터페이스를 살펴보세요.  

   ![Travel BO Behavior Pool](images/l2.png)  
   **간단한 설명**:  
   - 메서드 이름 뒤의 **`FOR INSTANCE FEATURES`** 구문은 이 메서드가 instance-based dynamic feature control의 구현을 제공함을 나타냅니다.
   - instance method `get_instance_features`의 시그니처:
     - `IMPORTING` 파라미터 **`keys`** - feature control이 실행되어야 할 인스턴스의 키를 담고 있는 테이블.
     -  암시적 `IMPORTING` 파라미터 **`requested_features`** - consumer가 dynamic feature control을 요청한 엔티티의 요소(필드, standard operation, action)를 반영하는 구조체. 
     - 암시적 `CHANGING` 파라미터 (일명 _implicit response parameters_):  
       - **`result`** - 수행된 feature control 계산의 결과를 저장하는 데 사용됩니다.      
       - **`failed`** - 오류가 발생한 데이터셋을 식별하기 위한 정보가 담긴 테이블.
       - **`reported`** - instance-specific 메시지를 위한 데이터가 담긴 테이블.

   이제 구현을 진행하세요.  
 
 
</details>

## Exercise 7.2: Dynamic Instance Feature Control 구현하기 
[^Top of page](#)

> Standard operation인 **`update`** 와 **`delete`**, draft action인 **`Edit`**, 그리고 instance action인 **`deductDiscount`** 에 대한 dynamic instance feature control을 구현합니다.
>
> ⚠ 이전 연습문제 단계에서 정의했다면, instance action **`acceptTravel`** 과 **`rejectTravel`** 에 대한 dynamic instance feature control도 구현하게 됩니다. 
> 
> 다음과 같은 동적 behavior가 백엔드에서 구현되고 Fiori UI에 표시될 것입니다:
> - _travel_ instance의 전체 상태(overall status)가 `Accepted`(**`A`**)인 경우, 해당 인스턴스에 대해 standard operation인 **`update`** 와 **`delete`**, 그리고 action인 **`Edit`** 와 **`deductDiscount`** 는 비활성화되어야 합니다.   
> - 추가적으로, 다음과 같은 토글(활성화/비활성화) behavior가 구현되어야 합니다:
>   - 전체 상태가 `Accepted`(**`A`**)인 경우, action **`acceptTravel`** 은 비활성화되어야 합니다. 
>   - 전체 상태가 `Rejected`(**`X`**)인 경우, action **`rejectTravel`** 은 비활성화되어야 합니다. 

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>


3. Local handler class의 구현부에서 instance feature control 메서드 **`get_instance_features`** 를 구현합니다. 
   
   로직은 다음 단계로 구성됩니다:  
   1. 전달된 _travel_ 인스턴스의 관련 데이터를 읽습니다. 
      현재 시나리오에서는 operation 상태를 결정하기 위해 **`TravelID`** 와 **`OverallStatus`** 필드만 필요합니다. 
   2. 조건을 평가하고 다른 operation들의 상태를 결정합니다. 
      현재 시나리오에서는 이를 위해 `COND` 연산자를 인라인으로 사용합니다. 
   3. 결과 세트를 적절하게 설정합니다.   
   
   이를 위해, 현재 메서드 구현을 아래에 제공된 코드 스니펫으로 교체하고, 플레이스홀더 **`###`** 의 모든 발생을 본인의 그룹 ID로 바꾸세요. 

   ⚠**주의**⚠:   
   만약 이전 연습문제([참조: 연습문제 6 - Actions](../ex6/readme.md))에서 instance action **`acceptTravel`** 과 **`rejectTravel`** 을 정의하고 구현했다면, 삽입된 소스 코드에서 해당하는 네(4) 줄의 주석을 해제하세요.   
 
   EML 구문 및 다른 ABAP 구문에 대한 자세한 정보는 **F1 도움말** 을 활용할 수 있습니다.
  
      ```ABAP
      **************************************************************************
      * Instance-based dynamic feature control
      **************************************************************************
        METHOD get_instance_features.
          " read relevant travel instance data
          READ ENTITIES OF ZRAP100_R_TravelTP_### IN LOCAL MODE
            ENTITY travel
               FIELDS ( TravelID OverallStatus )
               WITH CORRESPONDING #( keys )
             RESULT DATA(travels)
             FAILED failed.

          " evaluate the conditions, set the operation state, and set result parameter
          result = VALUE #( FOR travel IN travels
                             ( %tky                   = travel-%tky

                               %features-%update      = COND #( WHEN travel-OverallStatus = travel_status-accepted
                                                                THEN if_abap_behv=>fc-o-disabled ELSE if_abap_behv=>fc-o-enabled   )
                               %features-%delete      = COND #( WHEN travel-OverallStatus = travel_status-open
                                                                THEN if_abap_behv=>fc-o-enabled ELSE if_abap_behv=>fc-o-disabled   )
                               %action-Edit           = COND #( WHEN travel-OverallStatus = travel_status-accepted
                                                                THEN if_abap_behv=>fc-o-disabled ELSE if_abap_behv=>fc-o-enabled   )
      *                           %action-acceptTravel   = COND #( WHEN travel-OverallStatus = travel_status-accepted
      *                                                            THEN if_abap_behv=>fc-o-disabled ELSE if_abap_behv=>fc-o-enabled   )
      *                           %action-rejectTravel   = COND #( WHEN travel-OverallStatus = travel_status-rejected
      *                                                            THEN if_abap_behv=>fc-o-disabled ELSE if_abap_behv=>fc-o-enabled   )
                               %action-deductDiscount = COND #( WHEN travel-OverallStatus = travel_status-open
                                                                THEN if_abap_behv=>fc-o-enabled ELSE if_abap_behv=>fc-o-disabled   )
                            ) ).

        ENDMETHOD.
      ```   
      
      작성된 소스 코드는 다음과 같을 것입니다:
      
      ![Travel Behavior Pool](images/instance_feature.png)
      
  2. 변경 사항을 저장(![save icon](images/adt_save.png))하고 활성화(![activate icon](images/adt_activate.png))하세요.
 
 이제 구현이 완료되었습니다.
 
 </details>
 
## Exercise 7.3: 개선된 Travel 앱 미리보기 및 테스트
[^Top of page](#)

이제 SAP Fiori elements 앱을 테스트할 수 있습니다. 

 <details>
  <summary>🔵 클릭하여 펼치기!</summary>

브라우저가 아직 열려 있다면 **F5** 를 눌러 애플리케이션을 새로 고침하거나, Service binding **`ZRAP100_UI_TRAVEL_O4_###`** 으로 이동하여 **`Travel`** entity set에 대한 Fiori elements 앱 미리보기를 시작할 수 있습니다.

이제 백엔드에서 구현된 dynamic feature control 로직을 테스트해 볼 수 있습니다.

예를 들어, 전체 상태(overall status)가 _**Accepted**_ 인 _travel_ 인스턴스를 선택하고, _**Accept Travel**_, _**Edit**_, _**Delete**_ 버튼의 상태를 확인하세요. 모두 비활성화되어 있어야 합니다.

> UI에서 기대되는 구현된 dynamic BO behavior를 기억하세요:
> - _travel_ instance의 전체 상태가 _**Accepted**_ (**`A`**) 또는 _**Rejected**_ (**`X`**)인 경우, 해당 인스턴스에 대해 _**Edit**_ 및 _**Delete**_ 버튼이 비활성화되어야 합니다. 
> - 추가적으로, 두 instance action에 대해 다음과 같은 토글(활성화/비활성화) behavior가 표시되어야 합니다:
>   - 전체 상태가 _**Accepted**_ (**`A`**)인 경우, _**Accept Travel**_ action이 비활성화되어야 합니다. 
>   - 전체 상태가 _**Rejected**_ (**`X`**)인 경우, _**Reject Travel**_ action이 비활성화되어야 합니다. 

   ![Travel App Preview](images/preview10.png)

</details>

## 요약
[^Top of page](#)

이제 여러분은... 
- Behavior definition에서 standard 및 non-standard operation에 대한 dynamic instance feature control을 정의했고, 
- Behavior pool에서 그것을 구현했으며,
- 향상된 Fiori elements _Travel_ 앱을 미리보고 테스트했습니다.

다음 연습문제로 계속 진행할 수 있습니다 – **[선택] [연습문제 8: RAP BO를 위한 ABAP Unit Test 작성](../ex08/README.md)**

---
<!--
## Appendix
[^Top of page](#)

Find the source code for the behavior definition and behavior implementation class (aka behavior pool) in the [sources](sources) folder. Don't forget to replace all occurences of the placeholder `###` with your group ID.

> ℹ **Info**:   
> The solution comprises the implementation of all four actions, i.e. `deductDiscount`, `copyTravel`, `acceptTravel`, and `rejectTravel`.

- ![document](images/doc.png) [CDS BDEF ZRAP100_R_TRAVELTP_###](sources/EX7_BDEF_ZRAP100_R_TRAVELTP.txt)
- ![document](images/doc.png) [Class ZRAP100_BP_TRAVELTP_###](sources/EX7_CLASS_ZRAP100_BP_TRAVELTP.txt)
-->
