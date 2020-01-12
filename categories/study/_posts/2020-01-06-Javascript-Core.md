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

> ***+ &alpha;. &&과 || 연산자를 이용한 조건문***<br />var something = a || 1; _  a가 truthy하면 something은 a, falsy하면 1이 된다.<br />a && doSomething(); _ a가 truthy하면 doSomething() 함수 실행.

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
giin2.langauge // "javascript"

Giin.prototype.langauge = "typescript"
giin2.langauge // "typescript"
```

객체에서 멤버를 참조할 때, 객체가 찾고자 하는 멤버를 가지고 있지 않을 경우 연결된 프로토타입 객체까지 찾아 들어간다. 이렇게 멤버를 찾아 프로토타입을 순회하는 것을 **프로토타입 체인**이라고 한다.

생성자 함수를 선언할 때 만들어지는 프로토타입은 Object의 인스턴스이다. 즉, **Object의 프로토타입을 참조**하고 있다. 모든 객체의 조상이 Object이고, 모든 객체가 Object의 멤버를 사용할 수 있는 이유이다. Object의 프로토타입의 프로토타입 링크는 **undefined**이고, 따라서 프로토타입 체인은 undefined를 만나면 탐색을 멈추고 undefined를 반환하게 된다.

단, `<Object name>.hasOwnProperty` 메서드로 객체의 멤버를 확인할 때는 프로타입 체인이 동작하지 않는다.

그리고, 인스턴스 객체에서 프로토타입 값을 변경하면 보안적으로 문제가 생길 수 있기 때문에 객체의 프로퍼티를 할당할 때는 프로토타입이 아니라 객체 자신의 프로퍼티가 변경된다.

```javascript
giin2.langauge = "JAVA"
Giin.prototype.langauge // typescript
giin2 // {dream: "천재 개발자", langauge: "JAVA"}
```

> ***Q. 프로토타입을 사용하는 이유?***
>
> 생성자에서 초기화하는 멤버는 인스턴스를 생성할 때마다 새로 생성된다. 이렇게 멤버를 중복 생성하면 인스턴스를 생성할 때마다 불필요한 비용이 발생한다.
>
> 따라서 인스턴스마다 고유의 값을 가지거나 자주 변경되는 멤버는 변경될 때 인스턴스의 멤버로 추가되므로 생성자에서 초기화하는 것이 좋다.
>
> ```javascript
> function Giin() {
>   this.method = function(){};
> }
> 
> var giin1 = new Giin();
> var giin2 = new Giin();
> 
> giin1.method == giin2.method // false
> ```
>
> 프로토타입에 할당하는 멤버는 단 한번만 생성되고, 해당 멤버의 참조를 모든 인스턴스가 공유한다. 잘 변경되지 않고 모든 인스턴스가 동일한 값을 가지는 메서드를 작성할 때는 프로토타입을 사용한다.
>
> ```javascript
> function Giin(){}
> Giin.prototype.method = function(){};
> 
> var giin1 = new Giin();
> var giin2 = new Giin();
> 
> giin1.method == giin2.method // true
> ```

### 2. 상속

생성자를 상속하려면 apply 메서드를 사용한다.

```javascript
function Giin() {
  this.dream = "천재 개발자";
}

function Giin2() {
  Giin.apply(this);
  this.age = 100;
}

var giin = new Giin2(); // {dream: "천재 개발자", age: 100}
```

프로토타입을 상속하려면 **생성자의 프로토타입의 프로토타입 링크**에 상속할 프로토타입을 연결해준다.

```javascript
Giin2.prototype.__proto__ = Giin.prototype
```

한 객체는 하나의 프로토타입만 상속받을 수 있다. (다중상속 불가능)

**instanceof** 연산자를 사용하면 좌변에 주어진 객체의 프로토타입 체인에 우변에 주어진 객체 원형의 프로토타입이 존재하는지 확인하게 된다.

```javascript
giin instanceof Giin2
giin instanceof Giin
```

### 3. Class

JS에 <span class="es6">Class</span> 는 ES6에 추가된 키워드이다. 실제 구현 방식은 **프로토타입**이고, 편하게 객체를 작성할 수 있도록 생긴 신택스 슈거이다.

```javascript
class Giin {
  constructor() {
    this.dream = "천재 개발자";
  }
  // 메서드들은 프로토타입에 등록된다.
  method() {
    return this.dream;
  }
}

