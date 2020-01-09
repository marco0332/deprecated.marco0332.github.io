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

## 2. 함수

**다중 프로그래밍 패러다임**

- 절차적 프로그래밍 : 조건문, 분기문, 반복문, 프로시저
- 함수형 프로그래밍 : 일급함수(함수를 값으로 취급). 고계(고차) 함수, 익명함수
- 객체 지향 프로그래밍 : 객체의 생성, 상속, 다형성

javascript에서 함수는 위의 성질을 모두 가진다.

### 1. 일급 객체

함수로 object를 상속하는 객체. 평범한 객체가 가진 모든 성질을 지님.

1. 프로퍼티를 가지고 있음
2. 변수의 값이나 객체, 배열의 멤버가 될 수 있음
3. 함수의 매개변수나 반환값이 될 수 있음
   + 함수를 인자로 받아서 함수를 반환하는 함수를 고계 함수라고 한다.

### 2. 다형성

```javascript
function add(a, b) {
  return a + b;
}

add(1, 2); // 3
add(3, 5, 7); // 8
add(1); // NaN

// 함수 호출 시 인자가 들어오지 않으면 undefined가 되기 때문에 default값을 설정해야 하는 경우
function add2(a, b) {
  if(!b) b = 1;
  return a + b;
}
// ES6
function addES6(a, b=1) {
  return a + b;
}
```

Javascript에서는 같은 함수를 여러번 중첩 선언할 수는 없지만, 함수의 매개변수 자체를 유동적으로 전달 가능하기 때문에 오버로딩을 구현할 수 있다.

또한 함수 내부에서만 접근 가능한 arguments 객체를 사용하면 함수에 넘겨진 모든 매개변수를 제어할 수 있다.

```javascript
function fn() {
  return arguments;
}
fn(); // Arguments
fn(1, 2, 3); // Argumnts [1,2,3]

Math.max(1,2,3,4,5,6,7,8,9); // 9

// ES6
function fnES6(...args) {
  return args;
}
fn2(1,2,3); // [1,2,3]
```

### 3. 메서드

객체 내부에 멤버로 존재하는 함수.

```javascript
var obj = {
  method1: function() {return 1;},
  method2: () => {}, // ES6
  method3: function fn() {},
  method4() {} // ES6
}
obj.method1();
obj["method1"]();
```

### 4. 생성자

new 키워드를 이용해 함수를 객체 원형으로 이용가능하다. 함수를 생성자로 호출하면 함수가 가진 prototype 프로퍼티를 상속받은 새로운 빈 객체가 생성된다. 단, 화살표 함수는 생성자로 이용 불가능하다.

```javascript
// Object, Function, Array 모두 함수이다.
new Object();
new Function();
new Array();
```

### 5. this

함수 내부에서 this 키워드는 함수를 호출한 주체 객체를 가리킨다. 따라서 this의 값은 함수가 호출될 때 결정된다.

```javascript
function fn(name) {
  this.name = name;
  return this;
}
fn("giin"); // window
name // "giin"

var obj = {
  method: fn
};
obj.method("lee"); // {method: fn, name: "lee"}
new fn("park"); // fn {name: "park"}
```

위에서 fn의 호출 객체가 window라서 window.name = "giin"가 실행된다. 그런데 window가 글로벌 객체이므로 name 만으로 값 호출이 가능해진다.

**call, apply, bind** 는 함수 객체가 가진 메서드이다. 이를 이용해서 this가 가리키는 객체를 변경할 수 있다.

```javascript
function fn(age) {
  this.age = age;
}
var obj = {};

fn.call(obj, 10);
obj // {age: 10}

fn.apply(obj, [10]); // apply는 함수 인자를 배열로 받는다.
fn.bind(obj, 10); // fn. bind는 함수를 실행시키는 대신 this가 변경된 함수를 반환한다.
```

화살표 함수의 this값은 항상 함수가 만들어진 시점의 this와 동일하다. call, apply, bind를 사용해도 this 객체를 변경할 수 없다.

```javascript
var arrow = () => this;
arrow(); // window

var obj = {method: arrow};
obj.method(); // window

var obj2 = {
  outer: function () {
    return () => {
      this.name = "inner";
      return this;
    }
  }
}
obj2.outer()(); // {outer, name: "inner"}
```

## 3. 프로토타입

JS는 클래스가 아닌 프로토타입 기반의 객체지향언어이다. JS에서 객체를 만들기 위한 추상 선언문이 **생성자**, 생성자를 기반으로 만들어낸 초기화된 것은 **인스턴스 객체** 이다. 프로토타입은 모든 인스턴스가 공유할 수 있는 객체이며, 이 프로토타입에 접근할 수 있는 프로토타입 링크를 가지고 있다.

```javascript
function Giin() {
  this.dream = "천재 개발자";
}
Giin.prototype // {constructor: <Giin>} 여기서 <Giin>은 생성자 함수의 링크를 의미

var giin1 = new Giin(); // {dream: "천재 개발자"}
giin1.__proto__ // {constructor: <Giin>}
```

프로토타입은 **객체**이며, 모든 객체 인스턴스는 프로토타입이 될 수 있다. 생성자 함수를 선언했을 때 만들어지는 프로토타입 객체는 **Object의 인스턴스이다.**

### 1. 프로토타입 체인

객체는 자신이 가지고 있지 않지만 프로토타입이 가지고 있는 멤버를 참조 가능하며, 프로토타입을 동적으로 변경 가능하다. 또한 프로토타입의 멤버가 변경되면 연결되어있는 객체에서 멤버를 참조할 때 변경된 값이 참조된다.

```javascript
Giin.prototype.weapon = "javascript";
var giin2 = new Giin();

giin2 // {dream: "천재 개발자"}
giin2.weapon // "javascript"

Giin.prototype.weapon = "typescript"
giin2.weapon // "typescript"
```

객체에서 멤버를 참조할 때, 객체가 찾고자 하는 멤버를 가지고 있지 않을 경우 연결된 프로토타입 객체까지 찾아 들어간다. 이렇게 멤버를 찾아 프로토타입을 순회하는 것을 **프로토타입 체인**이라고 한다.

생성자 함수를 선언할 때 만들어지는 프로토타입은 Object의 인스턴스이다. 즉, **Object의 프로토타입을 참조**하고 있다. 모든 객체의 조상이 Object이고, 모든 객체가 Object의 멤버를 사용할 수 있는 이유이다. Object의 프로토타입의 프로토타입 링크는 **undefined**이고, 따라서 프로토타입 체인은 undefined를 만나면 탐색을 멈추고 undefined를 반환하게 된다.

단, `<Object name>.hasOwnProperty` 메서드로 객체의 멤버를 확인할 때는 프로타입 체인이 동작하지 않는다.

그리고, 인스턴스 객체에서 프로토타입 값을 변경하면 보안적으로 문제가 생길 수 있기 때문에 객체의 프로퍼티를 할당할 때는 프로토타입이 아니라 객체 자신의 프로퍼티가 변경된다.

```javascript
giin2.weapon = "JAVA"
Giin.prototype.weapon // typescript
giin2 // {dream: "천재 개발자", weapon: "JAVA"}
```

