# 프로그래밍 패러다임
프로그래밍의 관점을 갖게 하는 개발 방법론   

## 선언형과 함수형 프로그래밍
**'무엇'을 풀어내는가**에 집중하는 패러다임   

```scala
val list = List(1,2,3,4,5)
list.reduceleft(_+_) // 15
```
reduce()는 배열만 받아서 누적한 결과값을 반환하는 **순수 함수**이다.   
결과는 매개변수에만 영향을 받는다. (전역변수가 없다.)

```scala
val list = List(1,2,3,4,5)
list.map(_>=3).reduceleft(_+_) // 12
```
이와 같이 **순수 함수**들을 블록처럼 쌓아 로직을 구현하고, **고차 함수**를 통해서 재사용성을 높인 프로그래밍 패러다임이다.

## 객체 지향 프로그래밍
객체들의 집합으로 프로그램의 상호작용을 표현한다.   
설계에 많은 시간이 소요되며 처리 속도가 상대적으로 느리다.

### 특징
- 추상화: 복잡한 시스템에서 핵심적인 개념 또는 기능을 간추려내는 것 (어떤 하위클래스들에 존재하는 공통적인 메소드를 인터페이스로 정의하는 것)
- 캡슐화: 비슷한 역할을 하는 속성과 메소드를 하나의 클래스로 모으고, 캡슐 내부 로직이나 변수들을 감추고 외부에는 기능만을 제공하는 **정보 은닉** 개념 포함
- 상속성: 상위 클래스의 특성을 이어받아 재사용하거나 추가, 확장하는 것 (코드 재사용, 계층 관계 생성, 유지보수에서 장점을 가짐)
- 다형성: 하나의 메서드나 클래스가 다양한 방법으로 동작하는 것 (오버로딩, 오버라이딩)
```java
    // 오버로딩
    // 매개변수에 따라서 여러 개 둘 수 있다.
    public class person(int age) {
    	this.age = age;
    }
    public class person(int age, double height) {
    	this.age = age;
    	this.height = height;
    }

    // 오버라이딩
    // 하위 클래스에서 재정의한다.
    class Animal{
    	public void bark() {
    		System.out.println("mumu!");
    	}
    }
    
    class dog extends Animal{
    	@Override
    	public void bark() {
    		System.out.println("wal! wal!");
    	}
    }
```

### 설계원칙 (SOLID)
유지보수가 쉽고, 유연하고, 확장이 쉬운 소프트웨어를 만들기 위한 원칙   
참고: https://www.nextree.co.kr/p6960/
1. 단일 책임의 원칙 (SRG, Single Responsibility Principle)
    - 모든 클래스는 하나의 기능만을 가지며, 모든 서비스는 하나의 책임을 수행하는 데 집중해야 한다.
    - 클래스 하나에 많은 내용이 포함되어 있다면 다른 책임에 의한 변경이 불가피해진다.   
    - 주의) 클래스 이름을 보았을 때 이 클래스의 책임이 명확하게 드러나야한다.
    
2. 개방폐쇄의 원칙 (OCP, Open Close Principle)
    - 소프트웨어의 구성요소(Component, Class, Method)는 확장에 열려있고, 변경에 닫혀있어야한다.
    - 요구사항의 변경이 자주 발생하더라도 기존 구성요소는 수정이 일어나지 말아야하며, 기존 구성요소를 쉽게 확장해서 재사용할 수 있어야 한다.
    - 예) 클래스의 공통되며 변하지 않는 속성들을 Interface로 정의하여 확장해나간다.
    - 주의) Interface는 가능하면 변경되어서는 되지 않는다. 경우의 수에 대한 고려와 예측이 필요하다.

3. 리스코프 치환 원칙 (LSP, Liskov Substitution Principle)
    - 부모 객체에 자식 객체를 넣어도 시스템이 문제없이 동작하게 만드는 것을 의미한다.
    - 다형성과 확장성을 극대화하려면 하위 클래스를 사용하는 것보다는 상위 클래스(Interface)를 사용하는 것이 더 좋다.
    - 예) 직사각형-정사각형 문제

    ``` java
	public class Rectangle {
	    private int width;
	    private int height;

	    public void setWidth(final int width) {
	        this.width = width;
	    }

	    public void setHeight(final int height) {
	        this.height = height;
	    }

	    public int getWidth() {
	        return width;
	    }

	    public int getHeight() {
	        return height;
	    }
	}
	
	public class Square extends Rectangle {
	    @Override
	    public void setWidth(final int width) {
	        super.setWidth(width);
	        super.setHeight(width);
	    }

	    @Override
	    public void setHeight(final int height) {
	        super.setWidth(height);
	        super.setHeight(height);
	    }
	}
	
	// 사각형의 너비가 길이보다 길다면 길이=너비+1을 해준다
	// 여기에 Square을 대입하게되면 의도와 다르게 동직되게 된다.
    public void increaseHeight(final Rectangle rectangle) {
        if (rectangle.getHeight() <= rectangle.getWidth()) {
            rectangle.setHeight(rectangle.getWidth() + 1);
        }
    }
    
    // 이렇게 구성하면은 제대로 동작을 하지만
    // 함수가 확장에 열려있지 않아, 개방 폐쇄 원칙에 어긋난다.
    public void increaseHeight2(final Rectangle rectangle) {
        if (rectangle instanceof Square) {
            throw new IllegalStateException();
        }

        if (rectangle.getHeight() <= rectangle.getWidth()) {
            rectangle.setHeight(rectangle.getWidth() + 1);
        }
    }
    ```


# 디자인 패턴
프로그램을 설계할 때 발생했던 문제들을 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것을 의미
     
   
## 싱글톤 패턴
하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴   
보통 데이터베이스 연결 모듈에 많이 사용   
- 장점
    - 인스턴스 생성할 때 드는 메모리 비용 감소 (객체 로딩 성능이 좋아짐)
    - 다른 클래스의 인스턴스들이 데이터를 공유하기 쉬움
- 단점
    - 싱글톤 인스턴스 하나가 너무 많은 일, 데이터를 공유할 경우 다른 인스턴스 간의 결합도가 높아짐 (DI: Deplendency Injection)
    - 동기화 처리가 되어있지 않으면 인스턴스가 두 개 생성된다든지 오류가 발생할 수 있음
    - 객체 지향 프로그래밍의 의도와 맞지 않음 ([SOLID](#설계원칙-solid)))

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
   
        





