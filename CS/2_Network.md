2_Network


# 네트워크

## 기본 개념
노드와 링크가 서로 연결되어 있거나 연결되어 있지 않은 집합체
- 노드: 서버, 라우터, 스위치
- 링크: 유선 또는 무선  

### 처리량과 지연 시간
'좋은' 네트워크? 처리량이 많음. 지연 시간이 짧음. 장애 빈도가 적음. 좋은 보안.   

- 처리량   
링크를 통해 전달되는 단위 시간당 데이터양 (bps, bits per second)   
처리량은 **트래픽**, **대역폭**, 네트워크 중간에 발생하는 에러, 하드웨어 스펙에 영향을 받음
    - **트래픽**  
    서버를 통해 최종 사용자에게 전달된 데이터의 양.  
    사용자가 많아질 수록 커진다.    
    트래픽 = 용량 * 사용자수 * 개수 = 4GB 영화 * 10명 * 10개 = 400GB
    - **대역폭**  
    주어진 시간동안 네트워크 연결을 통해 흐를 수 있는 **최대** 비트 수
    
- 지연 시간  
어떤 메시지가 두 노드를 왕복하는데 걸리는 시간. 요청이 처리되는 시간.  
이는 **매체 타입(무선, 유선)**, **패킷 크기**, **라우터의 패킷 처리 시간**에 영향을 받음

### 네트워크 토폴로지(Network topology)  
노드와 링크가 어떻게 배치되어 있는지에 대한 연결 형태.  
**병목 현상**을 찾는 데에 중요한 기준이 됨.
- 병목 현상  
전체 시스템의 성능이 하나의 구성 요소로 인해 제한을 받는 현상.  
네트워크 대역폭, 토폴로지, 서버 CPU/메모리에 영향을 받는다.


토폴로지 종류  
<img src="https://private-user-images.githubusercontent.com/40620421/296165502-8054a793-fd4e-45a3-818c-daecbfeac267.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUwNDU5NjUsIm5iZiI6MTcwNTA0NTY2NSwicGF0aCI6Ii80MDYyMDQyMS8yOTYxNjU1MDItODA1NGE3OTMtZmQ0ZS00NWEzLTgxOGMtZGFlY2JmZWFjMjY3LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTEyVDA3NDc0NVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTBiNDc0ODgwY2IzMTcwNTRhYTYyZjk1ZGY3YWYwOGFkZWIzMGMxOGRiMTkxNTIyZmUyOGI2OTNlNzNiMWRiM2QmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.j_7lXZgWs6_A-y6TjsbZnFwqXb_hUwNmG4as3u0lbAU" width="400">

1. 트리 토폴로지  
개념: Tree구조 형태이다.  
장점: 통제 및 유지보수가 용이하다. 단말기 추가, 제거 및 에러 발생시 발견이 쉽다.  
단점: 병목 현상이 발생할 수 있다. 중앙 컴퓨터에 오류 발생 시 전체에 영향을 끼친다.

1. 버스 토폴로지  
개념: 중앙 통신 회선 하나에 여러 대의 노드를 연결. 근거리 통신망(LAN)에 사용.  
장점: 하나의 컴퓨터가 다운되어도 문제가 없다. 노드 추가/제거가 쉽다.  
단점: 통신 회선 길이에 제한이 있음. 충돌이 자주 일어난다. 스푸핑이 가능하다.

> 스푸핑: 송신부의 패킷을 송신과 관련 없는 다른 악의적인 노드에 전달되도록 처리하는 것

3. 스타형 토폴로지  
개념: 중앙에 있는 컴퓨터를 중심으로 터미널이 연결됨.  
장점: 유지보수 및 관리가 용이. 에러 탐지가 쉬움.  
단점: 중앙 컴퓨터 고장 시 전체 네트워크가 마비됨.

1. 링형 토폴로지  
개념: 인접해 있는 노드들을 연결하는 단방향 전송 형태.  
장점: 노드 수가 증가되어도 네트워크상 손실이 적음. 충돌이 발생되는 가능성이 적음.  
단점: 노드 추가/삭제가 어려움. 회선에 장애가 발생하면 전체에 영향.

