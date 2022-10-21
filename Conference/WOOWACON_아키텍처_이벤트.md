WOOWACON_아키텍처_이벤트
- WOOWACON2022 (https://woowacon.com/) (https://www.youtube.com/watch?v=b65zIH7sDug&t=11s)
- 2022.10.20 권용근 (회원 플랫폼 팀)

<br/>
<br/>
<br/>

# 회원시스템 이벤트 아키텍처 구축하기

## 무엇을 이벤트로 발행할 것인가?
- MSA (Micro-Service Architecture)
    - 애플리케이션을 `느슨하게 결합`된 서비스의 모임으로 구조화하는 서비스 지향 아키텍처(SOA) 스타일의 일종인 소프트웨어 개발 기법
    - 타 시스템에 대한 의존 영향을 줄임

### Event-Driven Architecture
- 이벤트를 달성했으니, 다른 것을 해라 라는 것이 아닌 `도메인 이벤트 그 자체`를 이벤트로 발행해야 함

#### 예시
1. 회원, 가족 계정이 한 시스템에 있다.
    - 회원의 본인인증이 초기화되는 경우 가족계정 서비스에서 탈퇴 되어야 한다.
    ```java
    public void initCertificationOwn(MemberNumber memberNumber){
        member.initCertificationOwn(memberNumber);
        family.leave(memberNumber); // 강한 결합
    }
    ```
1. 트래픽이 늘어나며 2개의 시스템으로 분리
   
    1. 가족 계정 도메인을 원격 클라이언트로 취급하여 접근 (HTTP)
        ```java
        // 회원
        public void initCertificationOwn(MemberNumber memberNumber){
            member.initCertificationOwn(memberNumber);
            familyClient.leave(memberNumber); // 강한 결합
        }

        // 가족 계정
        public void leave(MemberNumber memberNumber){
            family.leave(memberNumber);
        }
        ```
    
    1. 비동기 처리 → 스레드가 분리되었을 뿐, 가족 계정에 대한 행위는 그대로 남아있음
        ```java
        // 회원
        public void initCertificationOwn(MemberNumber memberNumber){
            member.initCertificationOwn(memberNumber);
            familyClient.leave(memberNumber); // 강한 결합
        }
        
        @Async
        void leave(MemberNumber memberNumber)

        // 가족 계정
        public void leave(MemberNumber memberNumber){
            family.leave(memberNumber);
        }
        ```
    
    1. 메시징 시스템 활용
        - 발행한 메시지가 대상 도메인에게 원하는 것을 요청한다면 비동기 처리일 뿐 논리적인 의존관계는 여전히 남아있음 
        ```java
        // 회원
        public void initCertificationOwn(MemberNumber memberNumber){
            member.initCertificationOwn(memberNumber);
            eventPublisher.leave(memberNumber); // 메시지 큐로 전달
        }

        // 가족 계정
        public void leave(MemberNumber memberNumber){
            family.leave(memberNumber);
        }
        ```

    - 메시징 시스템 활용 (느슨해진 결합)
        ```java
        // 회원
        public void initCertificationOwn(MemberNumber memberNumber){
            member.initCertificationOwn(memberNumber);
            eventPublisher.initCertificationOwn(memberNumber); // 초기화한다는 행위 자체를 메시지 큐에 전달
        }

        // 가족 계정
        public void leave(MemberNumber memberNumber){
            family.leave(memberNumber);
        }
        ```

## 이벤트 발행과 구독

#### 어플리케이션 이벤트 & 첫번째 구독자 계층
- 어플리케이션 > Spring Event > 첫번째 구독자 계층
    - Spring Event: 분산 비동기를 다루며, 트랜잭션을 구현할 수 있음
- 첫번째 구독자 계층
    - 도메인 행위의 주관심사와 거리가 먼 메시징 시스템으로 이벤트 발행
    - 이벤트 구독은 자유롭게 확장, 변경 가능
    - SNS, Kafka 등  

<img src="https://user-images.githubusercontent.com/40620421/196887090-4e4b05f3-3816-47ec-a5b8-136148b015fb.png" height="250">

#### 내부 이벤트 & 두번째 구독자 계층
- 내부 이벤트 > 두번째 구독자 계층
    - 내부 이벤트: 첫번째 구독자 계층에서 발행하는 이벤트
    - 두번째 구독자 계층: 내부 이벤트에서 발행하는 이벤트는 `Event Worker`에서 처리

<img src="https://user-images.githubusercontent.com/40620421/196888053-e49bb060-6a94-47ee-a9b3-a79dfd10aab1.png" height="250">

#### 비관심사 분리
- 도메인의 이벤트가 무엇인지 명확하게 한다
    - 도메인에 대한 이벤트의 응집을 높이고, 비관심사 이벤트에 대한 결합을 느슨하게 해야함

```java
@Transaction
public void login(MemberNumber memberNumber, DeviceNumber deviceNumber){
    // 디바이스 로그인 기록 (비관심사)
    devices.login(memberNumber, deviceNumber);
    // 동일 계정 로그인된 타 디바이스 로그아웃 처리 (비관심사)
    devices.logoutMemberOtherDevices(memberNumber, deviceNumber);
    // 동일 디바이스의 다른 계정 로그아웃 (비관심사)
    devices.logoutOtherMemberDevices(memberNumber, deviceNumber);
    // 로그인
    member.login(memberNuber);
}
```

- 내부 이벤트를 통한 처리
    1. 첫번째 구독자 계층에서 login Event를 받음
    1. 비관심사 이벤트를 서로 다른 메시징 큐에 발행하여 처리
        - 비관심사 이벤트끼리도 독립성(강한 응집), 재사용성 확보
        <img src="https://user-images.githubusercontent.com/40620421/196889484-7408d6fd-a01d-4817-a8ab-6b1d000c86e7.png" height="250">

#### 어플리케이션 이벤트 vs 내부 이벤트
- TRADE OFF
    - 어플리케이션 내에서 해결해야하는 비관심사를 처리
    - 이 외의 모든 비관심사를 처리
- 어플리케이션 이벤트
    - 주요 행위와 트랜잭션 공유
    - 주요 행위와 성능을 공유
    - 주요 행위와 강한 정합성을 보장하는 작업
- 내부 이벤트
    - 주요 행위와 트랜잭션 분리
    - 주요 행위와 성능 분리
    - 주요 행위와 강한 정합성을 보장하지 않는 작업
        - 비관심사를 분리하는 비용으로 사용자와 비지니스에 영향을 주지 않도록 분리

#### 내부 이벤트 vs 외부 이벤트
- 두번째 구독자 계층에서 발행되는 이벤트 
- 열린 내부 이벤트: 시스템 내에 존재하기 때문에 수정 사항을 쉽게 수정할 수 있음
- 닫힌 외부 이벤트: 수정 사항이 발생할 시에 외부 시스템에서 에러가 나타날 수 있음 → 페이로드 변경에 많은 비용 발생
<img src="https://user-images.githubusercontent.com/40620421/196891607-4f6e4dc4-7a1c-4d70-8ed7-d853e670e884.png" height="250">


## 이벤트 저장소 구축
- 내부 이벤트 발행이 HTTP로 되어있어 에러가 발생하면 이벤트 유실이 발생할 수 있음
- Transactional Outbox Pattern
    - 동일 저장소를 통해 DB를 저장하고, 이벤트를 발행하여 안정적인 정합성을 보장
- 유실 문제 해결
    ```sql
    create table member_event(
        id              varchar(128)  not null  primary key,
        member_nubmer   varchar(12)   not null,  -- 누가 (식별자)
        event_type      varchar(50)   not null,  -- 무엇을 하여 (행위)
        attribute       text          not null,  -- 어떤 변화가 (속성)
        created_at      datetime      not null,  -- 언제 (시간)
        published       tinyint       not null,  -- 발행 여부
        published_at    datetime      null
    )
    ```
    1. 첫번째 구독자 계층에 전달될 때, published=false
    1. 두번째 구독자 계층에 전달될 때, published=true   (내부 이벤트에 도달 여부를 확인할 수 있다)
    1. published=false인 이벤트는 내부 이벤트로 재발행됨
