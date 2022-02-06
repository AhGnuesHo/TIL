외부에서 서비스에 접근하려면 **24시간 작동하는 서버**가 필요합니다. 

다음은 서브를 작동시키는 방법들입니다. 
> - 집에 PC를 24시간 구동시킨다
- 외부 호스팅업체 (카페24 등..)를 이용한다.
- 클라우드 서비스 (AWS..)를 이용한다. 

클라우드는 특정시간에 **트래픽이 몰리는 경우 유동적으로 사양을 늘릴 수 있기 때문에** 유리합니다. 
_____
### 클라우드란

클라우드 서비스는 인터넷(클라우드)를 이용해서 서버, 스토리지(파일저장소), 데이터베이스, 네트워크, 모니터링 등의 컴퓨팅 서비스를 제공하는 것을 말합니다. 

이런 클라우드에는 다음과 같은 형태들이 있습니다. 
>
** Infrastructure as a Service (IaaS) **  
  	- 기존 물리장비를 미들웨어와 함께 묶어둔 추상화 서비스입니다. 
  	- 가상머신, 스토리지, 네트워크, 운영체제 등의 IT인프라를 대여해주는 서비스라고 보면 됩니다. 
  	-  AWS의 EC2, S3등
>
** Platform as a Service (PaaS) **
 	- Iaas를 한번 더 추상화해 많은 기능을 자동화한 서비스입니다. 
 	- AWS의 빈스톡, 헤로쿠 등
>
** Software as a Service (SaaS) **
 	- 소프트웨어 서비스를 말합니다. 
 	- 구글 드라이브, 드랍박스, 와탭 등
    
AWS 서비스 중 IaaS를 사용해 서버 환경을 만들어보겠습니다. 
___
### EC2 인스턴스 생성하기
EC2는 AWS에서 제공하는 성능, 용량 등을 유동적으로 사용할 수 있는 서버입니다. 

_EC2는 사양이 t2.micro만 가능하며, 월 750시간의 제한(초과 시 비용청구)이 있습니다. _