1. 그물형 토폴로지  
개념: 모든 노드들이 상호 연결된 형태.  
장점: 회선에 장애가 발생해도 다른 경로로 통신이 가능.  
단점: 노드 추가/삭제가 어려움. 운영/구축 비용이 많이 듬.  
  
## TCP/IP 4계층 모델
- 네트워크에서 사용되는 `통신 프로토콜의 집합.`
- 호환성 보장 (다른 제조사 장비들끼리도 통신 가능), 각 계층에 대한 캡슐화와 은닉이 가능. 계층별로 문제 확인이 가능하여 쉽게 해결.
    - 캡슐화, 은닉: 캡슐 내부 로직이나 변수들을 감추고 외부에는 기능만을 제공 
- TCP/IP 4계층 모델 또는 OSI 7계층 모델로 설명됨.
  
<img src="https://private-user-images.githubusercontent.com/40620421/296165951-e868a879-14cb-41fc-88ac-2313186cfb58.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUwNDYwODIsIm5iZiI6MTcwNTA0NTc4MiwicGF0aCI6Ii80MDYyMDQyMS8yOTYxNjU5NTEtZTg2OGE4NzktMTRjYi00MWZjLTg4YWMtMjMxMzE4NmNmYjU4LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTEyVDA3NDk0MlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTQzNDkxN2VmZWM1NmM5OTRjN2YwNjIxYjAyNzI5NjFiYzNlODM5MmIyM2VhNjI0ZmZhMzNiM2Y2MmEyOGU2NGImWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.QzRZMzWuSB6u8kMXz0o8d216Nd9-9FIXkzeApgnZwyU" width="400">  

차이점  
1. OSI에서는 Application 계층이 3개로 쪼개져 있음.
1. TCP/IP의 Internet 계층을 OSI에서는 Network 계층으로 불림.  

이 계층들은 서로 영향을 받지 않도록 설계됨.

### Application 계층
응용 프로그램이 사용되는 프로토콜 계층.  
웹 서비스, 이메일 등 서비스를 실질적으로 사람들에게 제공  

#### 예시
- FTP: 장치와 장치간의 **파일을 전송**하는데 사용되는 표준 통신 프로토콜.
- SMTP: **전자 메일 전송**을 위한 인터넷 표준 프로토콜.
- HTTP: WWW를 위한 데이터 통신의 기초. **웹사이트**를 이용하는데 쓰는 프로토콜.
- DNS: 도메인 이름과 IP 주소를 매핑해주는 서버.
- SSH: 네트워크 상 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 안전하게 실행하기 위한 암호화 네트워크 프로토콜.

### Transport 계층
송신자와 수신자를 연결하는 통신 서비스를 제공.  
Application 계층과 Internet 계층 사이에 데이터가 전달될 때 중계 역할을 함.

#### 예시
TCP  
- 패킷 사이의 순서를 보장하는 연결지향 프로토콜 사용. (높은 신뢰성을 가짐)  
- **가상회선 패킷 교환 방식**: 패킷에 가상회선 식별자가 포함. 패킷들은 전송된 순서대로 도착하는 방식.  
- 3-way handshake를 통한 연결 (신뢰성 확보)

<img src="https://private-user-images.githubusercontent.com/40620421/296167171-4d2d8f30-e1b1-4dfd-a288-092462e52a78.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUwNDY0MDQsIm5iZiI6MTcwNTA0NjEwNCwicGF0aCI6Ii80MDYyMDQyMS8yOTYxNjcxNzEtNGQyZDhmMzAtZTFiMS00ZGZkLWEyODgtMDkyNDYyZTUyYTc4LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTEyVDA3NTUwNFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTljYmViMGY1OWEyN2RlMWRlOTdlYmU2N2RmNGQyMDBmMzZjNzU2Njc5NWI2ZGY1NDQwNGZkODg2YmQ1YzVhMWMmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.cmj2t-L9DkH4vSZv0Tul55gEE-gnYeLMnzUYhdOuFB0" width="500">

