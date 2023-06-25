Docker

<img src="https://user-images.githubusercontent.com/40620421/190995858-fe7f49d7-cc74-424c-bcfe-8e23abe2ec8e.png">

본 내용은 해당 도서를 참고하여 정리한 내용입니다.


<br/>
<br/>
<br/>

# 1. 도커
- 애플리케이션을 컨테이너로서 좀 더 쉽게 사용할 수 있게 만들어진 오픈소스 프로젝트
- 리눅스 컨테이너에 여러 기능을 추가함

## 가상머신과 도커 컨테이너

<img src="https://user-images.githubusercontent.com/40620421/191018781-78f534c2-39a6-4453-9906-8e70cc075e81.png" width="500" >

### 가상머신
- 특징
    - 하나의 호스트에서 `하이퍼 바이저`를 이용하여 여러 운영체제를 생성해서 사용
    - 생성되는 운영체제 => 가상 머신, Guest OS
    - VirtualBox, VMWare
- 장점
    - 완전히 독립된 공간과 시스템 자원을 할당 받음
- 단점 
    - 하이퍼 바이저를 반드시 거쳐야 함으로 성능 손실 (일반 호스트에 비해 느리다)
    - 가상머신이 라이브러리, 커널 등을 모두 가짐으로 이미지가 커서 배포가 어려움

### 도커 컨테이너
- 특징
    - 프로세스 단위의 격리 환경 제공
    - 가상화된 공간 생성을 위해 리눅스 자체 기능인 chroot, namespace, cgroup을 활용
- 장점
    - 성능 손실이 거의 없음
    - 호스트 커널을 공유하여, 이미지 용량이 줄어듬 (라이브러리와 실행파일만 따로 존재)

```
커널
- 프로세스 관리, 메모리 관리, 저장장치 관리와 같은 운영체제의 핵심적인 기능을 모아둔 것이다.  
- 자동차:엔진 = 운영체제:커널
```

## 도커의 장점

### 1. 개발/배포가 편해짐
- 개발 환경을 다른 서버의 컨테이너로서 똑같이 복제할 수 있어 개발/운영이 편리함
    - 도커 컨테이너는 호스트 OS 위에서 실행되는 격리된 공간
    - 독립된 개발 환경을 보장받을 수 있음
- 커널을 포함하지 않기 때문에 이미지가 크지 않음
    - 이미지 내용을 레이어 단위로 구성하고, 레이어를 재사용하여 배포 속도가 매우 빨라짐

### 2. 독립성과 확장성이 높아짐
- 확장성과 유연성이 좋은 `마이크로 서비스`를 구현할 수 있음
    - 마이크로 서비스 구조는 여러 모듈이 독립된 형태로 구성한다.
    - 언어에 종속되지 않고, 변화에 빠르게 대응
    - 컨테이너는 여러 모듈에게 독립된 환경을 동시에 제공하여 많이 사용됨
    - 구조를 직접 구현하기 보다 도커 스웜 모드, `쿠버네티스` 등으로 구현
- 예시
    - 웹서비스의 경우, 데이터베이스 컨테이너 + 웹 서버 컨테이너로 나뉨
    - 웹 서버에 부하 발생 시, 웹 서버 컨테이너만 동적으로 늘려 부하를 줄일 수 있음

## 도커 설치

#### wsl을 통한 설치 
- Docker 설치를 위해서는 Linux가 필요, 따라서 wsl을 통해서 Windows에서 Linux를 활용할 것이다
    - `Linux`  
        - 오픈 소스 OS
        - 24시간 계속해서 돌아가는 서버를 구성하기 위해서 쓰임
    - `wsl (Windows Subsystem for Linux)`  
        - 이전에는 Virtual Machine을 통해서 Windows에서 Linux환경을 사용할 수 있었음
        - 빠른 부팅속도와 적은 메모리를 사용하면서 Linux 커널과 명령어를 사용할 수 있도록 함

<br/>
<br/>
<br/>

# 2. 도커 엔진

## 도커 이미지와 컨테이너
- 도커 엔진의 핵심이며, 기본 단위

### 도커 이미지
- 컨테이너를 생성할 때 필요한 요소
    - 여러 계층으로 된 바이너리 파일로 존재
    - 컨테이너를 생성하고 실행할 때 읽기 전용
