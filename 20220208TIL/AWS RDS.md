데이터베이스를 구축하고 EC2 서버와 연동을 해야합니다. 
다만 직접 데이터베이스를 설치하면 모니터링, 알람, 백업, HA 구성등을 모두 직접 해야하기 때문에 이 작업을 모두 지원하는 관리형 서비스인 RDS를 사용하겠습니다. 

> RDS는 AWS 에서 지원하는 클라우드 기반 관계형 데이터베이스입니다. 

____
 ### RDS 인스턴스 생성하기
 
 검색창에 rds를 검색하고 대시보드에서 데이터베이스 생성 버튼을 클릭합니다. 
 
 ![](https://images.velog.io/images/nnoshel/post/7c55e422-916e-4311-848a-371f3999dc42/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%208.53.10.png)![](https://images.velog.io/images/nnoshel/post/d7eec87c-072f-4641-bff0-48e8bde713d0/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%208.53.26.png)
 ____
 다음 화면에서 아래와 같이 MariaDB를 선택하고 무료로 사용하기위해 프리티어를 선택합니다.
 ![](https://images.velog.io/images/nnoshel/post/95973bf9-5f3c-4d13-bbb6-cdafcc0b222d/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.01.54.png)
 MariaDB는 MySQL의 창시자가 만든 프로젝트로 전반적인용방법이 MySQL과 비슷합니다. 
 ________
 상세 설정은 다음과 같이 하겠습니다. ![](https://images.velog.io/images/nnoshel/post/f15181ce-13f4-4663-b8e6-877f3b57c808/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.02.35.png)
 ![](https://images.velog.io/images/nnoshel/post/1eaf73c6-1d8c-4aa8-9760-6ce5bc51e2e8/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.04.20.png)
 인스턴스 이름과 사용자 정보는 실제 데이터 베이스에 접근하게 되므로 잘 기억할 만한 것을 해야합니다. 
 _______
 ![](https://images.velog.io/images/nnoshel/post/acfeb6d2-68d0-48bd-96ae-24f321e52ee0/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.04.46.png)
 네트워크 퍼블릭 엑세스를 **예**로 설정하고 이후에 보안그룹에서 지정된 IP만 접근 가능하도록 막겠습니다. 
 ____
 데이터 베이스 생성 버튼을 클릭하면 다음과 같이 생성 과정이 진행됩니다. 

 ![](https://images.velog.io/images/nnoshel/post/4f15ab52-4c14-4694-afe5-40ed4acf2e9e/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.12.26.png)
 
 ______
 ### RDS 운영에 맞는 파라메터 설정하기
 
 RDS를 처음 생성하면 다음 3개의 설정을 우선 진행해야합니다. 
 > - 타임존
 - Character Set
 - Max Connenction
 
 파라메터 그룹 탭을 클릭하면 파라메터 그룹 생성을 할 수 있습니다. 
 ![](https://images.velog.io/images/nnoshel/post/ebb8e5ec-120d-44d3-9329-c6ea072ad814/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.51.47.png)
 _____
 MariaDB의 버전과 같은 버전으로 그룹 패밀리를 선택합니다. 
 
 ![](https://images.velog.io/images/nnoshel/post/d82c88ae-773f-4a16-9752-f57d8be1f99f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.53.25.png)
 
 ___
 생성이 완료되면 다음과 같이 목록창에서 확인 할 수 있습니다. 
 해당 파라메터 그룹을 클릭합니다. 
 ![](https://images.velog.io/images/nnoshel/post/7a722188-25e3-4ca5-84fd-5872f097944d/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.53.47.png)
 ____
 파라메터 편집을 눌러서 편집 창에 들어갑니다. 
 
 #### 타임존 설정 
 time_zone을 검색해서 다음과 같이 Asia/Seoul을 선택합니다. 
 ![](https://images.velog.io/images/nnoshel/post/6bc59825-c7f6-45d2-abed-baf0e61c2ce2/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.54.25.png)![](https://images.velog.io/images/nnoshel/post/f89a2c38-8f84-410d-b040-d475b6191548/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%209.55.37.png)
____ 
 #### Character Set
 
 아래와 같이 변경하겠습니다. 
 
 utf8과 utf8mb4의 차이는 이모지 저장 가능 여부입니다. ![](https://images.velog.io/images/nnoshel/post/faa95ac0-f927-4f5a-af2c-2c7a0519729e/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.02.16.png)![](https://images.velog.io/images/nnoshel/post/b2074c1c-62f8-478c-a596-7f1278a27e16/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.03.23.png)
 ![](https://images.velog.io/images/nnoshel/post/cde980e1-702f-4df1-8ead-825f38c16ee0/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.03.17.png)
 ______
 #### Max Connection 

 ![](https://images.velog.io/images/nnoshel/post/e0b2f0b5-d4f6-4ffb-8613-4a6c42aed212/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.07.40.png)
 ____
 
이렇게 생성된 파라메터 그룹을 데이터 베이스에 연결하겠습니다. 

![](https://images.velog.io/images/nnoshel/post/e41125ea-56e6-4c68-9840-387b5d054188/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.10.28.png)![](https://images.velog.io/images/nnoshel/post/de9af8a9-86cc-4498-b331-995c92c66386/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.11.58.png)
_____
수정 버튼을 클릭하고, 파라메터 그룹을 방금 생성한 신규 파라메터 그룹으로 변경합니다. 
![](https://images.velog.io/images/nnoshel/post/2ce00a0c-0b1c-452e-b874-401bff50eb85/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-07%20%EC%98%A4%ED%9B%84%2010.13.29.png)
예약된 다음 유지 관리기간에 적용을 선택하면 새벽시간대에 수정이 진행됩니다. 
하지만 지금은 서비스가 오픈되기 전이므로 즉시 적용을 선택하겠습니다. 
____

### 내 PC에서 RDS에 접속해보기

RDS의 세부정보 페이지에서 보안그룹 인바운드 규칙에 ec2의 그룹 아이디와 내 IP를 추가합니다. 

![](https://images.velog.io/images/nnoshel/post/7349d34d-b11b-4338-b73e-4d46c0f323e0/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-08%20%EC%98%A4%ED%9B%84%206.21.02.png)
____

### Database 플러그인 설치


Database Navigator 플러그인을 설치하고 Database Browser를 실행하겠습니다. 
![](https://images.velog.io/images/nnoshel/post/086d46d2-9fef-49ae-a0a3-138971a0ecd4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-08%20%EC%98%A4%ED%9B%84%206.14.47.png)

위 사진의 초록색 \+ 버튼을 누르고 MySQL 접속 정보를 열어서 아래와 같이 접속 정보를 입력해줍니다. 

![](https://images.velog.io/images/nnoshel/post/fc91f847-915f-4dd8-85d7-4bf0f8112e36/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-08%20%EC%98%A4%ED%9B%84%206.17.24.png)
호스트에는 RDS의 엔드포인트 정보를 복사해 붙여넣으면 됩니다. 

데이터베이스가 연결됩니다. 
