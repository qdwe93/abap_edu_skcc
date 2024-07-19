# abap_edu_skcc
Abap 교육용 저장소

1. ABAP 개요
(1) SAP 과 ABAP 소개
(2) ABAP 프로그래밍 환경 - ABAP Workbench (SE80)
W1. 첫번째 프로그램 만들어보기
- 사용요소: report, write
Hello, World!! 찍어보기
(3) Report with Subroutine
W2. 서브루틴을 활용하여 인자를 받아들여 결과 출력하기 
- 사용요소: perform, write
Hello, 홍길동!! 찍어보기
(4) Function - Z_MY_FUNCTION
W3. 이름을 받으면 이름의 글자수를 반환하는 RFC 만들어보기
- 사용요소: Function (importing/exporting)
(5) Flow Logic (Do, While, If, Loop, Case)
W4. 날짜를 입력받아 오늘 날짜와의 차이일수(n) 를 계산하여
홀수이면 n만큼 *를 화면에 찍어주기
- 사용요소: Do/While, If, write
W5. 1, 2, 3 중 하나의 값이 있는 데이터 테이블을 
1은 교통비, 2는 숙박비, 3은 식대 로 바꿔주기
- 사용요소: Internal Table, VALUE #(), Loop, Case, CL_DEMO_OUTPUT
(6) 궁금할때 찾아볼만한 곳
- abapdocu(F1), sample program packages, help.sap.com, google 


2. ABAP 데이터 핸들링
(1) ABAP 에서각 모듈별로 많이쓰는 테이블, 시스템 변수(SY)
- FI, CO, MM, SD, 공통
(2) ABAP 프로그래밍 환경 - Debugging
W6. W5 디버깅 해보기
- 사용요소: /H, SY, SY-TABIX
(3) Selection Screen - Selection-Option, Parameter
W7. 연도, 법인, 날짜를 입력받아 write문으로 보여주기
- 사용요소: Selection-Option, Internal Table, CL_DEMO_OUTPUT
(4) Open SQL
W8. 연도, 법인, 날짜를 입력받아 해당일의 BKPF 전표 목록을 보여주기
- 사용요소: Selection-Option, OepnSQL, Internal Table, CL_SALV_TABLE
W9. 연도, 법인, 날짜를 입력받아 해당일의 BKPF-BSEG 을 조인하여 전표 목록을 보여주기
- 사용요소: Selection-Option, OepnSQL, Internal Table, CL_SALV_TABLE
(5) Internal Table
- 인터널 테이블 생성방법, 헤더라인, LOOP 를 활용한 인터널 테이블 제어
W10. 연도, 법인, 날짜를 입력받아 해당일의 BKPF-BSEG 을 조인하여 전표 목록을 보여주기
ITAB 은 Select 전 미리 선언
금액이 원화가 아닐 경우 원화로 환산하여 보여주기 (달러=1500, 엔=900, 위안=250)
- 사용요소: Selection-Option, OepnSQL (Join), Internal Table 선언, APPEND, READ, CL_SALV_TABLE
W11. Collect, Delete Adjacent 활용.
(6) String Processing
W12. 연도, 법인, 날짜를 입력받아 해당일의 BKPF-BSEG 을 조인하여 전표 목록을 보여주기
ITAB 은 Select 전 미리 선언
금액이 원화가 아닐 경우 원화로 환산하여 보여주기 (달러=1500, 엔=900, 위안=250)
OpenSQL 에서 받을때 S는 차변, H는 대변으로 표시
SKAT 에서 계정명을 받아와서 LOOP 를 돌며 계정에 대한 이름을 ITAB 에 입력
- 사용요소: Selection-Option, OepnSQL (Join), Internal Table, For all entries in, CL_SALV_TABLE


3. ALV (ABAP List Viewer)
(1) ABAP List version history
(2) ALV 프로그램 만들어보기
- 사용요소: CL_GUI_ALV_GRID, Field Catalog
(3) ALV 속성 변경
- 사용요소: Layout, Toolbar
(4) ALV 이벤트 이해
- 사용요소: PBO, PAI, Event Handler

4. Abap 화면구성
(1) Screen Painter - Component 와 화면 구성
스크린 페인터 소개, 화면 꾸며보기, 데이터 연결해보기, 화면 부르기
(2) 구성된 화면에 데이터 출력
(3) Status, Title, PBO, PAI
(4) ABAP Container (CL_GUI_CONTAINER) 활용