- 이름: [저장소 이름]/[이미지 이름]:[태그]
    - 저장소 이름: 이미지가 저장된 장소. 명시되지 않은 경우 도커에서 제공되는 저장소 활용
    - 이미지 이름: 이미지가 어떤 역할을 하는지 나타냄
    - 태그: 이미지의 버전관리 혹은 리비전 관리에 사용

### 도커 컨테이너
- 도커 이미지에 의해 생성되며, 해당 이미지의 목적에 맞는 파일과 시스템 자원 및 네트워크를 사용할 수 있는 `독립적인 공간`
- 컨테이너에서 무엇을 하든 이미지에는 영향이 없음

## 도커 컨테이너 다루기

### 기본
```powershell
#======================================================
# 컨테이너 생성 및 실행 
# -i: 컨테이너와 상호 입출력 설정
# -t: tty 활성화. bash 셸을 사용하도록 설정
# --name: 컨테이너 이름 설정
docker run -i -t --name myUbuntu ubuntu:14:04

# run을 하는 과정
docker create -i -t --name myUbuntu ubuntu:14:04
docker start myUbuntu
docker attach myUbuntu

#======================================================
# 컨테이너 종료
exit or Ctrl+D # 컨테이너 정지 후 빠져나오기
Ctrl+P,Q # 컨테이너를 종료하지 않고 빠져나오기

#===============ㅊ=======================================
# 상태 확인
docker images   # 이미지 확인
docker ps -a    # 컨테이너 확인 

#======================================================
# 컨테이너 삭제
# 실행 중인 컨테이너는 삭제할 수 없음
docker stop myUbuntu
docker rm myUbuntu

docker rm -f myUbuntu   # 실행 중인 컨테이너 삭제
docker container prune  # 모든 컨테이너 삭제
docker rm $(docker ps -a -q)    # -a -q: 모든 컨테이너의 ID를 가져옴
```

### 컨테이너 외부 노출
- 컨테이너는 가상 머신과 마찬가지로 가상 IP 주소를 가짐
    - 도커가 컨테이너에 172.17.0.x의 IP를 순차적으로 할당
- -p 옵션을 통해서 바인딩할 포트를 설정
    ```powershell
    # 포트를 여러개 설정할 수 있음
    # [호스트 포트]:[컨테이너 포트] 형태
    # 호스트의 특정 IP를 활용하기 위해서는 명시 필요
    docker run -i -t -p 80:80 -p 192.168.0.100:7777:80 Ubuntu
    ``` 
- 접근 방법
    1. 호스트 IP의 80 포트로 접근
    1. 80포트는 컨테이너의 80 포트로 포워딩
    1. 웹 서버 접근

#### 컨테이너 애플리케이션 구축
- 서비스는 단일 프로그램으로 동작하지 않음
- 컨테이너를 통해 독립성과 버전 관리, 소스코드 모듈화 등이 쉬워짐
- DB 컨테이너 + 웹서버 컨테이너 예제
    - -d
        - Detached 모드로 컨테이너를 실행. 컨테이너를 백그라운드에서 실행하도록 설정
        - 상호 입출력이 불가능하여, 이를 위해서는 exec -i -t 명령어가 필요
        - exec로 컨테이너로 들어갔을 때는 exit로 나와서 컨테이너가 종료되지 않음
    - -e
        - 컨테이너 내부의 환경변수를 설정
    - --link
        - 별명(alias)를 통해 컨테이너 간 접근하도록 설정
        - link된 컨테이너가 실행X/존재X 라면 실행되지 않음

    ```powershell
    docker run -d \
    --name wordpressdb \
    -e MYSQL_ROOT_PASSWORD=password \
    -e MYSQL_DATABASE=wordpress \
    mysql

    docker run -d \
    --name wordpress \ 
    -e WORDPRESS_DB_HOST=mysql \ 
    -e WORDPRESS_DB_USER=root \ 
    -e WORDPRESS_DB_PASSWORD=password \
    --link wordpressdb:mysql \
    -p \
    wordpress
    ```

### 도커 볼륨
- 컨테이너의 데이터를 영속적 데이터로 활용하기 위한 방법
- 동기화되는 것이 아니라, 완전히 같은 디렉토리