class Giin2 exrtends Giin {
  constructor() {
    super(); // super 메서드로 부모 객체의 생성자 호출
    this.age = 100;
  }
}
```

## 4. 스코프

함수 안에서 변수를 선언하면 바깥에서 참조 불가능한 지역 변수가 된다. 이런 변수의 유효범위를 **스코프**라고 한다. 또한 <span class="es6">es6에서 추가된 **let**과 **const** 키워드</span>로 조건문, 반복문 블록 내부에서 변수를 선언하면 바깥에서 참조 불가능한 지역 변수가 된다.

함수 내부에서는 함수 바깥에서 선언된 변수에 접근이 가능하다. 하지만 함수 내에 같은 이름의 지역변수가 있다면 해당 지역 변수가 우선시 된다. 만일 상위 스코프에 찾는 변수가 없다면 전역 스코프에 도달할 때까지 바깥쪽 스코프를 탐색하게 되는데 이를 **스코프 체인**이라고 한다.

### 1. 정적 스코프 (lexical scope)

**스코프**는 함수를 호출할 때가 아니라 **선언**할 때 일어난다.

```javascript
var name = 'giin';
function log() {
  console.log(name);
}

function wrapper() {
  name = 'kiin';
  log();
}
wrapper(); // kiin
```

위의 코드에서 결과는 kiin이다. log에서 바라보고 있는 name은 전역 스코프에 선언되어있고, 그 값을 wrapper에서 선언할 때 바꾸었기 때문이다.

```javascript
var name = 'giin';
function log() {
  console.log(name);
}