1. SYN(Sychronization/요청) 단계  
클라이언트가 서버에 임의의 시퀀스 번호(ISN)을 보냄.
1. SYN+ACK(Acknowledgement/응답) 단계  
서버가 클라이언트의 SYN을 수신하고, 서버의 ISN + 클라이언트의 ISN+1 을 보냄.  
1. ACK 단계
클라이언트가 서버의 SYN을 수신하고, 서버의 ISN+1 을 보냄.

- 4-way handshake를 통한 연결 해제

<img src="https://private-user-images.githubusercontent.com/40620421/296167527-47165c4b-5da2-430b-8b63-a51096fae092.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUwNDY1MDgsIm5iZiI6MTcwNTA0NjIwOCwicGF0aCI6Ii80MDYyMDQyMS8yOTYxNjc1MjctNDcxNjVjNGItNWRhMi00MzBiLThiNjMtYTUxMDk2ZmFlMDkyLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTEyVDA3NTY0OFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTA5ZmEyOWJmNGY4YmMxYTA4YWZmY2JlM2I3NjE4YmU2NDQ0NjcyOGI4MWUxYWVhZTYwZmQ0ZWJhMTMxMGY5ZGMmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.gTJR5AXHWSmbJm_5Zh-Q3gp_zp3n5VfqJzawTxgeQT0" width="500">

1. 클라이언트가 연결을 닫기 위해 서버로 FIN 플래그를 보내고, FIN_WAIT_1 상태로 응답을 기다린다.
1. 서버는 ACK라는 승인 플래그를 보낸다. 응답을 받은 클라이언트는 FIN_WAIT_2 상태가 된다.
1. 서버는 일정 시간 후 FIN 플래그를 보낸다.
1. 클라이언트는 FIN 플래그를 받고 TIME_WAIT 상태가 되며 서버로 ACK 플래그를 보내 서버는 CLOSED 상태가 된다.  
일정 시간 후 클라이언트는 CLOSED 상태가 되어 연결이 닫긴다.  
> TIME_WAIT가 있는 이유  
> 1. 지연 패킷이 발생하여 패킷을 처리하지 못 한다면 데이터 무결성 문제가 발생할 수 있다.
> 1. 서버의 연결이 닫혔는지 확인하기 위해서 (LAST_ACK 상태에서 멈춰 에러 발생할 수 있음)

UDP  
- 순서를 보장 X, 수신 여부를 확인 X, 단순히 데이터만 발신. (속도가 빠르다)  
- **데이터그램 패킷 교환 방식**: 패킷이 독립적으로 이동. 최적의 경로를 선택하여 이동.

### Internet 계층
- 상위 계층에서 받은 패킷을 IP 주소로 지정된 목적지로 전송하는 계층.  
- 호스트간의 라우팅 담당
- 상대방이 제대로 받았는지에 대해 보장하지 않는 비연결형적 특징을 가짐.

### Link 계층
- 전선, 광섬유, 무선 등 실질적으로 데이터를 전달하며 장치 간에 신호를 주고받는 규칙
    - 물리 계층: 무선 LAN과 유선 LAN을 통해 데이터를 보내는 계층
    - 데이터 링크 계층: 이더넷 프레임을 통해 에러 확인, 흐름 제어, 접근 제어를 담당하는 계층
- 물리적 주소로 MAC을 사용
    - MAC 주소: 컴퓨터나 노트북 등 각 장치에는 네트워크를 연결하기 위한 장치가 있는데, 이를 구별하기 위한 식별번호를 의미.

### 계층 간에 데이터 송수신

<img src="https://mblogthumb-phinf.pstatic.net/MjAxODAxMTNfMjIz/MDAxNTE1ODUzNzUzODM5.FP_zggvTRGjvViOWinAu-l6kO-VlWV66Cc9moHM5FKcg.HPx0n154P81GrDIkDmfElAS7patAF0tMlWIv2cLJzgUg.PNG.wnrjsxo/image.png?type=w800" width="600"> 