1. 호스트 볼륨 공유
    ```powershell
    # 호스트의 /home/wordpress_db와 컨테이너의 /var/lib/mysql를 공유
    # 다수 디렉토리 공유도 가능
    # 원래 존재하던 디렉토리라면 덮어씀
    docker run -d \
    --name wordpressdb \
    -e MYSQL_ROOT_PASSWORD=password \
    -e MYSQL_DATABASE=wordpress \
    -v /home/wordpress_db:/var/lib/mysql
    -v /home/multi_dir:/mylti_dir
    mysql
    ```

1. 볼륨 컨테이너
    ```powershell
    docker run -i -t \ 
    --name volume_overide \
    -v /home/wordpress_db:/home/testdir \
    alicek106/volume_test

    # -v 옵션을 적용한 다른 볼륨 컨테이너의 볼륨 디렉토리 공유 가능
    # volume_from_container는 /home/testdir를 통해 호스트의 /home/wordpress_db 디렉토리를 공유받을 수 있다
    docker run -i -t \ 
    --name volume_from_container \ 
    --volume-from volume_overide \ 
    ubuntu
    ```

1. 도커 볼륨
    ```powershell
    # docker에서 제공하는 볼륨 기능 활용
    docker volume create --name myvolume

    # docker 볼륨과 컨테이너의 /root 디렉토리가 연결됨
    docker run -i -t --name test_container \ 
    -v myvolume:/root/

    # 볼륨 리스트/정보 확인
    docker volume ls
    docker inspect --type volume myvolume
    docker container inspect test_container
    ```

### 도커 네트워크
- 도커는 컨테이너에 내부 IP를 순차적으로 할당
    - 내부 IP가 외부와 연결되기 위해서는 호스트에 `veth.. (virtual eth)` 인터페이스를 생성함으로 이루어짐
- 도커 네트워크의 구조
    - 컨테이너의 eth0 인터페이스는 호스트의 veth 인터페이스와 연결됐으며, veth 인터페이스는 docker0 브리지와 바인딩 돼 외부와 통신할 수 있다
        - `eth0`: 공인 IP 또는 내부 IP가 할당되어 실제로 외부와 통신할 수 있는 호스트 네트워크 인터페이스
        - `lo`: 로컬 네트워크 인터페이스
        - `docker0`: 인터페이스와 바인딩 돼 호스트의 eht0 인터페이스와 이어줌
        - `veth`: 가상 네트워크 인터페이스 (실행 중인 컨테이너 만큼 생성)

    <img src="https://user-images.githubusercontent.com/40620421/193817872-331bef5d-2c16-4ac0-839e-532826a1d7cb.png" width="300">


#### 도커 네트워크 종류
- docker0 브리지 외에 여러 네트워크 드라이버 사용 가능
    - 브리지, 호스트, 논, 컨테이너, 오버레이
    - weave, flannel, openvswitch (3-rd party)
- docker network 확인
    ```powershell
    # 기본적으로 브리지, 호스트, 논 네트워크가 존재
    # 컨테이너에서 네트워크를 설정하지 않는다면 자동으로 docker0 브리지
    docker network ls
    > NETWORK ID     NAME      DRIVER    SCOPE
    > 7a8049f045f7   bridge    bridge    local 
    > 8fcf78caa1b0   host      host      local
    > ab5d61abaddb   none      null      local

    네트워크 상세 정보    
    # docker network inspect bridge
    ```

1. 브리지 네트워크
    - 사용자 정의 브리지를 생성해 각 컨테이너를 연결하는 네트워크 구조
    - --net 옵션으로 컨테이너와 연결
    ```powershell
    # 브리지 네트워크 생성
    docker network create --driver bridge \
    --subnet=172.72.0.0./16 \   # 컨테이너가 사용할 네트워크 정보
    --ip-range=172.72.0.0/24 \  # 호스트에서 사용할 컨테이너 IP 범위
    --gateway=172.72.0.1 \
    mybridge

    # 컨테이너 생성과 네트워크 연결
    docker run -i -t --name mynetwork_container \
    --net mybridge \
    ubuntu

    # 연결과 연결 해제
    docker network disconnect mybridge mynetwork_container
    docker network connect mybridge mynetwork_container
    ```

