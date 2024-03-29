1_Design_Pattern_and_Paradigm

# 목차
- [프로그래밍 패러다임](#프로그래밍-패러다임)
    - [선언형과 함수형 프로그래밍](#선언형과-함수형-프로그래밍)
    - [객체 지향 프로그래밍](#객체-지향-프로그래밍)
        - [특징](#특징)
        - [SOLID](#설계원칙-solid)
- [디자인 패턴](#디자인-패턴)
    - [생성 패턴](#생성-패턴)
        - [싱글톤 패턴](#싱글톤-패턴)
        - [팩토리 패턴](#팩토리-패턴)
        - [추상 팩토리 패턴](#추상-팩토리-패턴)
        - [빌더 패턴](#빌더-패턴)
        - [프로토 타입 패턴](#프로토타입-패턴)
    - [구조 패턴](#구조-패턴)
        - [어댑터 패턴](#어댑터-패턴)
        - [복합체 패턴](#복합체-패턴)
        - [프록시 패턴](#프록시-패턴)

<br>
<br>
<br>

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
- 추상화   
    복잡한 시스템에서 핵심적인 개념 또는 기능을 간추려내는 것    
    어떤 하위클래스들에 존재하는 공통적인 메소드를 인터페이스로 정의하는 것
- 캡슐화   
    비슷한 역할을 하는 속성과 메소드를 하나의 클래스로 모으고, 캡슐 내부 로직이나 변수들을 감추고 외부에는 기능만을 제공하는 **정보 은닉** 개념 포함 (내부에 중요한 데이터를 쉽게 바꾸지 못 하도록 하기 위해 사용)  
    접근 제어자를 통해서 이루어짐 (public, protected, default, private)  
- 상속성  
    상위 클래스의 특성을 이어받아 재사용하거나 추가, 확장하는 것  
    코드 재사용, 계층 관계 생성, 유지보수에서 장점을 가짐
- 다형성    
    하나의 메서드나 클래스가 다양한 방법으로 동작하는 것 (오버로딩, 오버라이딩)
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
    - 클래스 하나에 많은 내용이 포함되어 있다면 다른 책임에 의한 변경이 불가피해지고, 의존성이 생긴다. 
    - 주의) 클래스 이름을 보았을 때 이 클래스의 책임이 명확하게 드러나야한다.
    
2. 개방폐쇄의 원칙 (OCP, Open Close Principle)
    - 소프트웨어의 구성요소(Component, Class, Method)는 확장에 열려있고, 변경에 닫혀있어야한다.
    - 요구사항의 변경이 자주 발생하더라도 기존 구성요소는 수정이 일어나지 말아야하며, 기존 구성요소를 쉽게 확장해서 재사용할 수 있어야 한다.
    - 예) 클래스의 공통되며 변하지 않는 속성들을 Interface로 정의하여 확장해나간다.
    - 주의) Interface는 가능하면 변경되어서는 되지 않는다. 경우의 수에 대한 고려와 예측이 필요하다.
    ```java
        // 잘못된 예시
        // 이 함수는 기능을 확장하기 위해서 코드 수정이 지속적으로 필요하다
        public boolean purchase(Object card, String name, int price)
        {
            boolean result;
            switch (card.toUpperCase())
            {
                case "A" -> result = ((CardA) card).send(price);
                case "B" -> result = ((CardB) card).send(price);
                case "C" -> result = ((CardC) card).send(price);
                
                default -> {
                    System.out.println("유효하지 않은 카드사");
                    result = false;
                }
            }
            
            return result;
        }
        
        // 리팩토링
        // 예시 2
        public interface Purchasable
        {
            boolean send(int price);
        }
        
        class CardA implements Purchasable
        {
            @Override
            public boolean send(int price)
            {
                System.out.println(getClass().getSimpleName() + " " + price + "원 결제 요청");
                return true;
            }
        }
        
        // Purchasable 인터페이스를 사용하여 카드가 추가되더라도 코드의 변경이 필요하지 않다.
        public boolean purchase(Purchasable purchasable, int price)
        {
            return purchasable.send(price);
        }
    ```


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

4. 인터페이스 분리 원칙 (ISP, Interface Segregation Principle)
    - 객체는 자신이 호출하지 않는 메소드에 의존하지 않아야하는 원칙.
    - 그렇게 하기 위해서는 하나의 일반적인 Interface보다 서로 다른 성격의 Interface를 명백히 분리하여, 여러 개의 Interface를 만들어야 한다.
    
5. 의존성 역전의 원칙 (DIP, Dependency Inversion Principle)
    - 객체는 저수준 모듈보다 고수준 모듈에 의존해야하는 원칙.
    - 저수준에 많은 양의 변화가 생길 시, 코드 변화가 많이 일어나야 한다. But 고수준에서 변화는 저수준에 변화가 반영되기에 코드의 확장성 및 재사용성이 늘어난다.
        - 타이어를 구현하고, 스노우 타이어를 구현하고... 구현해야하는 양이 많아진다. 공통된 부분은 interface로 묶어 고수준으로 관리하자.

# 디자인 패턴
프로그램을 설계할 때 발생했던 문제들을 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것을 의미  
설계자들이 **올바른**설계를 **빨리** 만들 수 있도록 도와줌  
참고: https://readystory.tistory.com/114  

## 생성 패턴
생성 패턴은 인스턴스를 만드는 절차를 추상화하는 패턴이다.  
객체를 생성, 합성, 표현 방법을 분리한다.  
**이슈**  
1. 생성 패턴은 시스템이 어떤 Class를 사용하는지에 대한 정보를 캡슐화한다.
2. 생성 패턴은 인스턴스를 어떻게 만들고, 결합하는지에 대한 부분을 완전히 가려준다


## 싱글톤 패턴
하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴   
보통 데이터베이스 연결 모듈에 많이 사용   
- 장점
    - 인스턴스 생성할 때 드는 메모리 비용 감소 (객체 로딩 성능이 좋아짐)
    - 다른 클래스의 인스턴스들이 데이터를 공유하기 쉬움
- 단점
    - 싱글톤 인스턴스 하나가 너무 많은 일, 데이터를 공유할 경우 다른 인스턴스 간의 결합도가 높아짐 (DI: Deplendency Injection)
    - 동기화 처리가 되어있지 않으면 인스턴스가 두 개 생성된다든지 오류가 발생할 수 있음
    - 객체 지향 프로그래밍의 의도와 맞지 않음 ([SOLID](#설계원칙-solid))

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

## 팩토리 패턴
- 객체 생성 부분을 떼어내 추상화한 패턴  
- 여러 개의 하위 클래스를 가진 상위 클래스가 있을 때, 인풋에 따라 하위 클래스를 리턴해주는 방식
- 하위 클래스의 인스턴스화를 제거하여 서로 간의 종속성을 낮추고, 확장을 쉽게 한다.  
    예를들어, 아래 예제에서 Latte 클래스가 사라진다 하더라도 코드를 크게 변경하지 않아도 된다.
        
```java
abstract class Coffee{
    public abstract int getPrice();
    public String toString() {
        return "Hi this coffe is " + this.getPrice();
    }
}

class Latte extends Coffee{
    private int price;
    public Latte(int price) {
        this.price=price;
    }
    @Override
    public int getPrice() {
        return this.price;
    }
}

class Americano extends Coffee{
    private int price;
    public Americano(int price) {
        this.price=price;
    }
    @Override
    public int getPrice() {
        return this.price;
    }
}

// 팩토리 패턴
class CoffeeFactory{
    public static Coffee getCoffee(String type, int price) {
        if("Latte".equals(type)) return new Latte(price);
        else if("Americano".equals(type)) return new Americano(price);
        return null;
    }
}

CoffeeFactory.getCoffee("Latte", 100)
```

## 추상 팩토리 패턴
- 팩토리 패턴에서는 인풋값에 따라 if-else 또는 switch를 사용하여 다양한 하위클래스를 리턴하는 형식으로 구현
- 추상 팩토리 패턴은 **if-else 또는 switch 없이** 또 하나의 팩토리 클래스를 받아 식별한다.
- 하위 클래스의 인스턴스화를 제거하여 서로 간의 종속성을 낮추고, 확장을 쉽게 한다.  
    만약 Cappuccino를 추가하고자 한다면 getCoffee() 수정 없이 CappuccinoFactory만 추가하면 된다.
```java
// 추상 팩토리 역할을 하는 인터페이스
interface CoffeeAbstractFactory{
    Coffee createCoffee();
}

class LatteFactory implements CoffeeAbstractFactory{
    private int price;
    public LatteFactory(int price) {
        this.price=price;
    }
    @Override
    public Coffee createCoffee() {
        return new Latte(this.price);
    }
}

class AmericanoFactory implements CoffeeAbstractFactory{
    private int price;
    public AmericanoFactory(int price) {
        this.price=price;
    }
    @Override
    public Coffee createCoffee() {
        return new Latte(this.price);
    }
}

//  컨슈머 클래스 (서브 클래스들을 생성하기 위해 클라이언트 코드에 접점으로 제공)
class CoffeeFactory{
    public static Coffee getCoffee(CoffeeAbstractFactory factory) {
        return factory.createCoffee();
    }
}

CoffeeFactory.getCoffee(new LatteFactory(100));
```

## 빌더 패턴
복잡한 객체를 생성시 발생하는 문제를 해결한다.

앞서 팩토리 패턴, 추상 팩토리 패턴에서는 속성값이 많을 때 다양한 문제가 발생할 수 있다.
1. 파라미터를 넘겨줄 때 타입, 순서가 복잡해짐
1. 필요없는 파라미터들에 일일히 null 값을 넘겨줘야함
1. 하위 클래스가 무거워짐

이를 해결하기 위해 별도의 Builder 클래스를 만들어 필수 값에 대해서는 생성자를 통해서, 선택적인 값들은 메소드를 통해서 입력받아 하나의 인스턴스를 리턴한다.

```java
public class Computer {
    //required parameters
    private String HDD;
    private String RAM;
	
    //optional parameters
    private boolean isGraphicsCardEnabled;
    private boolean isBluetoothEnabled;
		
    private Computer(ComputerBuilder builder) {
        this.HDD=builder.HDD;
        this.RAM=builder.RAM;
        this.isGraphicsCardEnabled=builder.isGraphicsCardEnabled;
        this.isBluetoothEnabled=builder.isBluetoothEnabled;
    }
	
    //Builder Class
    public static class ComputerBuilder{
        private String HDD;
        private String RAM;
        private boolean isGraphicsCardEnabled;
        private boolean isBluetoothEnabled;
		
        public ComputerBuilder(String hdd, String ram){
            this.HDD=hdd;
            this.RAM=ram;
        }
 
        public ComputerBuilder setGraphicsCardEnabled(boolean isGraphicsCardEnabled) {
            this.isGraphicsCardEnabled = isGraphicsCardEnabled;
            return this;
        }
 
        public ComputerBuilder setBluetoothEnabled(boolean isBluetoothEnabled) {
            this.isBluetoothEnabled = isBluetoothEnabled;
            return this;
        }
		
        public Computer build(){
            return new Computer(this);
        }
    }
}

Computer myComputer = new Computer.ComputerBuilder("500 GB", "2 GB")
                    .setBluetoothEnabled(true)
                    .setGraphicsCardEnabled(true)
                    .build();
```

## 프로토타입 패턴
- 프로토타입은 실제 제품을 만들기 앞서 대략적인 샘플 정도를 의미
- 객체를 생성하는 데 비용(시간과 자원)이 많이 들고, 비슷한 객체가 이미 있는 경우에 사용
  
DB로부터 데이터를 불러오는 행위는 비용이 크다.  
따라서 한번 객체를 생성해서 복사하는 것이 네트워크 접근이나 DB 접근보다 훨씬 비용이 적다.
  
```java
public class Employees implements Cloneable{
 
    private List<String> empList;
	
    public Employees(List<String> list){
        this.empList=list;
    }
    
    public void loadData(){
        // DB에서 모든 데이터를 가져온다
        empList.add("Pankaj");
        empList.add("Raj");
    }
	
    @Override
    public Object clone() throws CloneNotSupportedException{
        List<String> temp = new ArrayList<String>();
        for(String s : this.empList){
            temp.add(s);
        }
        return new Employees(temp);
    }	
}

Employees emps = new Employees();
emps.loadData();
Employees empsNew = (Employees) emps.clone();
Employees empsNew1 = (Employees) emps.clone();
```

## 구조 패턴
작은 클래스들을 상속과 합성을 이용하여 더 큰 클래스를 생성.  
독립적으로 개발한 클래스 라이브러리를 마치 하나처럼 활용할 수 있다.  
구조 패턴은 객체를 합성하는 방법을 제공한다.
  
## 어댑터 패턴
- 클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 변환시키는 패턴.  
- 어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있다.   

```java
public interface AudioPlayer{
   void play(String filename);
}

public class MP3 implements AudioPlayer{
   @Override
   void play(String filename){
      System.out.println("Playing MP3 File ♪ : "filename);
   }
}

public interface VideoPlayer{
   void play(String filename);
}

public class MP4 implements VideoPlayer{
   @Override
   void play(String filename){
      System.out.println("Playing MP4 File ▶ : "filename);
   }
}

public class MKV implements VideoPlayer{
   @Override
   void play(String filename){
      System.out.println("Playing MKV File ▶ : "filename);
   }
}

// VideoPlayer를 받아 AudioPlayer의 play함수를 활용하여 VideoPlayer의 play기능을 불러낸다.
public class FormatAdapter implements AudioPlayer{
   private VideoPlayer media;
   
   public FormatAdapter(VideoPlayer video){
      this.media = video;
   }
   
   @Override
   void play(String filename){
      System.out.println("Using Adapter : ");
      media.play(filename);
   }
}

AudioPlayer mp3Player = new MP3();
mp3Player.play("file.mp3");

mp3Player = new FormatAdapter(new MP4());
mp3Player.play("file.mp4");

mp3Player = new FormatAdapter(new MKV());
mp3Player.play("file.mkv");
```

## 복합체 패턴
- 객체의 관계를 트리 구조로 구성하여 단일 객체와 복합 객체 모두 동일하게 다룰 수 있도록 도움
- 그림판에서 다양한 도형을 그리고, 색을 채울 때 어떤 도형인지 구분하지 않아도 된다. 이처럼 전체 도형을 하나의 도형 다루듯이 관리할 수 있다. (**일관적 관리**)
   
### 오브젝트
1. Base Component: 클라이언트가 composition 내의 오브젝트를 다루기 위해 제공되는 인터페이스
```java
public interface Shape {
    public void draw(String fillColor);
}
```
2. Leaf: composition 내 오브젝트들의 행동을 정의. Base Component로 구현하며, 다른 Component를 참조하면 안 된다. (단일 객체)
```java
public class Triangle implements Shape {
    @Override
    public void draw(String fillColor) {
        System.out.println("Drawing Triangle with color "+fillColor);
    }
}

public class Circle implements Shape {
    @Override
    public void draw(String fillColor) {
        System.out.println("Drawing Circle with color "+fillColor);
    }
}
```

3. Composite: Leaf 객체들로 이루어져있으며 Component 내 명령들을 구현. (복합 객체)
```java
public class Drawing implements Shape {
    private List<Shape> shapes = new ArrayList<Shape>();
	
    @Override
    public void draw(String fillColor) {
        for(Shape sh : shapes) {
            sh.draw(fillColor);
        }
    }
	
    public void add(Shape s) {
        this.shapes.add(s);
    }
}
```

## 프록시 패턴
- 객체로 접근하는 것을 통제하기 위한 패턴. 그 객체의 대리자나 자리표시자의 역할을 하는 컨트롤을 제공.
- 만약 시스템 명령어를 실행하는 객체가 있을 때, 클라이언트 프로그램이 개발자의 의도와 다른 행위를 하는 심각한 문제를 야기할 수 있음.

```java
// 커맨드를 실행하는 메소드를 들고 있는 인터페이스
public interface CommandExecutor {
    public void runCommand(String cmd) throws Exception;
}

public class CommandExecutorImpl implements CommandExecutor {
    // cmd 명령어를 그대로 수행
    @Override
    public void runCommand(String cmd) throws IOException {
        Runtime.getRuntime().exec(cmd);
    }
}

// 위 클래스는 문제를 야기할 수 있기 때문에 프록스로 감싸준다. (Wrapper Class)
public class CommandExecutorProxy implements CommandExecutor {
    private boolean isAdmin;
    private CommandExecutor executor;
	
    public CommandExecutorProxy(String user, String pwd){
        if("correct_id".equals(user) && "correct_pwd".equals(pwd))
            isAdmin = true;
        executor = new CommandExecutorImpl();
    }
	
    @Override
    public void runCommand(String cmd) throws Exception {
        if(isAdmin){
            executor.runCommand(cmd);
        }else{
            if(cmd.trim().startsWith("rm")){ // Admin이 아닐 경우, 삭제 명령 불가능
                throw new Exception("rm command is not allowed for non-admin users.");
            }else{
                executor.runCommand(cmd);
            }
        }
    }
}
```

## 행동 패턴
객체나 클래스 사이에 책임 분배에 관련된 패턴.  
한 객체가 수행할 수 없는 작업을 여러 개의 객체로 분배하며, 객체 사이의 결합도를 최소화하는데 중점.  
참고: https://velog.io/@ha0kim/Design-Pattern-%ED%96%89%EB%8F%99-%ED%8C%A8%ED%84%B4Behavioral-Patterns

## 책임연쇄 패턴
- 클라이언트의 요청을 처리할 수 있는 처리 객체를 집합으로 만듬. (Chain)  
- 요청을 처리할 수 있는 객체가 여러 개일 때, 이 중 하나에게 요청을 보내려는 경우 사용.

#### 장점
1. 클라이언트가 내부 구조를 알 필요가 없다.
1. 새로운 요청에 대한 객체 생성이 매우 편리하다.

#### 단점
1. 집합 내부 사이클이 발생할 수 있다.

```java
public interface Chain {
    void setNext(Chain nextInChain); // chain을 연결하기 위한 함수
    void process(Number request);
}

public class Number {
    private int number;

    public Number(int number) {
        this.number = number;
    }

    public int getNumber() {
        return number;
    }
}

public class NegativeProcessor implements Chain {
    private Chain nextInChain;

    @Override
    public void setNext(Chain nextInChain) {
        this.nextInChain = nextInChain;
    }

    @Override
    public void process(Number request) {
        if (request.getNumber() < 0) {
            System.out.println("NegativeProcessor : " + request.getNumber());
        } else {
            nextInChain.process(request);
        }
    }
}

public class PositiveProcessor implements Chain {
    private Chain nextInChain;

    @Override
    public void setNext(Chain nextInChain) {
        this.nextInChain = nextInChain;
    }

    @Override
    public void process(Number request) {
        if (request.getNumber() >= 0) {
            System.out.println("PositiveProcessor : " + request.getNumber());
        } else {
            nextInChain.process(request);
        }
    }
}

Chain c1 = new NegativeProcessor();
Chain c2 = new PositiveProcessor();
c1.setNext(c2);

c1.process(new Number(90)); // PositiveProcessor에서 처리 된다.
c1.process(new Number(-50)); // NegativeProcessor에서 처리된다.
```

## 옵저버 패턴
- 객체의 상태 변화를 관찰하는 **관찰자 객체**를 생성
- 객체에 변화가 생기면 종속 객체들에 자동으로 변화가 통지된다.
- 예) 새로운 파일이 추가될 때 탐색기는 다른 탐색기에 즉시 변경을 통지해야한다.

#### 장점
1. 객체 간의 결합도가 느슨해진다.
1. 실시간으로 데이터를 효과적으로 배분할 수 있다.

```java
public interface Observer {
    public void update(String title, String news); // 변화를 통지받는 함수
}

public interface Publisher {
    public void registerObserver(Observer observer);
    public void notifyObservers(); // 변화를 통지하는 함수
}

public class NewsPublisher implements Publisher{
    private ArrayList<Observer> observers;
    private String title;
    private String news;

    public NewsPublisher() {
        observers = new ArrayList<>();
        title = null;
        news = null;
    }

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void notifyObservers() {
        for(Observer observer : observers) {
            observer.update(title, news);
        }
    }

    public void setNews(String title, String news) {
        this.title = title;
        this.news = news;
        notifyObservers(); // 뉴스를 설정하면 옵저버에게 통지.
    }
}

public class NewsSubscriber implements Observer{
    private String observerName;
    private String news; 
    private Publisher publisher;

    public NewsSubscriber(String subscriber, Publisher publisher) {
        this.observerName = subscriber;
        this.publisher = publisher;
        publisher.registerObserver(this);
    }

    @Override
    public void update(String title, String news) {
        this.news = title + "!!! " + news; display();
    }

    private void display() {
        System.out.println("=== " + observerName + " 수신 내용 ===\n" + news + "\n");
    }
}

NewsPublisher newsPublisher = new NewsPublisher();
NewsSubscriber newsSubscriber1 = new NewsSubscriber("옵저버1", newsPublisher);
NewsSubscriber newsSubscriber2 = new NewsSubscriber("옵저버2", newsPublisher);
newsPublisher.setNews("특보", "옵저버 패턴이 만들어졌습니다.");
// ===옵저버1 수신 내용 ===
// 특보!!! 옵저버 패턴이 만들어졌습니다.
// ===옵저버2 수신 내용 ===
// 특보!!! 옵저버 패턴이 만들어졌습니다.
```