- 데이터가 다른 컴퓨터로 송신되기 위해 캡슐화를 거침.
- PDU(프로토콜 데이터 단위)  
각 계층을 거치며 헤더 정보가 추가되며 이름이 달라짐  
Data > Segment > IP Packet > Frame > bit  

## IP 주소
Internet 계층에서 IP 주소를 사용하여 데이터를 목적지로 전송한다.

### ARP (Address Resolution Protocol)
- 보통 목표 호스트의 IP 주소는 알고있지만, 실제 전송을 위해서는 물리적인 MAC 주소가 필요함
    - 이를 위해서 IP와 MAC 주소간의 매핑 정보를 테이블의 형태로 관리함
        - IP 주소(가상 주소) > **ARP** > MAC 주소(실제 주소)
        - MAC 주소(실제 주소) > **RARP** > IP 주소(가상 주소)
- 통신 과정에서 IP 주소를 모르는 경우, 브로드캐스트를 하게되며, 목표 호스트는 이 요청을 받고, 자신의 IP와 MAC 주소를 포함한 ARP 응답을 유니캐스트를 통해 하게 됨
    - 브로드캐스트: 호스트 A가 속한 모든 네트워크에 전달됨
    - 유니캐스트: 고유 주소로 식별된 하나의 네트워크 목적지에 1:1로 데이터를 전송하는 방식
    

### 홉바이홉 통신
- 네트워크에서 한 홉(단일 라우터 또는 스위치)를 거치면서 전송되는 통신 방식
- 각 홉은 데이터를 다음 목적지로 전송하기 위해 최적의 경로를 선택
    - 네트워크 상태, 트래픽 부하, 라우팅 테이블 등 고려
    - 최적의 경로를 통해 네트워크 트래픽 관리


#### Switch
- 데이터 링크 계층에서 동작하는 네트워크 장비
- 여러 개의 포트를 가지고 잇으며 이를 통해 여러 장치를 연결
    - 스위칭 테이블을 유지하여 포트와 MAC 주소의 매핑 정보를 저장
<img src="https://private-user-images.githubusercontent.com/40620421/296176561-8c17c6cd-8edc-44f5-bb0c-7c7fdbcf3666.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDUwNDg2NDUsIm5iZiI6MTcwNTA0ODM0NSwicGF0aCI6Ii80MDYyMDQyMS8yOTYxNzY1NjEtOGMxN2M2Y2QtOGVkYy00NGY1LWJiMGMtN2M3ZmRiY2YzNjY2LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDAxMTIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwMTEyVDA4MzIyNVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTlkMGFmZjc0OTFjNDM2ZjY2OTU3MmVmMzIzZTRlNzE2MjdlYTU4NjIyNjM1ZjJiYTZlMzU1ODVjZmEzMGZiZWQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.hVapaZh-HT0tEQGc50B3HhVo-UfVMNacPLNsQCwPyLA">

#### Router
- 모델 네트워크 계층에서 동작 
    - Switch를 연결
- IP 주소를 기반으로 패킷을 전달하는 역할
    - 서로 다른 네트워크와 연결하는 역할 (최적의 경로를 활용)
- 라우팅 과정
    1. 패킷 전달: 목적지 IP 주소를 통해서 적절한 네트워크로 전달
    1. 패킷 필터링: 네트워크 트래픽을 필터링. 불필요한 트래픽 차단
    1. 네트워크 주소 변환(NAT): 사설 IP주소와 공인 IP 주소 간의 통신을 지원
    1. 패킷 분할 및 조립: 큰 크기의 패킷을 작은 크기로 분할하고, 조합하여 전달