1. 호스트 네트워크
    - 호스트의 네트워크 환경을 그대로 쓸 수 있음
    - 컨테이너 내부의 애플리케이션을 별도의 포트 포워딩 없이 바로 포워딩 가능
    ```powershell
    docker run -i -t --name network_host \
    --net host \
    ubuntu
    ```

1. 논 네트워크
    - 네트워크를 쓰지 않음 (lo 네트워크 인터페이스만 존재)
    - 외부와 연결이 단점 
    ```powershell
    docker run -i -t --name network_none \
    --net none \ 
    ubuntu
    ```

1. 컨테이너 네트워크
    - 다른 컨테이너의 네트워크 네임스페이스 환경을 공유 (내부IP, MAC 주소 등)
    ```powershell
    docker run -i -t -d --name network_container_1 ubuntu

    docker run -i -t -d --name network_container_2 \
    --net container:network_container_1 \
    ubuntu
    ```

#### 브리지 네트워크와 --net-alias
- --net-alias 옵션을 통해 특정 호스트 이름으로 여러 개에 접근할 수 있음
- 도커의 DNS를 활용 (--link, --net-alias 등에 활용)
```powershell
docker run -i -t -d --name network_alias_container1 \
--net mybridge
--net-alias alicek106 ubuntu

docker run -i -t -d --name network_alias_container2 \
--net mybridge
--net-alias alicek106 ubuntu

# mybridge에 속한 컨테이너에서 alicek106이라는 호스트 이름으로 접근하면 DNS서버는 컨테이너의 IP 리스트를 반환하고, 이 중 첫번째 IP를 사용한다.
```

#### MacVLAN 네트워크
- 호스트의 네트워크 인터페이스 카드를 가상화해 물리 네트워크 환경을 컨테이너에게 동일하게 제공
- 가상의 MAC 주소를 가지며, 다른 장치와 통신 가능
```powershell
docker network create -d macvlan my_macvlan

docker run -it --name c1 --hostname c1 \
--network my_macvlan ubuntu
```

### 컨테이너 로깅
1. json-file 로그
    - 컨테이너 내부에서 일어나는 일을 알기 위함
    - 도커는 컨테이너의 표준 출력과 에러 로그를 별도 메타 데이터 파일로 저장
    ```powershell
    docker logs myUbuntu
    docker logs --tail 2 myUbuntu   # 끝에서 두개 log만 가져옴
    docker logs -f -t myUbuntu      # -f: 스트림, -t: 타임스탬프
    ```

1. syslog 로그
    - syslog: 유닉스 계열 OS에서 로그를 수집하는 표준
    - JSON 뿐 아니라 syslog로 보내 저장하도록 설정 가능
    ```powershell
    docker run -d --name syslog_container \
    --log-driver=syslog \
    ubuntu
    ```
    - rsyslog를 써서 로그 정보를 원격 서버로 보낼 수 있음
    ```powershell
    # 서버
    docker run -i -t \
    -h rsyslog \
    --name rsylog_server \
    -p 514:514 -p 514:514/udp \
    ubuntu

    # 클라이언트
    docker run -i -t \
    --log-driver=syslog \
    --log-opt syslog-address=tcp://192.168.0.100:514 \
    --log-opt tag="mytag" \     # 로그와 함께 저장될 태그
    ubuntu
    ```

1. fluentd 로깅
    - 각종 로그를 수집하고 저장할 수 있는 기능을 제공하는 오픈 소스
    - AWS S3, HDFS, MongoDB 등 다양한 저장소에 저장할 수 있음
    ```powershell
    # Mongo 서버
    docker run --name mongoDB -d -p 27017:27017 mongo

    # fluentd 서버
    docker run -d --name fluentd -p 24224:24224 \
    -v $(pwd)\fluent.conf:/fluentd/etc/fluent.conf \
    -e FLUENTD_CONF=fluent.conf \
    alicek106/fluentd:mongo

    # Docker 서버
    docker run -d -p 80:80 \
    --log-driver=fluentd \
    --log-opt fluentd-address=192.168.0.101:24224 \
    --log-opt tag=docker.nginx.webserver \
    nginx
    ```
    <img src="https://user-images.githubusercontent.com/40620421/193832200-6466138a-1c92-45e0-9152-7e3959f74cfb.png">