function wrapper() {
  var name = 'kiin';
  log();
}
wrapper(); // giin
```

이번에는 wrapper 함수 선언 안에서 ***var name*** 을 다시 선언한 경우이다. 스코프는 함수를 **선언**할 때 생기기 때문에 log안에 name은 wrapper 안의 지역변수 name이 아니라 전역변수 name을 가리키고 있는 것이다. 이런 것을 lexical scope라고 한다.

다시 말해서 lexical scope는 함수를 처음 선언하는 순간 함수 내부의 변수는 자기 스코프로부터 가장 가까운 곳(상위 범위에서)에 있는 변수를 계속 참조하게 된다. 위의 예시에서는 log 함수안에 name 변수는 선언 시 가장 가까운 전역변수 name을 참조하게 된다. 그래서 wrapper 안에서 log를 호출해도 지역변수 name='kiin'를 참조하는게 아니라 그대로 전역변수 name의 값인 giin이 나오는 것이다.

해결방법은 전역 변수 대신 한 번 함수 안에 넣어 지역변수로 만들거나, 객체 안의 속성으로 만들면 된다.

```javascript
var obj = {
  x: 'local',
  y: function() {
    alert(this.x);
  }
}
```

이렇게 하면 `obj.x`, `obj.y()` 이렇게 접근해야 하기 때문에 다른 사람과 섞일 염려가 없다. 이런 방법을 **네임스페이스**를 만든다고 표현한다. obj라는 고유 네임스페이스를 만들어서 겹치지 않게 하는 것이다. 대부분의 라이브러리가 네임스페이스를 사용하고 있다. naver는 jindo, facebook은 FB, jquery는 jQuery(또는 $).

하지만 위의 방법은 코드 밑에 스크립트를 추가해서 누군가 고의적으로 x, y를 바꿀 수 있다. obj를 통째로 바꾸지 않더라도 밑에 `obj.x = 'hacked';` 라고 추가하면 obj.y();를 했을 때 local대신 hacked가 alert 됩니다. 그것을 방지하는 방법은 다음과 같다.

```javascript
var another = function() {
  var x = 'local';
  function y() {
    alert(x);
  }
  return { y: y };
}
var newScope = another();
```

조금 복잡하지만, `another();` 하는 순간 return에 의해 `{ y: function() { alert(x) }};` 가 newScope	에 저장된다. 그러면 newScope라는 네임스페이스를 통해서 y에 접근할 수 있지만, x는 접근할 수 없다. 위처럼 함수로 감싼 후 **return**을 통해 공개할 변수(y)만 공개하고 비공개할 변수(x)는 비공개하는 방법을 취할 수 있다.

위의 코드를 간단하게 바꾸면

```javascript
var newScope = (function() {
  var x = 'local';
  return {
    y: function() {
      alert(x);
    }
  };
})();
```

처럼 짝성할 수 있다. another같은 변수를 한 번 거치는 대신 바로 newScope에 넣는 방법이다. 여기서 `(function() {})();` 구문은 **IIFE(즉시 호출 함수 표현식)**이라고도 하고, **모듈 패턴**이라고도 하는데 함수를 선언하자마자 실행시키는 표현 방법이다. 많은 라이브러리가 이 구문을 활용해서 비공개 변수가 없는 자바스크립트에 **비공개 변수** 기능을 사용하고 있다.

***참고 : <a href="https://www.zerocho.com/category/Javascript/post/5740531574288ebc5f2ba97e">제로초 - javascript scope</a>***

### 2. 실행 컨텍스트

브라우저에서 처음으로 스크립트를 로딩해서 실행하는 순간 모든 것을 포함하는 **전역 컨텍스트**가 생긴다. 모든 것을 관리하는 환경이며 페이지가 종료될 때까지 유지된다. 또한 **함수 컨텍스트**는 함수를 호출할 때마다 생기는 컨텍스트이다.

- 전역 컨텍스트 생성 후, 함수가 호출될 때마다 컨텍스트가 생긴다.
- 컨텍스트가 생길 때 **변수객체(arguments, variable), scope, chain, this**가 생성된다.
- 컨텍스트 생성 후 함수가 실행되는데, 사용되는 변수들은 변수 객체 안에서 값을 찾고, 없다면 스코프 체인을 따라 올라가며 찾는다. (**스코프** 맨 처음 설명)
- 함수 실행이 마무리되면 해당 컨텍스트는 사라진다 (클로저 제외). 페이지가 종료되면 전역 컨텍스트가 사라진다.

#### 전역 컨텍스트

전역 컨텍스트가 생성된 후 두 번째 원칙에 따라 변수객체, scope chain, this가 들어온다. 전역 컨텍스트는 **arguments**가 없고, variable은 해당 스코프의 변수들이다. **scope chain(자신과 상위 스코프들의 변수객체)**은 자기 자신인 전역 변수객체이다. **this**는 따로 설정되어 있지 않으면 window이다. this를 바꾸는 방법은 **new** 를 호출하거나 함수에 다른 this 값을 **bind** 하면 된다.

```javascript
'전역 컨텍스트': {
  변수객체: {
    arguments: null,
    variable: ['name', 'wrapper', 'log']
  },
  scopeChain: ['전역 변수객체'],
  this: window
}
```

설명한 내용을 객체 형식으로 표현한 것이다. 이제 이 코드를 위해서부터 실행하면 wrapper와 log는 **호이스팅** 때문에 선언과 동시에 대입이 된다. 그 후 variable의 name에 'giin'이 대입된다.

```javascript
variable: [{ name: 'giin' }, {wrapper: Function}, {log: Function}]
```

#### 함수 컨텍스트

위에 적었던 코드에서 `wrapper();` 하는 순간 새로운 함수 컨텍스트인 wrapper 함수 컨텍스트가 생긴다.

```javascript
'wrapper 컨텍스트': {
  변수객체: {
    arguments: null,
    variable: ['name'], //초기화 후 [{name: 'kiin'}]이 된다.
  },
  scopeChain: ['wrapper 변수객체', '전역 변수객체'],
  this: window
}
```

wrapper을 호출한 후 위에서부터 차례대로 실행되는데, variable의 name에 'kiin'을 대입해주고 나서 log()를 실행한다. 그러면 또 다시 함수 컨텍스트인 log가 생기고, 그 안에서 console.log(name)이 실행된다. 이 때 name은 log 컨텍스트 -> wrapper 컨텍스트 -> 전역 컨텍스트처럼 올라가면서 name의 값을 찾을 수 있을 때 까지 올라가는 형식으로 진행된다. 이러한 것을 **scope chain** 이라고 한다.

#### 호이스팅

호이스팅이란 변수를 선언하고 초기화했을 때 선언 부분이 최상단으로 끌어올려지는 현상을 의미한다. (단, 초기화 또는 대입부분은 그대로 남아있다)

```javascript
var a = 1;
function fn() {
  a = 2;
  var a = 3;
}

