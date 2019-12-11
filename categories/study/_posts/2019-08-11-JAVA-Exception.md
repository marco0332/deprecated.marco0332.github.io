# JAVA - Exception

예외처리를 if로 하면 명확하게 확인하거나 파악하기가 어렵다.
-> <b>예외사항도 객체로 해결하자!</b>

## Class Exception

밑에 상속받는 다양한 예외 클래스 존재.
![exceptionGraph](https://user-images.githubusercontent.com/27988544/63685555-9d0c5400-c83a-11e9-95c2-b805f18635b5.jpg)

- 예외사항이 발생할 때 프로그램을 멈추고 싶지 않을 경우.

  ```java
  // 만약 ..2에서 예외? -> catch로 이동. ..3은 실행X
  try {
      // ________		..1
      // valA / valB  ..2
      // ________		..3
  } 
  // ..4 에는 예외일 경우 실행할 내용 작성
  // ..5 에는 예외가 생긴 객체를 파라미터로.
  catch ( "..5" ) {
      // ________		..4
  }
  ```

  <b>Q. 그럼 try - catch 를 모든 문장에 하면 좋을까?</b>
  : 아니다. try - catch를 하기 때문에 느려지고, 대표적으로 Scanner가 그렇다.

  - catch문은 하위 예외부터 적어야 한다.
  - 예외처리와 관계없이 진행되어야 하는 코드가 있다면 finally { 코드 }

- 다른 예외처리 방법

  ```java
  public class exceptionEx {
      public static void main(String[] args){
          try {
              myFunction();
          } catch (예외클래스명 e){
              e.printStackTrace();
          }
      }
  }
  
  public void myFunction() throws 예외클래스명 {
      if( ~~~ ){
          throw new 예외클래스();
      }
  }
  ```

- 메소드 뒤에는 throws
  메소드 안에는 throw

- Runtime Exception을 제외하고는 Compile이 안되므로 catch(error)에 명시적으로 받거나, throws로 처리해야 한다.

