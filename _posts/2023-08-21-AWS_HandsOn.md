---
layout: post
title: AWS 기반 아키텍쳐 설계해보기 
tags:
  - AWS
---

이번에 회사에서 AWS Hands-On 강의를 수강할 수 있는 기회가 생겨 금요일 오후에 강의를 듣고 왔다. 

사실 우리팀에서 나 말고도 참여를 원했던 팀원들이 있었는데 정원이 초과돼서 조금 일찍 신청한 나만 수강하게 되었다. 그래서 강의에서 배운 내용을 팀원들에게 공유하기로 해서 그 내용을 정리해 보았다. 

실습 위주의 강의였기 때문에 수업 중간에 실습 내용을 정리할 틈은 없었다. 그래서 강의를 들은 바로 다음날부터 기억을 복기하며 내가 소화한 내용들을 정리했다. 

간단한 실습같아 보여도 네트워크 개념에 대한 전반적인 이해가 필요해서 글을 쓰다보니 완성까지 꼬박 3일이 걸렸다. 시간은 오래 걸렸지만 정리하면서 그동안 배웠던 네트워크 지식도 복습할 수 있었고, 몰랐던 내용도 많이 알게된 것 같다. 

<br>

# Index

1. [IAM (Identity and Access Management)](#1-iam-identity-and-access-management)

2. [네트워크 기본](#2-네트워크-기본)

   - [CIDR(Classless Inter-Domain Routing)](#cidr-classless-inter-domain-routing)

   - [Private IP](#private-ip)

   - [NAT(Network Address Translation)](#nat-network-address-translation)

3. [핸즈온 아키텍쳐](#3-핸즈온-아키텍쳐)

4. [VPC (Virtual Private Cloud) 설정](#4-vpc-virtual-private-cloud-설정)

5. [인터넷 게이트웨이와 NAT 게이트웨이에 대해서](#5-인터넷-게이트웨이와-nat-게이트웨이에-대해서)

6. [EC2 (Amazon Elastic Compute Cloud) 설정](#6-ec2-amazon-elastic-compute-cloud-설정)

7. [RDS (Relational Database Service) 설정](#7-rds-relational-database-service-설정)

8. [마치며](#8-마치며)

<br>

### 1. IAM (Identity and Access Management)

---

AWS IAM 서비스는 그룹별 또는 사용자별로 AWS 리소스에 대한 접근/제어 권한을 부여하고 관리할 수 있는 서비스이다. 기본적으로 AWS 계정을 생성한 후 이메일과 비밀번호로 로그인하여 액세스 하는 계정은 루트 계정이라고 부른다. 이 루트 계정은 모든 AWS 서비스 및 리소스에 대해 완전한 액세스 권한을 가진다. AWS에서는 보안을 위해 루트 계정을 사용하지 않을 것을 강력히 권장한다고 한다. 

아래의 이미지는 AWS 공식 홈페이지에서 제공해주는 IAM 사용 모범 사례이다. 맨 위 계정 (Account)이 루트 계정이고, 그 아래는 IAM 서비스를 사용하여 관리하는 그룹과 사용자 계정이다. Bob, Nate, Cathy 사용자는 각각 Admin, Developers, Test 라는 그룹에 속한다. 그리고 IAM 서비스를 사용하여 그룹별로 개별적인 리소스 접근 또는 제어 권한을 부여한다. 

<div><img src="https://github.com/AmyJJung/blog/blob/main/images/aws_image/aws-iam.png?raw=true" alt="IAM 모범 사례" style="zoom:100%;" />
<p>[이미지 출처 : AWS 사용자 가이드 문서]</p>
</div>


예를 들어 Test 그룹에는 디비 리소스인 RDS에 대해 읽기 권한만 부여해서 리소스를 좀 더 안전하게 관리할 수 있다. 만약 이렇게 사용자 또는 그룹별로 권한을 부여하지 않고 모든 사용자가 루트계정 (Account)을 공유해서 사용한다고 생각해보자. 루트 계정은 모든 리소스에 대한 완전한 액세스 권한을 가지기 때문에 테스트 그룹 사용자가 실수로 디비 리소스에 접근해서 데이터를 날려버리는 등의 실수를 할 수도 있을 것이다. 

추가적으로 IAM에서는 멀티 팩터 인증(MFA) 기능도 제공하는데, MFA(Multi-Factor Authentication)는 다중인증이라는 뜻으로 로그인 할 때 최소 2가지 이상의 인증을 받게 함으로써 보안을 강화하는 방식이다. AWS에서 제공하는 MFA 는 여러 방식이 있는데 보통 구글OTP를 사용해서 인증하는 방식을 많이 사용한다. 

- 참고 : [AWS IAM 실습 따라하기](https://ukayzm.github.io/aws-create-iam-user/)

<br>

<br>

### 2. 네트워크 기본

---

실습을 진행하기 전에 자주 사용되는 네트워크 관련 개념들을 확인하고 넘어가자. 

<br>

- #### **CIDR (Classless Inter-Domain Routing)**

  - Classless Inter-Domain Routing(CIDR)은 이름 그대로 클래스가 없는 도메인간 라우팅 기법을 의미한다. CIDR와 대비되는 개념이 클래스 기반 주소 할당 방식이다. 클래스 기반 주소 할당 방식에서는 클래스별로 network prefix의 bit수를 고정시키기 때문에, 고정된 수의 호스트를 사용할 수 밖에 없었다. 이런 단점을 보완하기 위해 나온게 CIDR이다. CIDR를 사용하면 IP주소를 좀 더 유연하게 사용할 수 있다. 

  - 표기법  :  `A.B.C.D/E `
    - A,B,C,D : 각각 8비트로 이루어짐. 0~255까지의 10진수로 표현 
    - E : 0~32까지의 10진수 (왼쪽부터 E개의 비트가 Network Prefix를 나타낸다.) 


  - 예시

    <figure><table>
    <thead>
      <tr><th>IPv4 CIDR</th><th>Subnet Mask</th><th>Network ID</th><th>Host</th><th>IP Range</th></tr></thead>
      <tbody><tr><td>192.168.0.0/24</td><td>255.255.255.0</td><td>192.168.0.0</td><td>2^8 (256개)  </td><td>192.168.0.0 ~ 192.168.0.255</td></tr></tbody>
    </table></figure>


    - IP 주소범위의 가장 처음은 네트워크 주소(192.168.0.0), 가장 끝은 브로드캐스트 주소(192.168.0.255)로 사용하기 때문에 실제로 사용할 수 있는 IP 주소는 192.168.0.1 ~ 192.168.0.254 까지 총 254개 이다. 단, AWS에서는 관리 목적으로 3개의 IP주소를 (192.168.0.1 ~ 192.168.0.3) 추가적으로 예약하고 사용하기 때문에 256개의 IP에서 총 5개를 제외한 251개의 IP를 사용할 수 있다. 
    
    - E의 숫자를 더 크게 하거나 더 작게 조절해서 IP를 적절하게 할당할 수 있다. 
      - E의 숫자를 더 크게하면 (= 서브넷 마스크를 오른쪽으로 움직이면) 하나의 네트워크를 더 작은 호스트 갯수를 갖는 여러개의 네트워크 대역으로 쪼갤 수 있다 => 서브네팅(Subnetting)
      - E의 숫자를 더 작게하면 (= 서브넷 마스크를 왼쪽으로 움직이면) 여러개의 네트워크 대역을 합쳐서 더 큰 호스트 갯수를 갖는 네트워크 대역을 만들수 있다 => 슈퍼네팅(Supernetting)

<br>


- #### **Private IP**

  - 한정된 IP주소를 최대한 활용하기 위해 만든 개념이다. IP주소는 IPv4 (32bit로 IP주소 표현) 기준으로 약 43억개(2^32) 이다. 전세계 인구에게 IP 한개만 할당해도 이미 IP주소는 고갈되었을 것이다. 그래서 이 문제를 해결하기 위해 내부 네트워크에서만 유니크한 Private IP를 사용하고, 외부로 통신할 때는 전세계 네트워크 상에서 유니크한 Public IP를 사용한다. 

  - AWS에서 Subnet을 구성하고 Private IP 대역을 정할 때 [RFC1918](https://www.getoutsidedoor.com/2021/03/30/rfc1918-%EC%A0%95%EB%A6%AC-address-allocation-for-private-internets/)에서 규정한 Private IP 대역을 사용하도록 권고한다. 왜냐하면, 네트워크 내부에서 사용하는 Private IP 주소가 외부 네트워크에 존재하는 Public IP와 충돌하면 패킷이 외부로 나가지 못하고 네트워크 내부로 라우팅이 될 수 있기 때문이다.

    | RFC 1918 범위                 | CIDR표기       | CIDR 블록의 예 |
    | :---------------------------- | :------------- | -------------- |
    | 10.0.0.0 - 10.255.255.255     | 10.0.0.0/8     | 10.0.0.0/16    |
    | 172.16.0.0 - 172.31.255.255   | 172.16.0.0/12  | 172.31.0.0/16  |
    | 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 | 192.168.0.0/20 |

<br>

- #### **NAT (Network Address Translation)**

  - 위에서 말했듯이 Private IP가 외부 네트워크와 통신하기 위해서는 Public IP가 필요한데, Private IP 주소를 Public IP 주소로 변환해주는 방법이 NAT이다. NAT의 종류로는 Dynamic NAT,  Static NAT, PAT(Port Address Translation) 이 있다. 
    - **Dynamic NAT**
      - Public IP Pool 에서 현재 사용중이지 않은 Public IP를 가져와서 Private IP에 연결해주는 방식이다.
    - **Static NAT**
      - Public IP 1개와 Private IP 1개를 1대1로 연결해주는 방식으로, Private IP 갯수만큼 Public IP가 필요하기 때문에 위에서 말한 Public IP를 아껴쓰기 위한 목적으로는 의미가 없다. (뒤에서 언급하겠지만 AWS Internet Gateway가 이 방식을 사용한다.)
    - **PAT(Port Address Translation)**
      - Private IP 여러개를 Public IP 1개와 연결해주는 방식이다. 예를 들어 내부 네트워크에서 Source IP 와 Source Port 번호가 적힌 패킷을 외부 네트워크로 전송하려고 하면 NAT이 이 Source IP를 Public IP로, Source Port 번호를 특정 포트 번호로 변환해서 패킷을 전송한다. Source Port 번호를 특정 포트번호로 변환하는 이유는 무엇일까? 예를 들어 내부 네트워크에서 10.10.10.1:80 애플리케이션과 10.10.10.2:80 애플리케이션 모두 외부 네트워크로 요청을 보낸다고 가정해보자. 두 패킷 모두 동일한 포트번호를 가지는데, 만약 하나의 Public IP와 기존 포트번호로 변환되어 외부로 나가게 된다면, 나중에 응답이 Public IP까지는 찾아와도 사설망 내부에 어떤 호스트에게 응답을 줘야하는지 구분할 수가 없다. 따라서 Public IP와 임의의 포트번호로 매핑하고 이 정보를 기록해서 요청을 보내고 응답을 받을 수 있다. (뒤에서 언급하겠지만 AWS의 NAT Gateway가 이 방식을 사용한다. )

<br>

<br>

### 3. 핸즈온 아키텍쳐

---

![handson-architecture](https://github.com/AmyJJung/blog/blob/main/images/aws_image/aws.png?raw=true)

위 그림은 이번 실습에서 구성해 볼 네트워크 아키텍쳐이다. 하나의 VPC(Virtual Private Cloud) 내부에 4개의 Subnet이 존재한다. 2개의 Subnet은 Public Subnet 이고, 나머지 2개는 Private Subnet 이다. 여기서 Public Subnet 이란, 라우팅 테이블에 인터넷 게이트웨이로 향하는 규칙이 존재하는 Subnet을 의미한다. Private Subnet 이란, 라우팅 테이블에 인터넷 게이트웨이로 향하는 규칙이 존재하지 않는 Subnet을 의미한다. 우리는 Public Subnet에 EC2(웹서버)를 위치시켜서 인터넷 게이트웨이를 통해 외부에서 들어오는 요청을 받기도 하고 응답을 내보내기도 하도록 할것이다. 반면 데이터는 보안이 중요한 자원이기 때문에 Private Subnet에 RDS(데이터베이스 인스턴스)를 위치시킬 예정이다. 

그렇다면 Private Subnet에 위치한 인스턴스들은 인터넷과 통신할 수 있는 방법이 아예 없는 것일까? Private Subnet 안에 존재하는 인스턴스들도 소프트웨어 업데이트 등의 이유로 외부 네트워크와의 통신이 필요한 경우가 있다. 이런 경우에는 NAT (Network Address Translation)을 사용한다. 

AWS에서 제공하는 서비스인 NAT 게이트웨이와 인터넷 게이트웨이에 대한 설명은 뒤에서 살펴볼 것이다. 이제 직접 VPC를 구성하는 실습을 진행해보자. 

<br><br>

### 4. VPC (Virtual Private Cloud) 설정

---

VPC란 AWS에서 제공하는 가상의 프라이빗 네트워크로, 외부와 격리 되어있는 가상의 네트워크 단위 또는 가상의 데이터 센터라고 볼 수 있다. VPC는 기본적으로 외부에서 접근이 불가능하다. 외부와 통신하기 위해서는 인터넷 게이트웨이 등의 서비스를 사용해야 한다. 

AWS 계정을 생성하면 디폴트 VPC를 제공해준다. 우리는 이번 실습에서 이 기본 VPC를 사용하지 않고 새로운 VPC를 생성해 보자. 

<br>

- #### **VPC 생성**

![vpc-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-01.png?raw=true)

AWS 콘솔 상단 검색창에 VPC를 검색 > VPC 생성버튼 클릭

<br><br>

![vpc-02](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-02.png?raw=true)

VPC를 생성하면서 서브넷, NAT 게이트웨이도 함께 생성할 것이기 때문에 `VPC 등`을 선택한다. 이름태그는 맘대로 적어주자. IPv4 CIDR 블록은 핸즈온 아키텍쳐 이미지에 나와있는 대로 `10.0.0.0/16` 으로 설정한다. 이렇게 할 경우 앞에서부터 16비트는 네트워크 아이디이고 나머지 16비트로 호스트 주소를 표현할 수 있다. 따라서 이 네트워크는 2^16(65,536)개의 아이피 주소를 사용할 수 있다. (위에서 언급했지만 네트워크주소, 브로드캐트주소, AWS가 관리용으로 예약한 주소 5개를 제외하면 실제로 사용할 수 있는 주소는 65536 - 5 = 65531개 이다.) 

CIDR 블록을 구성할 때 유의할 점이 있는데, AWS는 원래 규정된 사설 IP 범위와는 다르게  /16 ~ /28 비트의 서브넷 마스크만 허용한다고 한다. 즉, AWS에서 VPC를 생성할 수 있는 가장 큰 IP 대역은 /16이며 , 가장 작은 대역은 /28이다. 

<br><br>

![vpc-03](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-03.png?raw=true)

가용역역을 설정하는 부분이다. **가용영역(Availability Zone)** 이란, 각 **리전(Region, 인프라를 지리적 구분한 개념)** 내에 격리된 개별 데이터 센터 위치이다. 우리가 사용하는 서울 리전에는 총 4개의 가용영역(ap-northeast-2a, ap-northeast-2b, ap-northeast-2c, ap-northeast-2d)이 존재한다. 각각의 가용역역은 같은 리전에 속해있어도 물리적으로 구분되어있기 때문에 천재지변등의 영향으로부터 데이터를 보호할 수 있다. 

이번 실습에서는 핸즈온 아키텍쳐 이미지에서 볼 수 있듯이 2개의 가용영역(ap-northeast-2a, ap-northeast-2c) 을 구성한다. 

<br><br>![vpc-04](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-04.png?raw=true)

다음으로는 퍼블릭 서브넷, 프라이빗 서브넷을 구성해보자. 각각의 가용역역에 퍼블릿 서브넷 1개 프라이빗 서브넷 1개씩 구성한다. 

<br><br>![vpc-05](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-05.png?raw=true)

NAT 게이트웨이는 위에서 언급한것처럼 프라이빗 서브넷 내부에서 인터넷과 통신할 필요가 있을 경우를 대비해 생성한다. NAT 게이트웨이 서비스는 비용이 비싸기 때문에 1개의 가용영역 안에 설치한다. 실습이 끝난 뒤에는 꼭 자원을 삭제해줘야 한다!!

<br><br>

이제 모두 잘 생성되었는지 확인해보자!

![vpc-06](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-06.png?raw=true)VPC 생성완료.

<br>

![vpc-07](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-07.png?raw=true)

서브넷 생성완료.

<br>

![vpc-08](https://github.com/AmyJJung/blog/blob/main/images/aws_image/vpc-08.png?raw=true)

NAT 게이트웨이 생성완료. NAT 게이트웨이에는 기본 퍼블릭 Ipv4 주소가 할당되어 있는걸 볼 수 있다. 그 이유는 뒤에서 설명한다. 

<br><br>

VPC, 서브넷, NAT 게이트웨이 모두 제대로 생성된걸 확인할 수 있다. 다음 단계로 넘어가기 전에 4개의 서브넷에 연결된 라우팅 테이블을 확인해자. 라우팅 테이블을 제대로 설정해줘야 다른 호스트들과 통신할 수 있기 때문이다.

<br>

![subnet-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/subnet-01.png?raw=true)

먼저 `ap-northeast-2c` 가용영역 안에 구성한 퍼블릭 서브넷이다. 라우팅 테이블을 보면 2개의 규칙이 있다. 

 `10.0.0.0/16` 은 우리가 정한 VPC CIDR 블록으로 VPC 내부에 존재하는 호스트를 의미한다. 목적지가 `10.0.0.0/16`인 패킷이 올 경우 `local` 즉, VPC 내부로 포워딩 하라는 의미이다. 두번째 규칙은 `0.0.0.0/0` 으로  `10.0.0.0/16` 에 속하지 않는 모든 목적지를 의미하는데 이 경우에는 `igw-088f3f78997e5e613`(인터넷 게이트웨이)로 포워딩 하라는 의미이다. 즉, 패킷의 목적지가 VPC 내부에 있는 Private IP 가 아닌 경우에는 인터넷 게이트웨이로 보내서 외부와 통신하도록 라우팅 테이블을 작성한 것이다. 

<br><br>

![subnet-03](https://github.com/AmyJJung/blog/blob/main/images/aws_image/subnet-03.png?raw=true)

다음은 `ap-northeast-2c` 가용영역 안에 구성한 프라이빗 서브넷이다. 라우팅 테이블을 보면 2개의 규칙이 있다. 

퍼블릭 서브넷과 마찬가지로 목적지가 `10.0.0.0/16`인 패킷이 올 경우 `local` 즉, VPC 내부로 포워딩 하라는 의미이다. 하지만 퍼블릭 서브넷과는 다른점이 있다. 목적지가 `0.0.0.0/0` 으로  `10.0.0.0/16` 에 속하지 않는 모든 패킷은 `nat-069d8218efb13837` (NAT게이트웨이)로 포워딩 하도록 한다.

`ap-northeast-2a` 내부의 프라이빗 서브넷과 퍼블릭 서브넷도 똑같은 규칙을 가진다. 

<br>

<br>

### 5. 인터넷 게이트웨이와 NAT 게이트웨이에 대해서

---

다음 단계로 넘어가기 전에 NAT게이트웨이와 인터넷 게이트웨이에 대해 그리고 퍼블릭 서브넷과 프라이빗 서브넷 안에 있는 인스턴스들이 외부 네트워크과 통신하는 과정에 대해 알아보자. 

<br>

####  **인터넷 게이트웨이**

<div align="center"><img src="https://github.com/AmyJJung/blog/blob/main/images/aws_image/internet-gateway.png?raw=true" alt="internet-gateway" style="zoom:100%;" />
<p>[이미지 출처 : https://jesuisjavert.github.io/2021/02/15/aws-study9/]</p>
</div>

VPC 퍼블릭 서브넷 안에 위치한 EC2 인스턴스(private ip : `10.0.0.10` , public ip : `50.1.1.10`)에서 외부 웹서버(`60.1.1.1` )로 요청을 보내는 과정을 살펴보자. (퍼블릭 서브넷의 라우팅 테이블에는 인터넷 게이트웨이로 가는 규칙이 있다.)

(1) 패킷을 확인하면 SourceIP는 EC2 인스턴스의 private ip(`10.0.0.10`) 이고 DestinationIP는 `60.1.1.1` 이다. 

(2) 이 패킷은 인터넷 게이트웨이로 포워딩 된다. 인터넷 게이트웨이는 VPC에서 인터넷으로 나가는 통로이면서 NAT(Network Address Translation)의 역할도 수행한다. 이 단계에서 NAT(Static NAT, 1:1 매핑)을 실행해서 SourceIP를 EC2 인스턴스의 탄력적IP(public ip)인 (`50.1.1.10`)으로 변환해서 포워딩한다. 

(3) 보낸 요청에 대한 응답 패킷을 보면 DestinationIP는 EC2 인스턴스의 public ip 주소 (`50.1.1.10`) 으로 적혀있다.

(4) 위 요청에 대한 응답이 내부로 들어올 때 인터넷 게이트웨이에서 다시 NAT을 실행해서 DestinationIP를  private ip 주소인 `10.0.0.10`으로 변환한다. 

위 과정에서 프라이빗 아이피와 퍼블릭 아이피가 1:1로 매핑되어 변환되는 과정을 확인할 수 있다. 결국 퍼블릭 서브넷 내부에 있는 인스턴스가 인터넷과 통신하기 위해서는 퍼블릭 아이피 주소를 할당받아야 한다. 

<br><br>

#### **NAT(Network Address Translation) 게이트웨이**

<div align="center"><img src="https://github.com/AmyJJung/blog/blob/main/images/aws_image/nat-gateway.png?raw=true" alt="NAT-gateway" style="zoom:100%;" />
<p>[이미지 출처 : https://jesuisjavert.github.io/2021/02/15/aws-study9/]</p>
</div>

프라이빗 서브넷은 퍼블릭 서브넷과 다르게 인터넷 게이트웨이로 향하는 라우팅 규칙이 없기 때문에 외부 네트워크와 통신할 수 없다. 하지만 NAT게이트웨이를 사용하면 프라이빗 서브넷 내부에 있는 인스턴스도 인터넷과 통신할 수 있다. 그렇다면, NAT 게이트웨이와 인터넷 게이트웨이의 차이점이 뭘까?? 인터넷 게이트웨이는 위에서 살펴봤듯이 인스턴스의 public ip 주소와 private ip 주소를 1:1로 NAT(Network Address Translation)하기 때문에 외부 네트워크에서 이 서브넷 내부에 있는 인스턴스로 직접 접근할 수 있다. 하지만, NAT 게이트웨이에서는 프라이빗 네트워크에 존재하는 여러 인스턴스들 (public ip 주소를 할당받지 않아도 된다.)의 private ip를 NAT 게이트웨이의 public ip로 변환하고 4계층 헤더의 포트 번호도 변환해서 패킷을 외부로 보낸다(Port Address Translation). 따라서 프라이빗 서브넷 내부에서 요청 패킷이 먼저 나가면 이 요청에 대한 응답은 받을 수 있지만, 외부에서 먼저 내부로 요청을 보낼 수 없기 때문에 보안 이슈를 해결할 수 있다. 

위 이미지를 보면서 VPC 프라이빗 서브넷 안에 위치한 EC2 인스턴스(private ip : `10.40.2.101` , public ip : x )에서 외부 웹서버(`60.1.1.1` )로 요청을 보내는 과정을 살펴보자. (프라이빗 서브넷의 라우팅 테이블에는 NAT 게이트웨이로 가는 규칙이 있다.)

(1) 패킷을 확인하면 SourceIP는 EC2 인스턴스의 private ip(`10.40.2.101`) 이고, SourcePort는 40001이다. 그리고 DestinationIP는 `60.1.1.1` 이고 DestinationPort는 80이다. 

(2) 이 패킷은 NAT 게이트웨이로 포워딩 된다. 이미지에서 확인할 수 있듯이 NAT 게이트웨이는 private ip (`10.40.1.100`) 뿐 아니라 public ip (`a.b.c.d`)를 가지고 있다. 이 과정에서 (1)의 SourceIP를 NAT 게이트웨이의 private ip (`10.40.1.100`) 로 변환했고, SourcePort는 그대로 40001을 사용했다. (Source Port는 이 과정에서 변환될 수 도 있다) 그리고 이 매핑 정보를 기록한다. 

(3) 이제 이 패킷은 인터넷 게이트웨이로 포워딩 된다. 인터넷 게이트웨이는 VPC에서 인터넷으로 나가는 통로이면서 NAT(Network Address Translation)의 역할도 수행한다. 이 단계에서 NAT(Static NAT, 1:1 매핑)을 실행해서 SourceIP를 NAT 게이트웨이의 public ip (`a.b.c.d`)로 변환해서 포워딩한다. 

(4) 보낸 요청에 대한 응답 패킷을 보면 DestinationIP는 NAT 게이트웨이의 public ip 주소 (`a.b.c.d`) 로 적혀있다.

(5) 위 요청에 대한 응답이 인터넷 게이트웨이를 통해 내부로 들어올 때 다시 NAT을 실행해서 DestinationIP를 `a.b.c.d`의 private ip 주소인 `10.40.1.100`으로 변환한다. 

(6) 그리고 이 패킷은 마지막으로 NAT 게이트웨이를 통해 들어오면서 아까 기록된 매핑 정보를 바탕으로 DestinationIP와 DestinationPort 변환이 실행된다. (`10.40.1.100`, `40001`) -> (`10.40.2.101`, `40001`) 

(7) 최종적으로 요청을 보냈던 인스턴스로 응답이 완료된다.

<br>

<br>

### 6. EC2 (Amazon Elastic Compute Cloud) 설정

---

이제 VPC 내부에 웹서버를 만들어보자! 이번에는 AWS 제공하는 EC2 인스턴스를 생성할 것이다. EC2란 AWS에서 제공하는 클라우드 컴퓨팅 서비스로, AWS로부터 컴퓨터를 임대하는 것이라고 볼 수 있다. 

<br>

![ec2-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-01.png?raw=true)

AWS 콘솔 상단 검색창에 EC2를 검색 > 인스턴스 > 인스턴스 시작 클릭

<br><br>

![ec2-02](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-02.png?raw=true)

먼저 인스턴스의 이름을 적어준다. 다음으로는 OS 이미지를 선택하면 되는데, 이번 실습에서는 실제로 서비스를 배포하진 않고 인프라 구성만 실습해보기 때문에 기본적으로 선택되어 있는 Amazon Linux 이미지를 선택했다. 꼭 프리티어(*지정된 한도 내에서 무료로 AWS 서비스를 사용해 볼 수 있도록 해주는 서비스) 사용 가능한 이미지를 선택하자.

<br><br>

![ec2-03](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-03.png?raw=true)

인스턴스 유형도 프리티어 사용 가능한 t2.micro로 선택하고 넘어간다. 다음으로는 키페어 생성버튼을 클릭한다. 키 페어는 인스턴트에 접속하기 위한 키로, 이 키 파일이 있어야만 EC2 인스턴스에 접근할 수 있다. 잃어버릴 경우 재발급이 불가능해 새로운 EC2 인스턴스를 생성해야 할 수 있으니 잘 관리해야 한다. 또 외부로 노출되지 않도록 주의하자. 

<br><br>

![ec2-04](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-04.png?raw=true)

키페어 이름을 작성한 후 키페어 생성 버튼을 클릭한다. 

<br>

<br>

![ec2-05](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-05.png?raw=true)

나는 aws-study라는 폴더를 생성해서 키페어 파일을 저장했다. 나중에 SSH로 EC2 인스턴스에 접근할 때 이 경로에서 접근하면 된다. 

<br><br>

![ec2-06](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-06.png?raw=true)

다음으로는 네트워크를 설정해보자. 3번 핸즈온 아키텍쳐 이미지에서 확인할 수 있듯이 우리의 웹서버(EC2)는 위에서 생성한 VPC 내부의 퍼블릭 서브넷 안에 구성할 것이다. 따라서 VPC는 `my-vpc-01-vpc`를, 서브넷은 `ap-northeast-2c` 가용영역 안에 있는 퍼블릭 서브넷을 선택하자. 

그리고 퍼블릭 IP 자동 할당을 활성화 시킨다. 이 웹서버는 외부 네트워크와 통신해야하기 때문에 퍼블릭 IP를 할당해야 한다. 퍼블릭 IP 자동 할당 기능을 활성화 시킬 경우 EC2 서버에 Public IP 주소가 자동으로 할당된다. 하지만 이 IP 주소는 고정 IP가 아니라서 EC2 인스턴스를 재시작하게 되면 IP 주소가 바뀐다. 그렇기 때문에 실제 서비스로 사용하기 위해서는 고정 IP 할당이 필요하다. 이 때 사용하는 서비스가 **탄력적 IP(Elastic IP, 유료 서비스)** 이다. 

<br><br>

![ec2-07](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-07.png?raw=true)

다음은 방화벽 설정이다. AWS에서는 인스턴스별로 개별적인 방화벽을 설정할 수 있는데 이때 사용하는 서비스가 **보안그룹(Security Group)** 이다. 기존 보안그룹을 사용하지 않고 새로 생성해보자. 보안그룹 이름이나 설명은 자유롭게 작성한다. 다음으로는 인바운드 보안그룹 규칙을 추가해주자. 먼저 SSH로 인스턴스에 접근할 것이기 때문에 유형에서 SSH를 클릭한다. SSH를 선택하면 자동으로 TCP 프로토콜과 22번 포트로 설정이 된다. 소스 유형은 내 IP에서만 SSH로 접속할 것이기 때문에 내 IP를 클릭한다. 

<br><br>

![ec2-08](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-08.png?raw=true)

다음으로는 HTTP(80)와 HTTPS(443) 포트를 열어준다. 소스유형은 외부에서 어떤 IP가진 클라이언트가 접속할지 모르기 때문에 위치무관 (0.0.0.0/0) 으로 설정한다. 

<br><br>

![ec2-09](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-09.png?raw=true)

나의 경우 스토리지 구성이나 고급 세부 정보는 기본 설정에서 건드리지 않고 넘어갔다. 이제 인스턴스를 시작해보자!

<br><br>

![ec2-10](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-10.png?raw=true)

위 이미지는 생성된 EC2 인스턴스의 정보이다. 눈여겨볼만한 정보는 먼저 외부와 통신할 수 있도록 퍼블릭 IPv4 주소가 할당되었다는 것이다. 그리고 서브넷 아이디를 보면 `ap-northeast-2c` 가용역역 안에 구성한 퍼블릭 서브넷 안에 이 인스턴스가 생성되었음을 알 수 있다. 아까 각각의 서브넷의 CIDR블록을 설정했던 부분을 확인해보면, 이 서브넷의 CIDR 블록은 `10.0.2.0/24` 이다. 이는 이 서브넷의 네트워크 주소는 `10.0.2.0` 이고 `10.0.2.4 ~ 10.0.2.254`  범위의 IP를 가질 수 있음을 의미한다. 이 인스턴스의 프라이빗 IPv4 주소는 `10.0.2.238` 로 이 인스턴스가 ap-northeast-2c 가용영역 안에 있는 퍼블릭 서브넷에 속한 호스트임을 확인할 수 있다. 

<br><br>

![ec2-11](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-11.png?raw=true)

마지막으로 우리가 생성한 EC2 인스턴스의 인바운드, 아웃바운드 규칙을 확인해보자. 인바운드 규칙에는 위에서 설정했듯이 HTTP(80), HTTPS(443), SSH(22) 포트가 열려있다. 아웃바운드 규칙은 직접 생성하진 않았지만 기본적으로 모든 아웃바운드 트래픽을 허용하도록 설정되어 있다. 이제 SSH로 인스턴스에 접근해보자!

<br><br>

![ec2-12](https://github.com/AmyJJung/blog/blob/main/images/aws_image/ec2-12.png?raw=true)

먼저 아까 EC2 인스턴스를 생성한 키페어가 있는 위치로 이동한다. 나의 경우 aws-study라는 경로 안에 이 키페어를 저장했다. 

`ssh -i {발급받은키} {인스턴스 유저}@{인스턴스 퍼블릭 IP 주소}` 또는

`ssh -i {발급받은키} {인스턴스 유저}@{인스턴스 퍼블릭 DNS}` 명령어로 인스턴스에 접속한다. (Amazon Linux 이미지의 기본 유저명은 ec2-user이다.)

<I>여담이지만 강의장에서 SSH로 EC2 인스턴스에 접근하는 실습을 진행했었는데, 22번 포트를 열었음에도 접속이 되지 않아서 조금 당황했었다. 나만 설정을 잘못한 줄 알았는데 알고보니 강의장의 방화벽 아웃바운드 규칙에 22번 포트가 열려있지 않아서 발생한 문제였다. ^^ </I>

EC2 설정은 여기서 끝이다. 이제 데이터베이스를 생성하러 가보자~

<br>

<br>

### 7. RDS (Relational Database Service) 설정

---

AWS RDS는 AWS에서 제공하는 관계형 데이터베이스 서비스이다. 이번 실습에서는 RDS 인스턴스를 프라이빗 서브넷 안에 구성해서 외부에서 직접적으로 접근하지 못하도록 구성할 예정이다. DB인스턴스를 생성하기 전에 먼저 DB 서브넷 그룹을 생성해보자

<br>

![rds-08](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-08.png?raw=true)

AWS 콘솔 상단 검색창에 RDS를 검색  > 서브넷 그룹 클릭

서브넷 그룹의 이름과 설명을 적어준다. 아까 위에서 생성한 VPC내부에 있는 서브넷 그룹에 데이터베이스 인스턴스를 생성할 것이기 때문에 VPC는 `my-vpc-01` 을 선택한다. 

<br><br>

![rds-09](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-09.png?raw=true)

다음으로는 RDS 인스턴스를 위치시킬 가용영역과 서브넷을 선택한다. 가용영역은  `ap-northeast-2a`, `ap-northeast-2c`를 선택하고, 서브넷은 2개의 프라이빗 서브넷 `10.0.1.0/24`와 `10.0.3.0/24`를 선택한다. 

<br><br>

![rds-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-01.png?raw=true)

이제 RDS 서비스로 데이터베이스를 생성해보자. AWS 콘솔 상단 검색창에 RDS를 검색 > 데이터베이스 생성 버튼 클릭

<br>

<br>

![rds-02](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-02.png?raw=true)

원하는 데이터베이스를 선택한다. 나는 MySQL을 선택했다. 

<br><br>

![rds-03](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-03.png?raw=true)

데이터베이스 인스턴스는 프리티어를 생성하지 않고 개발/테스트로 생성할 것이다. 그 이유는 개발/테스트나 프로덕션용 인스턴스는 프리티어와는 다르게 **다중 AZ DB 인스턴스** 기능을 제공하기 때문이다. 이 서비스는 기본 DB 인스턴스 외에도 다른 가용영역에 예비 DB 인스턴스를 생성해서 데이터 이중화를 제공한다. 이 서비스는 무료가 아니기 때문에 실습이 끝나고 바로 삭제하자! 

<br><br>

![rds-04](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-04.png?raw=true)

DB 인스턴스 식별자와 마스터 사용자 이름, 암호를 작성한다.

<br><br>

![rds-05](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-05.png?raw=true)

![rds-06](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-06.png?raw=true)

스토리지 설정 부분에서 할당된 스토리지는 20으로 수정하자.

<br><br>

![rds-07](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-07.png?raw=true)

다음으로는 연결 정보를 설정하자. 컴퓨팅 리소스는 일단 연결 안함으로 체크하고 넘어간다. VPC는 위에서 생성한 `my-vpc-01` 을 선택하고, DB 서브넷 그룹은 방금 전에 생성한 `my-database-subnet-group` 을 선택한다. 프라이빗 서브넷 안에 생성한 데이터베이스 인스턴스는 외부에서는 직접적으로 접근하지 못하도록 구성할 것이기 때문에 퍼블릭 액세스는 아니오를 선택한다. 

<br><br>

![rds-10](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-10.png?raw=true)

VPC 보안 그룹은 새로 생성한다. 뒤에서 이 보안그룹의 인바운드 규칙을 설정해서 웹서버의 보안그룹 (MyWebServer SecurityGroup) 내부에 있는 인스턴스들만 접근할 수 있도록 설정할 것이다. 

데이터 베이스 인증은 암호인증을 선택한다. 

<br><br>

![rds-11](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-11.png?raw=true)

나의 경우 디비 인스턴스를 직접 사용하지는 않고 인프라만 구축해본 후 인스턴스를 삭제할 예정이기 때문에 나머지 설정은 건드리지 않고 데이터베이스 생성 버튼을 클릭했다. 실제로 서비스하기 위해 인스턴스를 생성하는 경우에는 필요한 기능을 생각해서 추가적인 설정을 하면 될 것 같다.

<br><br>

이제 추가적인 설정을 해주자. 

![rds-12](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-12.png?raw=true)

AWS 콘솔 상단 검색창에 RDS 검색한 후 데이터베이스를 클릭하면 방금 생성한 인스턴스를 확인할 수 있다. 내가 생성한 `my-database` 인스턴스를 클릭해보자. 

<br><br>

![rds-13](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-13.png?raw=true)

내가 생성한 디비 인스턴스에 대한 상세 정보를 확인할 수 있다. 연결&보안 탭에서 내가 생성한 `MyDatabase-SecurityGroup` 을 클릭해서 인바운드 규칙을 추가해주자.

<br><br>

![rds-14](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-14.png?raw=true)

인바운드 규칙 탭 클릭 > 인바운드 규칙 편집 클릭

<br><br>

![rds-15](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-15.png?raw=true)

그리고 유형에서 `MYSQL/Aurora` 를 선택해서 3306번 포트를 열어준다. 이 때 소스는 사용자 지정을 선택하고 웹서버 인스턴스의 보안그룹 `MyWebServer-SecurityGroup` 을 선택해준다. 이렇게 개별적인 인스턴스가 아니라 보안그룹으로 인바운드 규칙을 설정하면 추후에 `MyWebServer-SecurityGroup`에 새로운 웹서버 인스턴스를 생성하더라도 인바운드 규칙을 추가할 필요가 없기 때문에 관리하기 편하다. 

<br><br>

이제 우리가 생성한 EC2 서버에서 데이터베이스로 접근이 되는지 확인해보자.

![rds-16](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-16.png?raw=true)

먼저 EC2 웹서버로 들어가서 `mysql -u {UserName} -p -h {Host Name}` 명령어를 입력한다. {UserName}은 아까 RDS 인스턴스를 생성할 때 입력한 마스터 사용자 이름이고, {Host Name} 은 RDS 인스턴스를 생성하면 만들어지는 EndPoint 값이다. `mysql>` 이라는 글자가 나타나면 연결에 성공한 것이다. 

<br><br>

마지막으로, 아까 RDS 인스턴스를 생성할 때 **다중 AZ(Availability Zone) DB 인스턴스** 기능을 활성화 했는데, 정말로 이중화가 되어있는지 확인해보자.

<br>

![nslookup-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/nslookup-01.png?raw=true)

먼저 EC2 인스턴스에서 `nslookup` 명령어를 사용해서 현재 디비 인스턴스의 주소를 확인해보자. 처음 검색했을 때는 `Address: 10.0.3.87`로 우리가 위에서 만든 4개의 서브넷 중에서 `ap-northeast-2c` 에 있는 private subnet 내부에 디비 인스턴스가 생성되었음을 확인할 수 있다. 이제 가상으로 이 인스턴스에 장애가 발생한 것 같은 상황을 만들어보자.

<br><br>

![rds-reboot-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-reboot-01.png?raw=true)

위에서 생성한 `my-dtabase` 인스턴스를 선택하고 작업 > 재부팅을 클릭해보자. 마치 장애 때문에 master 인스턴스가 죽는 시나리오를 재부팅을 통해 진행할 수 있다. 

<br><br>

![rds-reboot-02](https://github.com/AmyJJung/blog/blob/main/images/aws_image/rds-reboot-02.png?raw=true)

<I>장애 조치로 재부팅하시겠습니까?</I> 라는 질문에 체크한 후 확인 버튼을 누른다. 이제 멀티 AZ 환경에서 master 데이터베이스 인스턴스가 죽을 경우 fail over 가 되는지 확인해보자.

<br><br>

![nslookup-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/nslookup-02.png?raw=true)

아까처럼 다시 ec2 인스턴스에서 `nslookup`을 입력해보자. 이번에는 아까와 다르게 `Address: 10.0.1.64`로 되어있음을 확인할 수 있다. 즉 장애가 발생하기 전에는  `ap-northeast-2c` 에 있는 private subnet 내부 인스턴스가 master로 동작했다면, 지금은 `ap-northeast-2a`의 프라이빗 서브넷 안에 있는 인스턴스로 대체된 것이다. 

<br>

<br>

### 8. 마치며

---

드디어! 실습이 끝났다. 이제 오늘 실습한 내용을 정리하고 마무리해보자~

먼저 아래 이미지는 지난번 자바교육 팀 프로젝트를 진행할 때 AWS로 데이터베이스를 생성한 적이 있었는데, 그때 네트워크를 구성한 모습이다. ([AWS로 프로젝트 서버를 구축해보자](https://amyjjung.github.io/blog/Project01/))

<div>
<img src="https://github.com/AmyJJung/blog/blob/main/images/aws_image/aws-publicsubnet.png?raw=true" alt="퍼블릭DB" style="zoom:100%;" />
<p>[이미지 출처 : AWS 사용자 가이드 문서]</p>
</div>

이 때는 팀원들 모두 자신의 개발 환경에서 자유롭게 데이터베이스 인스턴스에 접근할 수 있도록 인스턴스를 퍼블릭 서브넷 안에 위치시키고 퍼블릭 아이피를 할당했었다. 이렇게 네트워크를 구성할 경우 개발 단계에서는 편리할 수 있지만, 보안에 취약하다는 단점이 있다. 따라서 프로덕션 환경에서는 오늘 실습한 아키텍쳐와 같이 프라이빗 서브넷과 퍼블릭 서브넷을 따로 구성해서 보안을 강화하자. 

<br>

지금까지 우리가 구성한 아키텍쳐를 이미지를 보며 오늘 실습한 내용을 정리해보자! 

![aws](https://github.com/AmyJJung/blog/blob/main/images/aws_image/aws.png?raw=true)

먼저 프라이빗 네트워크인 **VPC** 를 생성했다. 그리고 이 VPC의 네트워크 대역을 쪼개서 2개의 **퍼블릭 서브넷**과 2개의 **프라이빗 서브넷**을 구성했다. 하나의 퍼블릭 서브넷에는 서비스를 배포할 **EC2** 서버를 생성했고, 다른 하나의 퍼블릭 서브넷에는 프라이빗 서브넷의 인터넷 통신을 위해 **NAT 게이트웨이**를 생성했다. 이 2개의 퍼블릭 서브넷은 인터넷 세상으로 나갈 수 있도록 라우팅 테이블에 **인터넷 게이트웨이** 규칙을 추가해주었다. 프라이빗 서브넷 안에는 **RDS** 인스턴스를 생성했다.  **다중 AZ(Availability Zone) DB 인스턴스** 서비스를 사용해서 디비를 이중화했다. 그리고 EC2 인스턴스의 **보안그룹** (방화벽)을 생성했는데, 외부로부터의 HTTP 또는 HTTPS 요청을 허용하도록 인바운드 규칙에 80번, 443번 포트를 추가했다. 그리고 내 아이피에서 SSH클라이언트로 EC2 서버에 접속할 수 있도록 22번 포트도 인바운드 규칙에 추가했다. RDS 디비 인스턴스에도 **보안그룹** (방화벽)을 생성했는데, 웹서버 인스턴스들의 보안그룹 자체 `MyWebServer-SecurityGroup` 로 부터 들어오는 3306 포트를 열어 주었다. 이렇게 개별적인 인스턴스가 아니라 보안그룹으로 인바운드 규칙을 설정하면 추후에 `MyWebServer-SecurityGroup`에 새로운 웹서버 인스턴스를 생성하더라도 인바운드 규칙을 추가할 필요가 없기 때문에 관리하기 편하다. 


<br>

진짜 진짜 마지막으로 ! 실습에서 생성한 서비스(EC2, RDS, NAT 게이트웨이, 탄력적 IP, Security Group, VPC, Subnet  등의 서비스) 들은 사용하지 않는다면 꼭 삭제해서 불필요한 과금을 막도록하자. 제대로 삭제하지 않으면 대참사가 발생할 수도 있다. ^^

한가지 추천하는 방법으로는 AWS 계정을 생성하면 해당계정으로 설문조사에 참여할 경우 크레딧을 준다는 메일이 많이 오는데, 이런 설문에 참여해서 크레딧을 쏠쏠하게 적립해보자. 

<br>

![credit-01](https://github.com/AmyJJung/blog/blob/main/images/aws_image/credit-01.png?raw=true)

실제로 위 이미지와 같은 메일이 생각보다 자주 온다. AWS는 돈이 많기 때문에 설문만 참여해도 몇주 뒤에 진짜 크레딧을 보내준다. 

<br>

![credit-02](https://github.com/AmyJJung/blog/blob/main/images/aws_image/credit-02.png?raw=true)

이건 내가 실제로 받은 크레딧 리스트이다. 크레딧으로 결제할 수 없는 서비스도 있다. 그래도 있는게 더 든든하다 ㅎㅎ 

<br>

<br>

----

- References

  - [CIDR이란 무엇인가요?](https://aws.amazon.com/ko/what-is/cidr/?nc1=h_ls)

  - [IP Address에 대한 이해](https://sudo-minz.tistory.com/14)

  - [가상 데이터 센터 만들기 - VPC 기본 및 연결 옵션](https://www.youtube.com/watch?v=R1UWYQYTPKo)

  - [AWS를 사용한다면 반드시 알아야 할 네트워크 기초 지식](https://www.youtube.com/watch?v=vCNexbgYmQ8)

    





