# 디자인 패턴
프로그램을 설계할 때 발생했던 문제들을 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것을 의미
     
   
## 싱글톤 패턴
___
하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴   
보통 데이터베이스 연결 모듈에 많이 사용   
- 장점
    - 인스턴스 생성할 때 드는 메모리 비용 감소 (객체 로딩 성능이 좋아짐)
    - 다른 클래스의 인스턴스들이 데이터를 공유하기 쉬움
- 단점
    - 싱글톤 인스턴스 하나가 너무 많은 일, 데이터를 공유할 경우 다른 인스턴스 간의 결합도가 높아짐 (DI: Deplendency Injection)
    - 동기화 처리가 되어있지 않으면 인스턴스가 두 개 생성된다든지 오류가 발생할 수 있음

```java
class Singleton{
    private Singleton(){}

    private static class singleInstanceHolder{
        // static이기 때문에 로딩시간에 한번 호출되며 final를 활용하여 다시 값이 할당되지 않도록 한다.
        private static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance(){
        return singleInstanceHolder.INSTANCE;
    }
}
```