1. 아마존 클라우드워치 로그
    - AWS에서는 로그 및 이벤트 등을 수집하고 저장해 시각적으로 보여주는 `클라우드 워치`를 제공
    - 단계
        1. 클라우드워치에 해당하는 IAM 권한 생성
        1. 로그 그룹 생성
        1. 로그 그룹에 로그 스트림 생성
        1. 클라우드워치의 IAM 권한을 사용할 수 있는 EC2 인스턴스 생성과 로그 전송

### 컨테이너 자원 할당 제한
제품 단계의 컨테이너의 경우 자원 할당을 제한해 다른 컨테이너의 동작을 방해하지 않도록 하는 것이 중요

1. 메모리 제한
    - 단위는 m(megabyte), g(gigabyte)
    - 최소 메모리는 6MB
    - 할당된 메모리가 초과되면 자동 종료
    - swap 메모리(메모리가 가득 찼을 때 디스크 공간을 이용) 설정 가능
    ```powershell
    docker run -d \
    --memory=200m \
    --memory-swap=500m
    --name memory_1g nginx
    ```

1. CPU 제한
    - cpu-shares: CPU를 어느 비중만큼 나눠 쓸 것인지 설정
    ```powershell
    docker run -i -t --name cpu_share \
    --cpu-shares 1024 \
    ubuntu
    ```
    - cpuset-cpus: CPU가 여러 개 있을 때 컨테이너가 특정 CPU만 사용하도록 설정
    ```powershell
    docker run -i -t --name cpuset_2 \
    --cpuset-cpus=2 \   # 3번째 CPU만 사용
    stress
    stress --cpu 1      # 컨테이너 명령어: 1개 프로세스로 CPU에 부하를 줌
    ```
    - cpu-period, cpu-quota: CFS(Completely Fair Scheduler) 주기를 설정
    ```powershell
    docker run -d --name quota_1_4 \
    --cpu-period=100000 \ # 기본값이 100000으로 100ms
    --cpu-quota=25000 \   # 설정된 시간 중 CPU 스케줄링에 얼마나 할당할지
    stress                # 일반적인 컨테이너보다 성능 1/4 감소
    stress --cpu 1 

    docker run -d --name quota_1_4 \
    --cpus=0.25 \         # 위와 같은 설정
    stress
    stress --cpu 1 
    ```

1. Block I/O 제한
    - 하나의 컨테이너가 블록 입출력을 과도하게 사용하지 않게 설정
    ```powershell
    docker run -it \
    --device-write-bps /dev/xvda:1mb \
    --device-read-bps /dev/xvda:1mb \
    ubuntu

    docker run -it \
    --device-write-iops /dev/xvda:5 \   # 상대적인 값 설정
    --device-read-iops /dev/xvda:10 \
    ubuntu
    ```

## 도커 이미지
- 모든 컨테이너는 이미지를 기반으로 생성됨
- 도커 허브는 도커가 공식적으로 제공하고 있는 이미지 저장소

### 도커 이미지 생성
- docker commit을 통해 생성
    ```shell
    docker commit \     
    -a "junryeol.lee" \ # 이미지 작성자
    -m "first image" \  # 커밋 메시지
    commit_test \       # 이미지화할 컨테이너
    commit_test:first   # (이미지 명):(태그)
    ```

#### 이미지 구조 이해
- 이미지를 커밋할 때 컨테이너의 변경된 사항만 새로운 `레이어`로 저장
- 그 레이어를 포함하여 이미지를 저장

<img src="https://user-images.githubusercontent.com/40620421/194328006-ae6c09a7-431b-40e8-a027-cacc9b0cdad4.png" width="500">

#### 이미지 배포
1. 바이너리 파일 
    - 이미지를 단일 바이너리 파일로 저장할 수 있음
    - 레이어 형태를 이용하지 않아, 이미지 용량을 각각 차지함으로 좋은 방법은 아님
    ```powershell
    docker save -o ubuntu.tar ubuntu    # 저장
    docker load -i ubuntu.tar           # 로드
    ```