// 함수 호이스팅 적용된 모습
function fn() {
  var a = undefined;
  a = 2;
  a = 3;
}
```

```javascript
function outer() {
  console.log(inner1, inner2);
  
  function inner1() {
    var inner2 = function() {};
  }
}

// 함수 호이스팅 적용된 모습
function outer() {
  var inner1 = function() {};
  var inner2;
  console.log(inner1, inner2);
  inner2 = function() {};
}
```

또한 **함수 표현식**은 변수 선언만 끌어올려지고, **함수 선언식**일 경우는 식 자체가 통째로 끌어올려진다.

이러한 호이스팅은 에러를 발생시킬 소지가 있고, 코드를 읽기 어렵게 만드는 요인이기 때문에 호이스팅이 발생하지 않도록 모든 선언을 코드의 첫머리에 위치하도록 하는 습관을 들이는 것이 좋다. 또한 **let, const, class** 키워드를 쓰면 호이스팅은 발생하지만 접근은 불가능하다.

```javascript
var a = 1;

function fn() {
  a = 3;
  let a = 2;
}
fn(); // Cannot access 'a' before initialization
```

위에서 var로 선언한 변수가 호이스팅될 경우 그 값은 undefined가 되지만, **let, const, class** 로 선언한 변수가 호이스팅되면 해당 변수가 초기화되기 전에 접근할 때 오류가 발생하게 된다. 이렇게 변수가 초기화되기 전 오류가 발생하는 영역을 **Temporal Dead Zone(TDZ)** 라고 한다.

## 5. 클로저

함수의 외부 스코프는 함수가 **선언될 때** 결정된다.

```javascript
var a = 1;

functiom outer() {
  var a = 2;
  return function inner() {
    return a;
  }
}
var inner = outer();
inner(); // 2
```

위의 코드를 실행하면 outer 함수의 컨텍스트는 사라졌지만, inner에서 outer 컨텍스트의 변수를  가지고 있다. 함수가 선언되었을 때의 실행 환경은 해당 컨텍스트가 종료된 후에도 메모리상에 남아 접근 가능하게 된다. 이렇게 함수의 외부 변수 정보를 저장하는 것을 **클로저**라고 한다.

이렇게 생성된 클로저는 해당 함수가 제거되기 전까지 메모리상에 계속 남아있게 된다. 성능 저하의 원인이 될 수 있기 때문에 클로저를 이용하는 함수는 사용후 제거하는 것이 바람직하다.

<img width="703" alt="스크린샷 2020-01-12 오후 6 49 18" src="https://user-images.githubusercontent.com/27988544/72217154-126c8d00-356e-11ea-9dd6-c2d80960c1b6.png">
참조 - 서동유님 자료
{:.figure}

위의 코드를 도식화 하면 위와 같다.

클로저는 함수 객체가 아닌 컨텍스트에 의존한다. 컨텍스트는 함수가 호출될 때마다 새로 생성되며, 하나의 함수에서 파생되었더라도 각각의 컨텍스트는 독립적인 클로저를 생성한다.

<img width="608" alt="스크린샷 2020-01-12 오후 7 03 41" src="https://user-images.githubusercontent.com/27988544/72217171-46e04900-356e-11ea-9ad1-a55902df98fb.png">
참조 - 서동유님 자료
{:.figure}



