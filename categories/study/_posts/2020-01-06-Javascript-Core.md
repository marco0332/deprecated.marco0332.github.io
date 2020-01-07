---
layout: post
title: Javascript Core
description: >
  Javascript 핵심 내용에 대한 공부
excerpt_separator: <!--more-->
---

<!--more-->

<style>
.blueFont {
    color: #5577ff;
}

.es6 {
    color: #ff8899;
}
</style>

# Javascript Core - 1일차 복습
## 1. 타입과 변수
### 1. 타입
#### 1. Primitive type
- 숫자 <span class="blueFont">( 123 / NaN / infinity )</span>
- 문자열 <span class="blueFont">( "abc" )</span>
- boolean <span class="blueFont">( true / false )</span>
- null
- undefined
- symbol <span class="es6">( ES6 )</span>

> ***Q. null과 undefined의 차이?***</i><br />1. undefined는 변수가 정의되었지만 값을 할당받지 않은 상태.<br />2. null은 null값들 할당 받은 것. null은 객체이며, 아무것도 참조하지 않음을 의미. 주로 객체를 담을 변수를 초기화할 때 사용한다.<br />또한 typeof null은 object이다.<br /><br />참고 - <a href="https://webclub.tistory.com/1">null vs undefined</a>, <a href="https://webclub.tistory.com/460">undefined</a>, <a href="https://webclub.tistory.com/459">null</a>

#### 2. Reference type
- Object <span class="blueFont">( {a:1, b:2} )</span>
  - Array <span class="blueFont">( [1,2,3] )</span>
  - Function
  - Date, RegExp, 네거티브 객체...

#### 3. Javscript type 특징
- Javascript는 동적 타입 언어로 런타임에 타입 검사를 한다.
- 자료형을 선언할 필요가 없다.
- 묵시적 형변환에서 '+' 연산자는 문자열의 우선순위가 높다.<br />ex) 1 + "2" <span class="blueFont"> // "12"</span>
- '-' 연산자는 문자열이 숫자로 변환된다.<br />ex) 1 - "2" <span class="blueFont"> // -1
- new Number("2"), new parseInt("2") 등의 명시적 형변환 방법과, (+"2") 와 같은 묵시적 형변환이 가능하다.

#### 4. Boolean type에 대해서
- <b>falsy value</b>: 0, "" (빈 문자열), null, undefined, false, NaN
- <b>truthy value</b>: falsy value에 해당하지 않는 모든 것. 빈 배열, 객체 포함.
- <b>NaN</b>: 잘못된 숫자 연산시 반환되는 값. 모든 비교 연산에서 false 반환.
- <b>!</b>: truthy, falsy value를 반전해서 boolean 타입으로 반환.<br />ex) !1 <span class="blueFont"> // false</span>
- <b>!!</b>: 값을 boolean으로 변환<br />ex) !!1 <span class="blueFont"> // true</span>

Javascript에서 값이 자료형까지 완전히 동일한지 확인하기 위해서는 '==='을 사용해야 한다.  
&nbsp;&nbsp;ex1) 123 == "123" <span class="blueFont"> // true</span><br />&nbsp;&nbsp;ex2) 123 === "123" <span class="blueFont"> // false</span>

> ***+ &alpha;. &&과 || 연산자를 이용한 조건문***<br />- var something = a || 1;<br />&nbsp;&nbsp;: a가 truthy하면 something은 a, falsy하면 1이 된다.<br />- a && doSomething();<br />&nbsp;&nbsp;: a가 truthy하면 doSomething() 함수 실행.

#### 5. Wrapper 객체
```javascript
var a = "abc";
// a = new String("abc"); _ 내부적으로 수행
a.length; // 3
// a = "abc"; _ 내부적으로 수행
```
위의 예시를 보면 문자열은 primitive 타입이지만 주석 처리된 부분으로 인해서 a.length의 결과를 얻을 수 있다.  
이렇게 사용할 수 있도록 하는 것이 wrapper 객체이며, **Number, String, Boolean, Symbol** 이 있다.

#### 6. 객체
- Javascript에서 모든 객체는 Object를 상속받는다.
- for in 문을 이용하면 객체의 멤버를 열거할 수 있다.
```javascript
var obj = {
    a: 1,
    b: 2,
    c: 3
};

for (var key in obj) {
    console.log(key, obj[key]);
}

// ES6
// 열거 가능한 멤버만 가져온다.
var obj2 = {...obj};
```
- Object.defineProperty<br />
: 객체 멤버 속성 설정하는 메서드. Object.defineProperty(대상 객체, 정의하려는 키 명, 서술자)  
  - 설정객체 (서술자, description)<br />
    1. ***enumerable***: for in 이나 Object.keys 에서 해당 속성 노출 여부.
    2. ***configurable***: 해당 키를 delete 연산자로 제거 가능 여부와 속성 설정 변경 가능 여부.
    3. ***writable***: 키의 값 갱신 여부.
    4. ***value***: 키에 할당될 기본 값 설정.
    5. ***get***: getter 정의. 인자를 가지지 않을 수 있으며, 값을 반환하는 함수.
    6. ***set***: setter 정의. 인자로 값 하나를 받아들이며, 반환 값이 없을 수 있음.
  1. 공동 속성 : enumerable, configurable
  2. 직접 데이터 지정하는 속성(data descriptor): writable, value
  3. 간접 데이터 지정하는 속성(accessor descriptor): get, set  
    
    참고 - <a href="https://www.bsidesoft.com/?p=1878">Object.defineProperty</a>
- 객체 instanceof 객체 원형<br />
: 객체가 원형을 상속하는 인스턴스인지 확인 후 boolean 반환.
- delete 객체.프로퍼티<br />
: 객체의 프로퍼티 제거 후 성공 여부 boolean반환. 객체 자체는 제거할 수 없다.