1. 도커 허브 활용
    - 이미지를 올리고, 내려받기만 하면 됨
    ```powershell
    docker push deepflame21/my-image-name:0.0 # (image name):(tag)
    docker pull deepflame21/my-image-name:0.0
    ```

## Dockerfile
- 애플리케이션이 동작하는 환경을 구성하기 위한 일련의 작업을 기록한 파일
    - 해당 과정을 손쉽게 기록하고 수행할 수 있는 빌드(build) 명령어를 제공
- 깃과 같은 개발 도구를 통해 애플리케이션의 빌드 및 배포를 자동화
- 이미지를 배포하는 대신에 이미지를 생성하는 방법을 기록한 Dockerfile을 배포할 수도 있음

### Dockerfile 작성
- FROM: 생성할 이미지의 베이스가 될 이미지
- MAINTAINER: 이미지를 생성한 개발자 정보
- LABEL: 이미지에 키:값 정보의 메타데이터를 추가함
- RUN: 이미지를 만들기 위해 컨테이너 내부에서 명령어를 실행
- ADD: 파일을 이미지에 추가 (Dockerfile 디렉토리를 기준으로 함)
- WORKDIR: 명령어를 실행할 디렉토리를 설정 (cd 명령어와 같은 기능)
- EXPOSE: Dockerfile 빌드로 생성된 이미지에서 노출할 포트
- CMD: 컨테이너가 시작될 때마다 실행할 명령어를 설정. Dockerfile에서 한 번만 사용 가능

```powershell
# Dockerfile 내부
FROM ubuntu
MAINTAINER deepflame21
LABEL "purpose"="practice"
RUN apt-get update
RUN apt-get install apache2 -y  # 설치할 것일지 선택하는 -y 필요
ADD test.html /var/www/html     # test.html을 컨테이너의 /var/www/html 디렉토리에 추가
WORKDIR /var/www/html
RUN ["/bin/bash", "-c", "echo hello >> test2.html"]
EXPOSE 80
CMD apachectl -DFOREGROUND

# Dockerfile 빌드를 통한 이미지 생성
# -t: 이미지 이름
# ./: 빌드할 Dockerfile의 위치
docker build -t mybuild:0.0 ./
```

#### Dockerfile 구성 요소
- `빌드 컨텍스트`
    - Dockerfile이 위치한 디렉터리
    - 이미지를 생성하는 데 필요한 파일, 소스코드, 메타데이터를 가짐
    - 빌드 컨텍스트에는 필요한 파일만 있도록 주의 (속도가 느려지고, 호스트의 메모리를 지나치게 점유)
    - .dockerignore 파일 작성을 통해 특정 파일을 제외할 수 있음
- `컨테이너 생성과 커밋`
    - ADD, RUN 등의 명령어가 실행될 때마다 새로운 컨테이너가 하나씩 생성 및 삭제
    - **명령어 줄 수 만큼의 새로운 이미지 레이어로 저장됨**
- `캐시`를 이용한 이미지 빌드
    - 이미지를 빌드한 후 다시 빌드를 진행하면 이전의 이미지 빌드에서 사용했던 캐시를 사용
    - RUN git clone ...일 때는 리비전 관리가 일어나도 지정된 버전의 코드를 들고옴으로 주의
    - --no-cache 옵션을 통해서 사용하지 않을 수 있음
- 멀티 스테이지
    - 하나의 Dockerfile 안에 여러 개의 FROM 이미지를 정의함으로써 최종 이미지의 크기를 줄이는 역할
    - 필요한 파일만 최종 이미지 결과물에 포함시킴으로써 이미지 크기를 줄일 수 있음
    ```powershell
    FROM golang
    ADD main.go /root
    WORKDIR /root
    RUN go build -o /root/mainApp /root/main.go

    FROM alpine:latest
    WORKDIR /root
    COPY --from=0 /root/mainApp . 
    # --from=0은 첫번째 FROM에서 빌드된 이미지의 최종 상태를 의미
    CMD ["./mainApp"]
    ```

