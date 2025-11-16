[Home - ABAP RESTful Application Programming Model (RAP)에 대한 워크샵](https://github.com/SAP-samples/abap-platform-rap-workshops/blob/main/README.md)

[![REUSE status](https://api.reuse.software/badge/github.com/SAP-samples/abap-platform-rap100)](https://api.reuse.software/info/github.com/SAP-samples/abap-platform-rap100)

<!--
# SAP-samples/repository-template
이 SAP Samples repository의 기본 템플릿에는 README, LICENSE, .reuse/dep5 파일이 포함되어 있습니다. github.com/SAP-samples의 모든 repository는 이 템플릿을 기반으로 생성됩니다.

# 포함된 파일

1. LICENSE 파일:
대부분의 경우 SAP 샘플 프로젝트의 라이선스는 `Apache 2.0`입니다.

2. .reuse/dep5 파일:
샘플 프로젝트에는 [Reuse Tool](https://reuse.software/)을 사용해야 합니다. .reuse/dep5 파일은 프로젝트 초기에 찾을 수 있습니다. 홑화살괄호 < > 안의 내용을 여러분의 repository에 맞는 특정 정보로 교체해 주십시오.

3. README.md 파일 (이 파일):
이 파일은 프로젝트의 주요 설명 파일이므로 편집해 주십시오. 아래에서 섹션에 대한 몇 가지 자리 표시자 제목을 찾을 수 있습니다.
-->

# RAP100 - ABAP RESTful Application Programming Model (RAP)로 Fiori 앱 구축하기
<!-- 설명적인 제목을 포함해 주세요 -->

<!--- repository를 등록한 후(https://api.reuse.software/register), REUSE 배지를 추가하세요:
[![REUSE status](https://api.reuse.software/badge/github.com/SAP-samples/REPO-NAME)](https://api.reuse.software/info/github.com/SAP-samples/REPO-NAME)
-->

## 설명
<!-- SEO 친화적인 설명을 포함해 주세요 -->

이 repository에는 **RAP100 - ABAP RESTful Application Programming Model (RAP)로 Fiori 앱 구축하기** 핸즈온 세션을 위한 자료가 포함되어 있습니다.

**목차**
- [이 워크샵 참여를 위한 요구사항](#requirements-for-attending-this-workshop)
- [개요](#overview)
- [연습문제](#exercises)
- [녹화 영상](#recordings)
- [솔루션 패키지](#solution-package)
- [알려진 이슈](#known-issues)
- [지원받는 방법](#how-to-obtain-support)
- [ABAP RESTful Application Programming Model (RAP) 소개](#about-the-abap-restful-application-programming-model)
- [추가 정보](#further-information)

## 📋이 워크샵 참여를 위한 요구사항
[^페이지 상단으로](#)

> 이 워크샵의 실습 예제를 완료하려면, 여러분의 노트북이나 PC에 최신 버전의 ABAP Development Tools for Eclipse (ADT)가 설치되어 있어야 하며, 적합한 ABAP 시스템(예: SAP BTP ABAP Environment, SAP S/4HANA Cloud Public Edition, 또는 최소 2022 릴리스의 SAP S/4HANA Cloud Private Edition 및 SAP S/4HANA)에 접근할 수 있어야 합니다.
>
> [ABAP Flight Reference Scenario](https://github.com/SAP-samples/abap-platform-refscen-flight)는 관련 시스템(예: SAP BTP ABAP Environment Trial)에 import 되어 있어야 합니다.

<details>
  <summary>클릭하여 펼치기!</summary>

이 repository의 연습문제를 따라하기 위한 요구사항은 다음과 같습니다:
1. [최신 Eclipse 플랫폼과 최신 ABAP Development Tools (ADT) 플러그인 설치하기](https://developers.sap.com/tutorials/abap-install-adt.html)
2. [SAP BTP ABAP Environment Trial에서 사용자 생성하기](https://developers.sap.com/tutorials/abap-environment-trial-onboarding.html) (_아래 예외사항 참조_)
3. [ABAP Cloud Project 생성하기](https://developers.sap.com/tutorials/abap-environment-create-abap-cloud-project.html)
4. [ADT 설치 환경에서 웹 브라우저 설정 조정하기](https://github.com/SAP-samples/abap-platform-rap-workshops/blob/main/requirements_rap_workshops.md#4-adapt-the-web-browser-settings-in-your-adt-installation)

>> ⚠ **"ABAP Developer Day" 및 "SAP CodeJam"과 같은 SAP 주도 행사에 관한 예외사항**:
>> → 핸즈온 워크샵 참가자들을 위한 전용 ABAP 시스템이 제공될 것입니다.
>> → 워크샵을 위한 시스템 접속 정보는 세션 중에 강사가 제공할 것입니다.
>>
</details>

## 🔎개요

<!-- #### 현재 비즈니스 시나리오 -->
> 이 워크샵은 RAP의 기본에 관한 모든 것을 다룹니다; 특히 greenfield 구현을 구축할 때 RAP 핵심 기능을 사용하는 방법에 중점을 둡니다.

<details>
  <summary>클릭하여 펼치기!</summary>
  
이 핸즈온 세션에서는 semantic key와 내부 unmanaged early numbering을 사용하는 _managed_ business object (BO) 런타임 구현을 통해, RAP를 사용하여 SAP Fiori elements 기반의 _Travel Processing App_의 OData 서비스를 개발하는 과정을 안내합니다. 각 연습문제에서 시나리오에 대한 더 자세한 내용을 제공할 것입니다.

결과물 앱은 다음과 같이 보일 것입니다:

![Travel App](exercises/images/rap100_travelapp01.png)

여러분이 구현하게 될 OData 서비스는 _ABAP Flight Reference Scenario_를 기반으로 합니다. 비즈니스 컨텍스트를 설정하자면, 시나리오는 다음과 같습니다: 여러 에이전시의 전 세계 여행을 관리하는 부서에서 Travels를 처리(즉, 생성, 수정, 삭제)하기 위한 draft 기능이 있는 새로운 Fiori 앱을 구축해달라고 여러분에게 요청했습니다.

아래는 앱의 기반이 되는 단순화된 데이터 모델입니다.

![Travel App](exercises/images/rap100_datamodel01.png)

> **참고**:
> 각 연습문제의 목적은 다양한 RAP 핵심 기능을 구현하는 방법을 보여주는 것이며, 완벽한 비즈니스 시나리오를 만드는 것에는 덜 중점을 둡니다.
> 구현의 복잡성을 일부 제거하기 위해, 우리는 _Travel_ 엔티티라는 단 하나의 BO 노드만 있는 매우 단순화된 데이터 모델을 사용할 것입니다.
> 하나 이상의 BO 노드가 있는 구현 예제는 다음을 참조할 수 있습니다:
> - RAP Developer Guide@SAP Help Portal: **[Develop](https://help.sap.com/docs/abap-cloud/abap-rap/develop?version=sap_btp)** > **[Develop Applications](https://help.sap.com/docs/abap-cloud/abap-rap/develop-applications?version=sap_btp)**
> - 워크샵 **[RAP110](https://github.com/SAP-samples/abap-platform-rap110)**

</details>


## 🛠연습문제
[^페이지 상단으로](#)

RAP를 사용하여 처음부터 트랜잭션 처리가 가능하고 draft 기능이 활성화된 Fiori elements List Report 앱을 개발하기 위해, draft가 활성화된 RAP Business Object (BO) 위에 OData 서비스를 구축하는 다음 단계를 따르세요. 또한 이에 대한 ABAP unit test를 작성하고 Entity Manipulation Language (EML)를 탐색하게 됩니다.

#### 초급 레벨

<!--
> ⚠️ _Getting Started_ 연습문제는 건너뛰고 바로 [연습문제 1](exercises/ex01/README.md)부터 시작하세요.
-->

| 연습문제 | -- |
| ------------- |  -- |
| [시작하기 (Getting Started)](exercises/ex0/README.md) | -- |
| [연습문제 1: 데이터베이스 테이블 생성 및 UI 서비스 생성](exercises/ex01/README.md) | -- |
| [연습문제 2: BO 데이터 모델 향상 및 OData Streams 활성화](exercises/ex02/README.md) | -- |
| [연습문제 3: BO 기능 개선 – Early Numbering](exercises/ex03/README.md) | -- |
| [연습문제 4: BO 기능 개선 – Determinations](exercises/ex04/README.md) | -- |
| [연습문제 5: BO 기능 개선 – Validations](exercises/ex05/README.md) | -- |

#### 중급 레벨
아래 연습문제들은 _초급 레벨_ 섹션의 연습문제 1부터 5까지를 기반으로 합니다.

| 연습문제 | -- |
| ------------- |  -- |
| [연습문제 6: BO 기능 개선 – Actions](exercises/ex06/README.md) | -- |
| [연습문제 7: BO 기능 개선 – Dynamic Feature Control](exercises/ex07/README.md) | -- |
| [연습문제 8: RAP BO를 위한 ABAP Unit Test 작성](exercises/ex08/README.md) | -- |
| [연습문제 9: EML을 이용한 RAP BO 외부 API 기반 접근](exercises/ex09/README.md) | -- |

#### 추가 연습문제:
다음 연습문제들은 _초급_ 및 _중급 레벨_ 섹션의 연습문제 1-9를 기반으로 합니다.
그러나, 만약 ADT 미리보기 대신 실제 SAP Fiori elements 기반의 _Travel_ 앱에서 다음 연습문제들의 결과를 직접 테스트하고 싶다면, _연습문제 1_을 마친 후 바로 주어진 순서대로 이 연습문제들을 수행할 수 있습니다.

| 연습문제 | -- |
| ------------- |  -- |
| [연습문제 10: SAP Fiori elements 앱 생성 및 SAP Business Application Studio를 사용하여 SAP BTP, ABAP Environment에 배포하기](https://developers.sap.com/tutorials/abap-environment-deploy-fiori-elements-ui.html) | -- |
| [연습문제 11: SAP Fiori 앱을 ABAP Fiori Launchpad에 통합하기](https://developers.sap.com/tutorials/abap-environment-integrate-app-into-flp.html) (_라이선스 필요_) | -- |
| [연습문제 12: SAP Fiori Launchpad Space 및 Page 템플릿 생성하기](https://developers.sap.com/tutorials/abap-environment-create-spaces-pages-template.html) (_라이선스 필요_) | -- |

## 🔁녹화 영상
[^페이지 상단으로](#)

2022년 SAP TechEd에서 열린 RAP 가상 워크샵의 다시보기를 시청하세요. RAP에 대한 간결한 소개와 연습문제 1부터 7까지의 데모가 포함되어 있습니다.

📹 <a href="http://www.youtube.com/watch?feature=player_embedded&v=BNoUYkizM30" target="_blank">ABAP RESTful Application Programming Model로 앱 구축 및 확장하기</a>

## 📤솔루션 패키지

> 솔루션 패키지 **`ZRAP100_SOL`**을 여러분의 시스템에 import할 수 있습니다*.
>
> (*) 지원되는 ABAP 시스템은 SAP BTP ABAP Environment, SAP S/4HANA Cloud Public Edition, 또는 최소 2022 릴리스의 SAP S/4HANA Cloud Private Edition 및 SAP S/4HANA입니다.
> [ABAP Flight Reference Scenario](https://github.com/SAP-samples/abap-platform-refscen-flight)는 솔루션 패키지를 import하기 전에 시스템에서 사용 가능해야 합니다.

<details>
<summary>클릭하여 펼치기!</summary>
  
솔루션을 import하려면 다음 지침을 따르세요:

1. 아직 설치하지 않았다면, [abapGit 플러그인을 여러분의 ABAP Development Tools (ADT) for Eclipse에 설치](https://developers.sap.com/tutorials/abap-install-abapgit-plugin.html)하세요.
2. ADT에서, 여러분의 시스템에 ABAP 패키지 **`ZRAP100_SOL`**을 생성하세요.
3. ADT에서 **abapGit Repositories** 뷰를 열고 아래 단계를 따르세요.
4. **Link abapGit Repository** 창을 사용하여 repository에 대한 링크를 생성하세요.
    📤 Git repository URL: `https://github.com/SAP-samples/abap-platform-rap100`
5. 이제 컨텍스트 메뉴 _**Pull...**_을 사용하여 솔루션 구현을 pull/import 하세요.
6. import된 개발 객체들을 활성화하세요 (**Ctrl+Shift+F3**).

이 사용법 튜토리얼도 확인할 수 있습니다: [abapGit을 사용하여 SAP BTP ABAP environment에서 GitHub repository로 ABAP 소스 코드 push하기](https://developers.sap.com/tutorials/abap-environment-abapgit-transfer..html).

</details>

## ⚠알려진 이슈
알려진 이슈 없음.

## 🆘지원받는 방법
버그를 발견하거나 내용에 대해 질문이 있는 경우 이 repository에 [issue를 생성](../../issues)하세요.

추가 지원이 필요한 경우, [SAP Community에 질문하기](https://answers.sap.com/questions/ask.html).

## ABAP RESTful Application Programming Model 소개
[^페이지 상단으로](#)

> **ABAP RESTful Application Programming Model (RAP)**은 트랜잭션 시나리오를 구현하기 위한 ABAP Cloud 개발 모델(ABAP Cloud)의 핵심입니다.
>
> **ABAP Cloud**는 클라우드와 온프레미스 모두에서 모든 SAP S/4HANA 에디션 및 SAP Business Technology Platform (SAP BTP)에서 clean core 확장성 원칙을 준수하는 혁신적이고 클라우드에 적합한 비즈니스 앱, 서비스, 확장을 구축하기 위한 새로운 ABAP 개발 모델입니다.

<details>
<summary>클릭하여 펼치기!</summary>

_ABAP RESTful Application Programming Model_ (RAP)은 SAP Fiori, SAP HANA, 그리고 클라우드를 활용하여 ABAP 기반 SAP 솔루션의 사용자 경험을 개선하고 비즈니스 프로세스를 혁신하는 것을 가능하게 합니다.

RAP는 클라우드 및 온프레미스 환경에서 클라우드에 적합한 트랜잭션 처리용 SAP Fiori 앱, 웹 API, 로컬 API를 효율적으로 구축하기 위한 개념, 도구, 언어, 그리고 강력한 프레임워크의 집합을 제공합니다. 이는 SAP의 주력 제품인 SAP S/4HANA (클라우드 및 온프레미스 1909 릴리스부터) 및 SAP BTP ABAP Environment에서의 ABAP 개발을 위한 장기적인 전략적 솔루션입니다.

아래 그림은 RAP로 작업할 때의 상위 수준의 end-to-end 개발 스택을 보여줍니다.

![RAP Big Picture](exercises/images/rap_bigpicture.png)

> **더 알아보기**: [Modern ABAP Development with the ABAP RESTful Application Programming Model (RAP)](https://community.sap.com/topics/abap/rap)

</details>

## 추가 정보
[^페이지 상단으로](#)

ABAP RESTful Application Programming Model (RAP)에 대한 추가 정보는 여기에서 찾을 수 있습니다:
 - [State-of-the-Art ABAP Development with RAP](https://community.sap.com/topics/abap/rap) | SAP Community 페이지
 - [Modernization with RAP](https://blogs.sap.com/2021/10/18/modernization-with-rap/) (_블로그 게시물_)
 - 가장 자주 묻는 질문: [ABAP Cloud FAQ](https://community.sap.com/topics/abap/abap-cloud-faq) | [RAP FAQ](https://blogs.sap.com/2020/10/16/abap-restful-application-programming-model-faq/)
 - [RAP100 Tutorials Mission on SAP Developers Center](https://developers.sap.com/mission.sap-fiori-abap-rap100.html) | SAP Developers' Center
 - [SAP Fiori elements Feature Showcase App with RAP and ABAP CDS](https://blogs.sap.com/2022/12/19/the-sap-fiori-elements-feature-showcase-with-rap-and-abap-cds-annotations/) | GitHub.com
 - [RAP 및 embedded analytics를 포함한 ABAP Cloud에 대한 다양한 핸즈온 워크샵 자료가 있는 랜딩 페이지](https://github.com/SAP-samples/abap-platform-rap-workshops/blob/main/README.md)

<!--
## 기여하기
코드에 기여하거나, 수정 또는 개선 사항을 제안하고 싶다면 pull request를 보내주세요. 법적인 이유로, 기여자는 이 프로젝트에 첫 pull request를 생성할 때 DCO에 동의하도록 요청받게 됩니다. 이는 제출 과정에서 자동화된 방식으로 이루어집니다. SAP는 [Linux Foundation의 표준 DCO 텍스트](https://developercertificate.org/)를 사용합니다.
-->

## 라이선스
Copyright (c) 2024 SAP SE or an SAP affiliate company. All rights reserved. 이 프로젝트는 [LICENSE](LICENSE) 파일에 별도로 명시된 경우를 제외하고 Apache Software License, 버전 2.0에 따라 라이선스가 부여됩니다.