- 라우팅 테이블
    - 패킷을 전달할 경로를 저장한 DB
        - 라우터나 스위치와 같은 네트워크 장비에 저장
        - 네트워크 트래픽을 효율적으로 라우팅
    - Column
        - Destination IP: 목적지 IP 주소 범위
        - Next Hop: 패킷을 전달할 다음 라우터 또는 게이트웨이의 IP 주소 (인터페이스를 통해서 Next Hop으로 감)
        - Interface: 패킷을 전달할 다음 라우터 또는 게이트웨이의 인터페이스 (네트워크를 연결하는 물리적 또는 가상적인 연결점)
    - 예시
        Destination IP | Next Hop	| Interface
        --|--|--
        192.168.1.0/24	 | 10.0.0.1 |	eth0 (첫번째 이더넷 카드)
        10.0.0.0/24	| 192.168.1.1 |	eth1
        0.0.0.0/0 |	10.0.0.2 |	eth2
- 종류
    1. 정적 라우팅
        - 관리자가 수동으로 경로를 정의
        - 목적지 IP 주소 또는 서브넷과 해당 경로를 매핑하여 패킷이 목적지로 전달되는 경로를 결정
        - 단순하고 예측 가능하지만, 네트워크 변화에 대응하기 어렵고 유연성이 제한적
    1. 동적 라우팅
        - 동적 라우팅은 네트워크 프로토콜을 사용하여 경로 정보를 자동으로 교환하고 업데이트
        - 동적 라우팅 프로토콜을 사용하여 경로 정보를 교환하고 네트워크의 변화에 따라 최적의 경로를 선택
        - 프로토콜 종류: OSPF (Open Shortest Path First), BGP (Border Gateway Protocol) 등
        - 네트워크의 유연성과 확장성을 향상시키며, 장애 복구 및 로드 밸런싱에도 유용합니다.
    1. 로드 밸런싱
        - 여러 대상 서버 또는 서비스 사이에서 트래픽을 분산
        - 서버의 가용성을 모니터링하고 필요에 따라 트래픽을 다른 서버로 전달하여 부하 분산

<img src="https://user-images.githubusercontent.com/40620421/246644470-8c9cd0f3-6776-455b-9f11-98d170493404.png" width="500">

#### Gateway
- 서로 다른 네트워크 간의 통신을 가능하게 하는 장치
    - 네트워크의 입구 역할
    - 프로토콜 변환, 주소 변환 등을 통해 다른 네트워크로 전달될 수 있도록 함
- Gateway
    - A (192.168.1.5) <> B (192.168.1.6/192.168.2.6) <> C (192.168.2.5)
    - A에서 C에 접근하려고 해도 안 됨 > B를 통해서 가야함
    - `ip route add 192.168.2.0/24 via 192.168.1.6` 명령을 통해서 게이트웨이에 등록
    - A에서 C 접근 시에 B로 갔다가 C로 가게 됨


### IP 주소 체계
1. IPv4
    - 32비트를 8비트 단위로 쪼갬. 8비트씩 4자리 (8비트 = 2^8 = 0~256 표시 가능)
    - ex. 123.45.67.89
1. IPv6
    - 128비트를 16비트 단위로 쪼갬. 16비트씩 8자리
    - 앞에 64비트는 네트워크. 뒤에 64비트는 네트워크에 연결된 통신장비
    - IPv4 주소가 고갈되는 문제를 해결을 위함
    - ex. 2001:0DB8:1000:0000:0000:0000:1111:2222 =  2001:DB8:1000::1111:2222 (연속되는 0은 ::로 생략)

#### 클래스 기반 할당 방식 (CIDR)
- A,B,C,D,E 다섯 개 클래스를 기반으로 구분 (5개의 IP 주소를 가짐)
- 앞 부분: 네트워크 주소, 뒤 부분: 컴퓨터에 부여하는 주소인 호스트 주소
    - ex. 클래스 A로 12.0.0.0 네트워크를 부여받았다면 앞에 12. 는 고정된다  
    따라서 12.0.0.1~12.255.255.254를 호스트 주소로 사용할 수 있다
- 사용하는 주소보다 버리는 주소가 많은 단점이 있다

#### NAT (Network Address Translation)
- 패킷이 라우팅 장치를 통해 전송되는 동안 IP주소를 변경하는 방법
- 공인 IP와 사설 IP를 나누어서 많은 주소를 처리
    - 공인 IP의 개수는 한계가 있기 때문
