---
layout: post
title: JAVA - Modifier
description: >
  JAVA - Modifier에 대하여
excerpt_separator: <!--more-->
---

<!--more-->

# JAVA - Modifier

## 1. public, private, protected, default 

자바에서 변수와 함수, 클래스에 대한 접근을 제한하는 문법.
접근을 제한하는 이유는 객체가 가진 고유의 멤버 변수값들이 외부에서 잘못 변경되거나 보안상 노출되면 안되는 데이터가 있을 경우 가리기 위함.

접근 범위는 다음과 같다.

><b>public > protected > default > private</b>

- <b>public</b> : 접근에 제한이 없음
- <b>private</b> : 동일한 패키지 내에 존재하거나 하위클래스에서만 접근 가능
- <b>protected</b> : 아무런 접근 제한자를 명시하지 않으면 default값이 되며, 동일한 패키지 내에서만 접근이 가능.
- <b>default</b> : 자기 자신의 클래스 내에서만 접근이 가능.

## 2. static, final, abstract, Interface

- <b>static</b>
  멤버 변수, 멤버 메소드, 내부 클래스에 붙일 수 있음. (outer class 제외)
  하나의 클래스에서 나온 객체들이 하나의 변수나 메소드를 공유할 수 있게 됨.

  static 영역에 있으므로 객체를 생성하지 않아도 사용 가능.
  또한 '클래스명.변수명' 으로 접근할 수 있다.
  => static 변수는 객체의 변수가 아니라 클래스의 변수이기 때문에!
  => <b>특징 : 공유, 객체없이 전역변수처럼 사용가능</b>

  >참고.
  >
  >static 메소드에서는 instance 변수를 사용하지 못한다.
  >객체를 생성하지 않으면 instance값이 없으므로.
  
  
  
- <b>final</b>
  클래스, 메소드, 변수 앞에 사용.

  - final class : 상속 받을 수 없음.
  - final method : Ovveride 할 수 없음.
  - final variable : 상수로 정의
    

- <b>Abstract</b>
  메소드 앞에 abstract가 있으면 구현부가 없어야 함. 선언부만 존재. -> <b>추상 메소드</b>
  추상 메소드를 가지려면 추상 클래스여야 함.
  추상 클래스는 일반 메소드도 가질 수 있음.
  <b>추상 클래스는 약속이다. 객체화될 수 없다.</b>

  ><b>Q1. 그럼 왜 쓰는가?</b>
  >구현되지 않은 클래스를 사용할 수 있게 해준다.
  >프로젝트를 설계할 때 추상 클래스를 만들어 놓으면, 하위 클래스가 기준을 가지고 설계될 수 있으며, upcasting이 되므로 generalization이 가능해진다.

  ><b>Q2. 어떻게 사용?</b>
  >super로 쓰인다. 상속받아서 사용. 상속받는 클래스도 추상 클래스거나, 추상메소드를 Override해서 사용한다.
  >
  >```java
  >// AbstractEx : 추상 클래스
  >// AbstractSub extends AbstractEx
  >AbstractSub as = new AbstractSub();
  >AbstractEx ae = new AbstractSub();
  >```
  >
  >위의 예제에서 ae는 원래 추상 클래스지만, 실제 내용물은 AbstractSub 이므로 상위 클래스의 메소드를 사용할 수 있다.

  

- <b>Interface</b>

  인터페이스에서의 변수는 무조건 상수이자 public.
  객체 생성 X -> 추상화.

  - 인터페이스는 implements를 통해(여러개 가능) 상속 받음.

  - 컴파일 할 때 변수앞에 public final이 붙음.
  -> 코드 작성할 때 명시적으로 public static final 붙이는 습관 들일 것.
  
- 컴파일 할 때 메소드 앞에 public abstract가 붙음. 즉, 선언부만 있어야 함.
  
    ><b>Q. 왜 쓰는가?</b>
    >
    >java는 단일 상속만 되지만, implements를 쓰면 extends도 가능해지므로 다중 상속 같이 사용할 수 있다.
    >
    >- extends는 generalization
  >- implements는 realization
  
  
  
- 다중 상속을 할 때 생기는 모호성 문제가 해결된다.
  
    ```java
    public Class TestAbstract {
        public static main(String[] args){
            InterSub is = new InterSub();
            is.print(); // super.print()는 AbstractSub의 print().
            		   // Override만 했을 경우에는 Interface의 print()를 재정의.
        }
    }
    
    abstract class AbstractEx {
    	public abstract void print();
    }
    
    class AbstractSub extends AbstractEx {
    
    	@Override
    	public void print() {
    		System.out.println("AbstractSub");
    	}
    }
    
    class InterSub extends AbstractSub implements InterEx {
    	public InterSub() {}
    	
    	@Override
    	public void print() {
    		super.print();
    		System.out.println("override here");
    	}
    }
  ```
  
- Interface가 Interface를 상속받을 때는 extends 사용.
  
- extends와 implements를 같이 받는 클래스는 다음과 같이 형변환이 가능하다.
  
    ```java
    AbstractEx ae = new (AbstractEx)InterEx();
  ```
  
  

## 추가 공부

### 1. 싱글턴 쓰는 이유

객체를 마음대로 생성할 때 문제가 생길 수 있는 클래스일 겨우? <b>하나만 객체를 만들자!</b>
여러 다른 클래스에서 객체를 생성해도 하나의 객체를 바라보게끔 하는 방법.

- 생성자 modifier를 private로
- 사용을 위한 메소드는
  static 클래스명 getInstance()

### 2. Block

- Inner 클래스에서는 Outer 클래스의 변수와 메소드를 사용할 수 있다.

- Outer 클래스 객체를 생성한다고 Inner객체가 만들어지지는 않는다.
  public static으로 하면 외부, 내부 모두 접근이 가능. 객체 생성도 가능.

- ```java
  // Instance Block
  {
      //생성자 전에 작동하는 코드.
      //ex) 객체 생성할 때 마다..
  }
  // Static Block
  static {
      // 처음 실행할 때 1번 실행 됨.
      //ex) 설정 파일을 읽거나...
  }
  ```

### 3. String

String은 Literal Pool에 존재.
String 클래스를 상속 받으면 Literal Pool에 접근 가능. 그러나 고슬링이 이러한 접근이 불가하도록 String 클래스에 final을 붙임.

### 4. CharSequence

CharSequence는 문자열 관련 super로 쓰이는 Interface이다.
String, StringBuilder ... 등의 super이다.

### Q. 상속만이 관계와 기능을 이어줄까?

클래스를 구현할 때 상위 크래스나 기능을 이용하고 싶을 뿐, 그 자체를 받고 싶지 않을 경우는?
즉, <b><i>sub</i> is a <i>super</i></b> 이게 싫다면?

- 상속 받는 것이 generalization
- 기능 혹은 데이터만 aggregation

```java
public class AggregationEx {
    public static void main(String[] args){
        Student s = new Student("홍길동", 26, "최고대", "내마음속");
        System.out.println(s.name);
        System.out.println(s.age);
        System.out.println(s.mySchool.name);
        System.out.println(s.mySchool.address);
    }
}

public class School {
    String name;
    String address;
    
    public School(String name, String address){
        this.name = name;
        this.address = address;
    }
}

public class Student {
    String name;
    int age;
    School mySchool;
    
    public Student(String name, int age, String schoolName, String schoolAddress){
        this.name = name;
        this.age = age;
        this.mySchool = new School(schoolName, schoolAddress);
    }
}
```