>![](https://images.velog.io/images/nnoshel/post/7eefbe9c-dd23-4a94-87d8-b9e45e67c5c1/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.36.00.png)
지역을 다음과 같이 서울로 변경하겠습니다. (초기에는 오아이주로 선택되어있습니다.)

>검색창에서 ec2를 검색하고 EC2대시보드에서 인스턴스 시작 버튼을 누릅니다. 
![](https://images.velog.io/images/nnoshel/post/0811d1cc-58e0-4764-bfb0-964466dc3775/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.38.51.png)![](https://images.velog.io/images/nnoshel/post/cf9310b9-d86a-41e6-945f-06d99d67e046/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.39.07.png)

>인스턴스를 생성하기 위해서는 먼저 AMI를 선택해야합니다. 
AMI는 EC2인스턴스를 시작하는데 필요한 정보를 이미지로 만들어 둔 것을 이야기합니다.
>
즉, 인스턴스라는 가상머신에 운영체제등을 설치할 수 있게 구워넣은 이미지입니다.
![](https://images.velog.io/images/nnoshel/post/4bc2d617-ef51-4f0e-a912-84a9ded57ff0/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.40.14.png)Amazon Linux 2 AMI를 선택합니다. ![](https://images.velog.io/images/nnoshel/post/b29f2a47-51ec-4fd7-8675-6c38cb6ce6d9/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.47.57.png)위와 같이 프리티어로 표기된 t2.micro를 선택합니다.

>![](https://images.velog.io/images/nnoshel/post/1bf4d406-6db7-4122-a24f-6ee581a40193/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.53.01.png) 서버를 한대만 사용할 것이므로 별 다른 설정없이 다음 버튼을 눌러 넘어가겠습니다. 

>![](https://images.velog.io/images/nnoshel/post/df70b570-e7b3-40b3-b62b-d906046f4d11/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.55.53.png)스토리지는 흔히 말하는 서버의 용량입니다.
30GB까지 프리티어로 사용할 수 있기때문에 30GB로 변경하겠습니다.

>![](https://images.velog.io/images/nnoshel/post/22b42a07-690d-45c5-a991-e30ff8dcf8e4/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%206.59.38.png)

태그는 웹 콘솔에서 표기 될 태그인 Name을 등록하는 단계입니다. 
EC2의 이름을 붙인다고 생각하면 됩니다. 

>![](https://images.velog.io/images/nnoshel/post/bf7de6cd-e77e-4a4e-ae4a-5074d606c71f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.10.29.png)보안그룹은 방화벽을 말합니다. 
AWS에서는 **서버로 80포트 외에는 허용하지 않는 방화벽이 ** 보안그룹으로 사용됩니다. 
>
유형항목에서 SSH이면서 포트항목이 22인경우는 **AWS EC2에 터미널로 접속** 할 때를 이야기합니다. 
pem키가 없으면 접속이 안돼서 전체오픈 하는 경우 pem키가 실수로 노출되면 순간 서버에서 가상화폐가 채굴됩니다. 
>
보안을 위해서 pem키 관리와 **지정된 IP에서만 ssh 접속이 가능하도록** 구성해야합니다. 
그래서 안전한 곳(집..)의 IP를 기본으로 추가하는 것이 안전합니다. 
(내 IP를 선택하면 현재 접속한 곳의 IP가 자동입력됩니다. )

> ![](https://images.velog.io/images/nnoshel/post/73a0becd-64f6-465c-81ec-c4a3a1d60953/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.12.06.png)시작하기를 누르면 pem 키 ( 비밀키) 가 필요합니다. 
>
인스턴스는 지정된 pem키 와 매칭되는 공개키를 가지고 있어, 해당 pem키 이외에는 접근을 허용하지 않습니다. 
>
이 **마스터키**는 절대 유출되면 안됩니다.
>
이후 ec2 서버로 접속할 때 필요한 파일이므로 다운로드한 파일은 **잘 관리할 수 있는 디렉토리**에 저장해놓아야합니다. 
![](https://images.velog.io/images/nnoshel/post/eefe8595-338e-4d9a-a077-33209d2690d3/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.16.23.png)

>인스턴스 시작 버튼을 누르면 다음고 인스턴스 생성 시작 페이지로 이동합니다. 
![](https://images.velog.io/images/nnoshel/post/e3e3ddc3-0aa2-4d6e-8ea4-91f29a2e2ad0/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.20.09.png)
인스턴스 id를 선택해 EC2목록으로 이동합니다. 
![](https://images.velog.io/images/nnoshel/post/f0956cb7-f48c-4735-9178-2d29376039e9/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.25.27.png)생성이 다 되었다면 다음고 같이 IP와 도메인이 할당된 것을 확인할 수 있습니다. 
>
인스턴스도 결국  하나의 IP이기 때문에 IP가 존재합니다. 하지만 인스턴스를 중지하고 다시 시작할때마다 새 IP가 할당되기때문에 매번 변경되지않는 고정 IP를 가지게 해야합니다. 

_______

### EIP 할당
AWS의 고정 IPFMF Elastic IP라고합니다. 
>EC2 인스턴스 페이지의 왼쪽 카테고리에서 탄력적 IP를 선택하고 새주소 할당을 하겠습니다. 
![](https://images.velog.io/images/nnoshel/post/23416c74-db86-40d6-81f8-aa0307dad26f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.32.50.png)![](https://images.velog.io/images/nnoshel/post/92133d9a-2461-4988-991d-3567a0707cdc/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.33.40.png)
새 주소가 할당되었으면 다음과 같이 탄력적 IP가 발급된것을 확인 할 수 있습니다. 
탄력적 IP를 방금 생성한 EC2 주소와 연결하겠습니다. 
![](https://images.velog.io/images/nnoshel/post/fb212e1b-afbb-44fe-ac73-673d1783473c/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.34.46.png)
생성한 EC2 이름과 IP를 연결하고 연결 버튼을 누릅니다. 
![](https://images.velog.io/images/nnoshel/post/3c5b8bc3-40f7-4f98-9812-ac53292f2ff1/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%207.35.55.png)
>> EIP는 생성하고 즉시 EC2서버에 연결하지 않으면 비용이 발생합니다. 

_______
### EC2 서버에 접속하기
로컬 Pc에서 EC2 인스턴스로 접속해보겠습니다. 

다운로드 받은 pem 파일을 ~/.ssh/로 복사합니다. 
이 곳으로 파일을 옮겨놓으면 ssh 실행 시 pem 키 파일을 자동으로 읽어 접속을 진행 할 수 있습니다. 
> 
>>```cp {pem 키를 내려받은 위치} {~/.ssh/}```
>
![](https://images.velog.io/images/nnoshel/post/cbc07561-4c90-4ef7-904a-7b3665805b29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%208.13.42.png)![](https://images.velog.io/images/nnoshel/post/d498f59a-0cd3-4f80-95c0-b132517a0e6a/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%208.13.51.png)ls 명령어로 pem파일이 이동된 것을 확인할 수 있습니다. 

>
다음과 같이 pem 키의 권한을 변경합니다.  
>>``` chmod 600 ~/.ssh/{pem 키 이름}```
>
권한을 변경하고, config 파일을 생성하겠습니다.
>
![](https://images.velog.io/images/nnoshel/post/44a61992-cff1-491f-a760-7b369013b280/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%208.14.06.png)
편집 창이 열리면 다음과 같은 양식으로 작성하고 저장합니다. 
>> ```
Host 원하는 서비스 명
	HostName EIP주소
    User ec2-user
    IdentityFile ~/.ssh/{pem 키 이름}   
  ``` 
>
![](https://images.velog.io/images/nnoshel/post/530e6508-4d9a-4ed3-8847-98bf530b1516/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%208.16.58.png)

>
실행 권한을 부여해줍니다. 
>> ```chmod 700 ~/.ssh/config```
>
EC2에 접속합니다. 
>> ```ssh {config에 등록한 서비스명}```
>
![](https://images.velog.io/images/nnoshel/post/7ea0a3d2-6098-47fb-b99e-756f5bcacc7f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%208.18.38.png)
>> EC2 접속에 성공했습니다!!

_______

### 아마존 리눅스 서버 생성 시 설정

#### java8 설치
다음의 명령어를 사용해 java 8을 EC2에 설치하겠습니다. 
>sudo yum install -y java-1.8.0-openjdk-devel.x86_64

설치가 완료되었다면 인스턴스의 java 버전을 8로 변경하겠습니다. 
>  sudo /usr/sbin/alternatives --config java
>> ![](https://images.velog.io/images/nnoshel/post/09ea5fbf-6f05-484f-ab31-95bff2be24d3/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%208.58.10.png)
엔터키를 눌러서 java 8버전을 유지하겠습니다. ![](https://images.velog.io/images/nnoshel/post/6eb5de79-f0fa-4031-9585-af47dd5c93ba/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%209.00.04.png)
현재 버전이 java 8 버전이 된것을 확인 할 수 있습니다. 

_______
#### 타임존 변경

서버의 타임존을 한국시간으로 변경하겠습니다. 
>sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime

위 명령어를 차례대로 수행하면 date명령어로 타임존이 KST로 변경된 것을 확인할 수 있습니다. 
![](https://images.velog.io/images/nnoshel/post/20057574-1ffe-4d07-9385-04dca6349626/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%209.03.30.png)
_______
#### Hostname 변경

여러서버를 관리중인 경우 IP만으로도 어떤 서비스의 서버인지 확인이 어렵기때문에 각 서버가 어느 서비스인지 표현하기위해 HOSTNAME을 변경하겠습니다. 
> sudo vim /etc/sysconfig/network

위 명령어를 통해 편집창에서 HOSTNAME을 아래와 같이 변경하겠습니다. 
![](https://images.velog.io/images/nnoshel/post/3fc0e808-f8be-4f57-9f61-f83a8282f143/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-06%20%EC%98%A4%ED%9B%84%209.06.25.png)

> sudo reboot 

위 명령어로 서버를 재부팅하면 HOSTNAME이 변경된것을 확인 할 수 있습니다. 