- 내부 네트워크의 IP를 노출하지 않아 보안에 좋음
- 여러 명이 동시에 접근할 때 속도가 느려질 수 있음
- 예시 (wifi)
    - 여러 대의 컴퓨터와 스마트폰이 연결되어 있고, 이는 각각 192.168.1.2, 192.168.1.3 등의 사설 IP를 가지고 있음
    - 사설 IP 주소 > 라우터 > NAT 변환 > 공인 IP주소 > 외부 서버 응답 > 역변환 > 사설 IP로 전달


### DNS (Domain Name System)
- 인터넷에서 도메인 이름과 IP 주소를 매핑하는 시스템
    - 예: www.example.com > 192.0.2.1 (반대도 가능)
    - /etc/hosts > `192.0.2.1   www.example.com`
    - /etc/resolv.conf > `nameserver 8.8.8.8` (windows의 경우, DNS서버 연결하는 설정이 있음)
- 구성 요소 (계층적 구조)
    - DNS 클라이언트
        - DNS 정보를 요청하는 시스템이며, 주로 사용자의 컴퓨터 또는 네트워크 장비
        - 도메인 이름을 IP 주소로 변환하거나 역으로 IP 주소를 도메인 이름으로 변환하기 위해 DNS 서버에 쿼리를 보냄
    - DNS 서버
        - DNS 정보를 저장하고 관리하는 서버 (서버마다 너무 많은 DNS를 가지고 있음)
        - DNS 서버는 도메인 이름과 해당 도메인의 IP 주소를 포함하는 DNS 레코드를 저장하고, 클라이언트의 DNS 쿼리에 응답
    - DNS 캐시 서버
        - DNS 캐시 서버는 이전에 요청한 DNS 정보를 일정 기간 동안 캐시에 저장하여 반복적인 쿼리의 응답 시간을 단축

## HTTP
애플리케이션 계층으로서 웹서비스 통신에 이용됨  
(Application Layer > Transport Layer > Internet Layer > Network Access Layer)

### HTTP/1.0
- 기본적으로 한 연결당 하나의 요청을 처리하도록 설계됨
- 이는 파일을 가져올 때마다 TCP의 3-웨이 핸드셰이크를 열어 **RTT(Round Trip Time)를 증가**시킴

#### RTT 감소 방법
1. 이미지 스플리팅
    - 많은 이미지가 합쳐있는 이미지를 다운로드 (많은 이미지를 다운하면 과부하)
    - 이를 기반으로 각각의 이미지의 속성을 설정
1. 코드 압축
    - 개행 문자, 빈칸을 없애서 코드의 크기를 최소화
    - 코드 용량이 줄어듬
1. 이미지 Base64 인코딩
    - 이미지 파일을 64진법의 문자열로 인코딩
        - 인코딩: 정보의 형태나 형식을 표준화, 보안, 처리 속도 향상, 저장 공간 절약
    - 문자열 크기가 더 커지는 단점이 있음

### HTTP/1.1
- TCP 초기화 후 **keep-alive** 옵션으로 여러 개의 파일을 송수신
- 문서 안에 다수의 리소스를 처리하려면 대기 시간이 길어짐
- 헤더에 많은 메타 데이터가 포함되어 무거운 단점

### HTTP/2
- HTTP/1.x보다 지연 시간을 줄이고 응답 시간을 빠르게 처리
- 멀티플렉싱
    - 여러 스트림을 사용하여 송수신
    - 스트림간 독립적 (특정 스트림의 패킷이 손실되어도 영향이 없음)
    - 스트림 내 데이터들도 쪼개져있음
- 서버푸시
    - 클라이언트 요청 없이 서버가 리소스를 푸시할 수 있음
    - html과 함께 하는 css나 js 파일을 서버에서 함께 푸시하여 속도 개선

### HTTPS
- 애플리케이션 계층과 전송 계층 사이에 신뢰 계층인 **SSL/TLS** 계층을 넣음
    - 제 3자가 메시지를 도청하거나 변조하지 못 하도록 함 (인터셉터 방지)
    - 이를 통한 통신 암호화