#### 기타 명령어
- ENV, VOLUME, ARG
    ```powershell
    FROM ubuntu
    ENV test /home  # Dockerfile에 사용될 환경변수를 지정
    VOLUME /home dir1 /home/dir2  # 호스트와 공유할 컨테이너 내부의 디렉토리를 설정
    ARG my_arg
    ARG my_arg2=my_value2   # build 명령어에서 추가르 입력받아 사용할 변수

    docker build --build-arg my_arg=/home -t myImage:0.0 ./
    ```

- Onbuild, Stopsignal
    ```powershell
    FROM ubuntu
    ONBUILD RUN echo "onbuild" >> /onbuild_file
    # 빌드된 이미지를 기반으로 하는 다른 이미지가 Dockerfile로 생성될 때 실행할 명령어
    STOPSIGNAL SIGKILL
    # 컨테이너가 정지될 때 사용할 시스템 콜의 종류를 지정 (기본: SIGTERM)
    ```

- ADD, COPY
    - 둘 다 컨텍스트로 부터 이미지에 파일을 복사하는 기능
    - 차이점
        - COPY: 로컬 파일만 이미지에 추가 가능
        - ADD: URL 및 tar 등의 파일에서도 파일을 추가 가능 (tar을 압축해제도 진행)
    - ADD는 빌드 시점에서도 어떤 파일이 추가될 지 몰라서 COPY가 더 권장됨 

#### Dockerfile 빌드시 주의할 점
- \로 가독성을 높인다
- .dockerignore 파일을 작성해 불필요한 파일을 빌드 컨텍스트에 포함하지 않는다
- 빌드 캐시를 이용해 기존에 사용했던 이미지 레이어를 재사용한다
- 불필요한 이미지 레이어를 주의한다
    ```powershell
    FROM ubuntu
    RUN mkdir /test
    RUN fallocate -l 100m /test/dummy   ## 해당 파일은 삭제됨에도 불구하고 이미지 레이어로 남아 용량이 커진다
    RUN rm /test/dummy

    FROM ubuntu 
    RUN mkdir /test && \
    fallocate -l 100m /test/dummy && \  ## &&을 통해서 하나의 이미지 레이어로 만든다
    rm /test/dummy
    ```

## 도커 데몬
- 도커 클라이언트: API를 사용할 수 있도록 CLI를 제공하는 것
- 도커 서버: 컨테이너를 생성/실행, 이미지 관리
- 예시
    1. 사용자가 docker -v 명령어 입력
    1. 도커 클라이언트는 /var/run/docker/sock 유닉스 소켓을 사용해서 도커 데몬에 전달
    1. 도커 데몬이 명령어를 파싱하고, 작업 수행 및 결과 알림

### 도커 데몬 설정

#### 도커 데몬 제어
- -H: 도커 데몬의 API를 사용할 수 있는 방법을 추가
- IP 주소와 포트 번호 > Docker Remote API로 도커 제어 가능
    - 도커 클라이언트를 사용할 수 없음
- 다수 입력도 가능
```powershell
dockerd -H unix:///var/run/docker.sock
dockerd -H tcp://0.0.0.0:1375
dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:1375
```

#### 도커 스토리지 드라이버
- 도커 컨테이너와 이미지를 저장하고 관리함
- 하나만 선택해서 적용된 스토리지 드라이버에 따라 컨테이너와 이미지가 별도로 생성
- 개발하는 환경에 따라서 결정됨 (overlay2, deviceampper, AUFS, Btrfs 등)
- 원리
    - CoW(Copy-on-Write)
        - 스냅샷 공간에 원본 파일을 복사한 뒤 쓰기 요청을 반영
        - 파일을 읽는 작업, 파일을 스냅샷 공간에 쓰고 변경 사항을 쓰는 작업 > 2번 작업 (오버헤드)
    - RoW(Redirect-on-Write)
        - 1번의 쓰기 방식
        - 원본 파일은 스냅샷 파일로 묶고, 변경 사항을 새로운 장소에 할당받아 덮어씀
        - 스냅샷 파일은 그대로 사용하되 새로운 블록은 변경 사항으로 써 사용
    - 이미지 레이어는 `RoW 방식`으로 변경 사항을 기록하여 구성된다
