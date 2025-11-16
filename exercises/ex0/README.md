[Home - RAP100](../../#exercises)

<!-- Exercise 0: Getting Started -->
# 시작하기

## 소개

> ℹ️ 업데이트된 스크립트가 포함된 새로운 RAP100 GitHub 저장소에 여기 👉 [**here**](https://github.com/SAP-samples/abap-platform-rap100)에서 액세스하세요.

이 문서의 스크린샷은 group ID **`000`** 과 system **`D22`** 를 사용하여 촬영되었습니다. group ID **`000`** 의 사용은 **권장하지 않습니다**.

향후 릴리스에서는 ADT dialogs 및 views, 그리고 Fiori UI가 변경될 수 있다는 점에 유의하시기 바랍니다.

이 워크숍의 솔루션은 development package **`ZRAP100_SOL`** 에서 찾거나, [여기](https://github.com/SAP-samples/abap-platform-rap100/)에서 시스템으로 가져올 수 있습니다.

귀하는 이미 SAP 이벤트 팀으로부터 사용자 자격 증명을 받았거나 (아래 _로그인 정보에 대하여_ 참고), SAP BTP ABAP environment Trial 계정을 생성했습니다.

이제 시작하겠습니다!

> [!NOTE]
> **로그인 정보에 대하여**:
> **ABAP Developer Day** 또는 **SAP CodeJam**과 같은 SAP 주도 행사의 참가자는 행사 전 또는 행사 중에 SAP 팀으로부터 이메일을 통해 전용 ABAP system에 대한 logon details(즉, system information 및 user credentials)를 받게 됩니다.
> logon details를 받지 못한 경우 워크숍 강사에게 알려주십시오.


- [Group ID](#group-id)
- [ADT에서 _ABAP Cloud Project_ 또는 _ABAP Project_ 생성](#create-an-abap-cloud-project-or-an-abap-project-in-adt)
- [유용한 정보](#helpful-information)
  - 찾기/바꾸기
  - 최신 ABAP Syntax
  - 유용한 ADT 단축키
- [요약](#summary)


## Group ID
[^Top of page](#)

> 이 실습에서는 워크숍 과정에서 repository artefacts를 고유하게 식별하고 동일한 시스템에서 동일한 워크숍을 수행하는 다른 사용자의 artefacts와 분리하는 데 필요한 group ID를 정의합니다.
>
> group ID는 이 워크숍의 여러 실습에서 placeholder **`###`** 의 모든 발생을 대체하는 데 사용됩니다.
>
> ⚠ **참고:** ⚠
> SAP 팀에서 group ID를 할당받은 경우, 이 섹션을 건너뛰고 다음 섹션으로 바로 이동하여 ADT에서 _ABAP Cloud Project_ 또는 _ABAP Project_를 생성하십시오.

<details>
  <summary>확장하려면 클릭하세요!</summary>
   
ABAP 환경은 많은 사람들이 사용하므로, 생성할 각 artefact가 다른 artefact와 충돌하지 않도록 명명 패턴을 정의했습니다.
  
이를 위해, 객체 이름에 사용된 placeholder **`###`** 를 찾을 수 있으며, 실습 중에 선택한 group ID로 대체해야 합니다.
  
group ID는 **최대 3개의 문자(숫자 및/또는 글자)**를 포함할 수 있습니다 - 예: `123`, `XY1`, 또는 `ABC`.

**Open ABAP Development Object** ![open_object_icon](images/adt_open_object.png)를 선택하거나 **Ctrl+Shift+A**를 눌러 **이미 사용된 group ID**를 확인할 수 있으며, 예를 들어 **`zrap100_*###`** (여기서 **`###`** 는 선택한 접미사)를 검색합니다. 해당 패턴에 맞는 모든 artefacts가 나열됩니다.

group ID **`000`** 의 사용은 **권장하지 않습니다**. 예를 들어, 본인의 이니셜 뒤에 숫자를 추가하여 다른 사람이 이 group ID를 이미 사용하고 있지 않은지 확인해 보십시오.

아래 스크린샷에서는 접미사 **`000`** 이 아직 사용 가능한지 확인하기 위해 검색 문자열로 **`zrap100_*000`** 을 입력하고 있습니다.

  ![determine group id](images/groupid01.png)
  <!-- <img src="images/groupid01.png" alt="determine group id" width="30%"> -->

_**No results**_는 이 group ID가 사용 가능하다는 것을 의미합니다. 이 ID를 어딘가에 group ID로 기록해 두고 다음 실습에서 사용할 수 있습니다.

사용 가능한 group ID를 찾았으면 **Cancel**을 선택합니다.

</details>

## ADT에서 _ABAP Cloud Project_ 또는 _ABAP Project_ 생성

> 이 단계에서는 ADT 설치에서 ABAP system에 대한 연결을 생성합니다. 이를 위해 _**ABAP Project**_ 또는 _**ABAP Cloud Project**_를 생성해야 합니다.
>
> SAP S/4HANA, on-prem 또는 private cloud edition에서 작업하는 경우 ADT에서 _**ABAP Project**_를 생성해야 합니다. 그렇지 않고 *SAP BTP ABAP Environment system* 또는 *SAP S/4HANA Cloud, public edition, system*에서 작업하는 경우 _**ABAP Cloud Project**_를 생성하십시오.
>
> ⚠ **참고:** ⚠
> ABAP Development Tools for Eclipse(ADT)에서 이미 *ABAP Cloud Project* 또는 *ABAP Project*를 생성한 경우 이 섹션을 건너뛰십시오.

### ADT에서 _ABAP Cloud Project_ 생성
[^Top of page](#)

> ADT 설치에서 _**ABAP Cloud Project**_를 생성하여 *SAP BTP ABAP Environment* 또는 *SAP S/4HANA Cloud (public edition)* system에 연결합니다.
>
> ⚠️ _SAP S/4HANA system, on-prem 또는 private cloud edition_(CAL instances 포함)에서 작업하는 경우 이 단계를 건너뛰십시오.

<details>
  <summary>확장하려면 클릭하세요!</summary>
   
1. 아직 열지 않았다면 **ABAP** perspective를 엽니다.

    ![Open ABAP Perspective](images/abap_perspective.png)

2. 이제 아래 제공된 스크린샷과 같이 _**ABAP Cloud Project**_를 생성합니다.

    ![Create ABAP Project Cloud 1/2](images/steampunk_systemlogon1.png)

    ![Create ABAP Project Cloud 2/2](images/steampunk_systemlogon2.png)

</details>

### ADT에서 _ABAP Project_ 생성
[^Top of page](#)

> ADT 설치에서 _**ABAP Project**_를 생성하여 CAL Instances를 포함한 *SAP S/4HANA system, on-prem 또는 private cloud edition* system에 연결합니다.
>
> ⚠️ _SAP BTP ABAP Environment_ 또는 _SAP S/4HANA Cloud, public edition_에서 작업하는 경우 이 단계를 건너뛰십시오.

<details>
  <summary>확장하려면 클릭하세요!</summary>
   
1. 아직 열지 않았다면 **ABAP** perspective를 엽니다.

    ![Open ABAP Perspective](images/abap_perspective.png)

2. 이제 아래 제공된 스크린샷과 같이 _**ABAP Project**_를 생성합니다.
  
  SAP 이벤트 팀에서 제공한 system information(SID, System IP, 및 Instance number)을 입력합니다.

   ![Create ABAP Project](images/adt_create_abapproject.png)

</details>

<!-- </details> -->

## 유용한 정보
[^Top of page](#)

> 이 섹션에는 실습에 유용한 정보가 포함되어 있습니다: _Find/Replace_ 기능, modern ABAP syntax, 그리고 유용한 ADT shortcuts.

<details>
  <summary>확장하려면 클릭하세요!</summary>
 
### Find/Replace

이 실습 과정에서 "_placeholder **`###`** 를 당신의 group ID로 바꾸시오_"라는 작업을 자주 보게 될 것입니다.

이를 위해 Eclipse Editor의 **Find/Replace** 기능을 사용하는 것이 좋습니다. 이 기능은 메뉴(**_Edit -> Find/Replace..._**) 또는 **Ctrl+F**를 통해 열 수 있습니다.
  
 ![find and replace](images/find01.png)
   
**Replace All**을 선택하면 **`###`** 의 모든 발생을 당신의 group ID로 바꿀 수 있습니다.

  
### Modern ABAP Syntax

여러 실습에서는 modern, declarative, 그리고 expression-oriented ABAP language syntax가 사용될 것입니다. 이를 통해 개발자들은 inline declarations, constructor expressions와 같은 새로운 언어 기능을 사용하여 더 간단하고 간결한 소스 코드를 작성할 수 있습니다.

> **ABAP Keyword Documentation에서 더 많은 정보 찾기**: [ABAP - Programming Language](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenabap_reference.htm)

  
### 유용한 ADT Shortcuts

다음은 Eclipse에서의 ABAP development를 위한 유용한 ADT keyboard shortcuts입니다.

![ADT Shortcuts](images/adt_shortcuts.png)

더 유용한 ADT shortcuts는 여기에서 찾을 수 있습니다: [Link](https://blogs.sap.com/2013/11/21/useful-keyboard-shortcuts-for-abap-in-eclipse/).

> **정보**: ADT에서 **Ctrl+Shift+L**을 눌러 **Show Key Assit**에서 사용 가능한 shortcuts의 전체 목록을 표시할 수 있습니다.
 
</details>


## 요약
[^Top of page](#)

다음 실습으로 계속 진행할 수 있습니다 - **[Exercise 1: Create Database Table and Generate UI Service](../ex01/README.md)**

---
