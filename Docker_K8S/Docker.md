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