- 종류
    1. AUFS
        - 데비안 계열의 기본 드라이버
        - 오랜 기간 사용되어 안정성이 우수함
        - 커널에 포함돼 있지 않아, 일부 OS에서 활용 X
        - 여러 개의 이미지 레이어를 유니언 마운트 지점으로 제공, 컨테이너 레이어는 여기에 마운트해서 이미지를 읽기 전용으로 사용
        - 실행, 삭제 등 컨테이너 관련 수행 작업이 매우 빠름으로 PaaS에 적합한 드라이버
        <img src="https://user-images.githubusercontent.com/40620421/196187561-ccac001d-09e1-4e29-b07b-99f8750f963a.png" width="500">
    1. OverlaysFS
        - 레드햇 계열 및 라즈비안, 우분투 등 대부분의 OS의 기본 드라이버
        - AUFS와 비슷한 원리로 동작 But, 좀 더 간단한 구조, 좋은 성능
        - lowerdir: 도커의 이미지 레이어
        - upperdir: 컨테이너 레이어, 변경 사항을 기록
        - mergeddir: 여러 개의 이미지 레이어를 하나의 컨테이너 마운트 지점에서 통합해 사용
    1. Btrfs 
        - 리눅스 파일시스템 중 하나
        - SSD 최적화, 데이터 압축 등 다양한 기능을 제공, 우수한 성능
        - 서브볼륨: 이미지 가장 아래에 있는 베이스 레이어
        - 스냅샷: 서브볼륨 위에 쌓이는 자식 레이어

# 3. 도커 컴포즈
- 여러 개의 컨테이너를 하나의 `프로젝트`로서 다룰 수 있는 작업 환경 제공
- 각 컨테이너의 의존성, 네트워크, 볼륨 등 정의 가능
- 컨테이너 수 유동적으로 조절 가능 (컨테이너 서비스 디스커버리도 이루어짐)
    - 한 노드가 정지되면 노드에서 실행되고 있던 컨테이너를 다른 노드에서 실행하도록 설정

## 도커 컴포즈 활용
- YAML 파일을 읽어 컨테이너 생성
    - 프로젝트 이름을 명시하지 않으면 현재 디렉토리의 이름을 갖음

```powershell
docker-compose \
scale mysql=2   # mysql 서비스를 2개 생성한다
-p myProject    # 프로젝트 이름 설정
-f /home/my_compose_file.yml    # yaml 파일 설정
up -d
```

### YAML 파일 작성
```yaml
version: '3.0'  # yaml 파일의 버전 정의
services:       # 서비스 정의
    database:
        image: mysql
        environment:    # 환경 변수 설정
            MYSQL_ROOT_PASSWORD: mypw
            MYSQL_DATABASE_NAME: mydb
    web:
        image: web
        links:      # 다른 서비스로 접근할 수 있도록 정의 (SERVICE:ALIAS)
            - db:database
            - redis
        command:    # 컨테이너가 실행될 때 수행할 명령어 (아래 두가지로 정의 가능)
            apachectl -DFOREGROUND
            [apachectl, -DFOREGROUND]   
        depend_on:  # 의존 관계를 나타냄. 명시된 컨테이너가 생성되고 실행됨
            - mysql 
        ports:      # 포트 설정. 단일 호스트 환경에서 특정 포트 지정하면 컨테이너 수를 늘릴 수 없음
            - "8080"
            - "8081-8085"
            - "80:80"
    web2:   
        build: ./composetest    # build 항목에 정의된 Dockerfile에서 이미지를 빌드
        image: web              # 빌드된 이미지 이름은 web으로 설정됨
        networks:
            - mynetwork
        volumes:
            - myvolume: /var/www/html

networks:   # yaml 파일에서 네트워크 설정
    mynetwork:
        driver: overlay
        driver_opt:
            subnet: "172.20.0.0/16"
            IPAddress: "10.0.0.2"
    ipam:   # IPAM(IP Address Manager)를 위해 사용할 수 있는 옵션
        driver: mydrive
        config:
            subnet: "172.20.0.0/16"
            ip_range: 172.20.5.0/24
            gateway: 172.20.5.1
    external_ip:    # 기존 네트워크를 사용
        external: true

volumes:    # 볼륨 설정 (어떠한 설정도 하지 않으면 local로 설정)
    driver: flocker
        driver_opts:
            opt: "1"
            opt2: 2
    myvolume:   # 기존 볼륨 사용
        external: true
```