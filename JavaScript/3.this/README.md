코어 자바스크립트를 읽으면서 공부한 내용을 저의 입맛에 맞춰 정리하였습니다.
혹시 이글을 읽게 되시면 잘못된 내용이 있을 수 있으니 비판적인 시각으로 읽으시는걸 추천드립니다.

![img](https://blog.kakaocdn.net/dn/cmElE7/btrTth1WTEp/sc1nc3MC5LobUX4oTOWV2k/img.jpg)



## this

자바스크립트에서 this는 실행컨텍스트가 생성될 때(VariableEnvironment와 LexicalEnvironment)와 함께 결정됩니다. 컨텍스트는 함수를 호출할 때 생성되므로, 바꿔 말하면 this는 함수를 호출할 때 결정된다고 할 수 있겠습니다.

즉, this는 상황별로 값이 달라질 수 있습니다.



### 전역공간에서의 this

전역공간에서 this는 전역 객체를 가리킵니다.

전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 가지고 있습니다. 브라우저 환경에서 전역객체는 window이고 Node.js환경에서는 global 입니다.



#### + 참고) 전역변수와 전역객체

```js
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1
```



전역공간에서 선언한 변수 a에 1을 할당하면 전역 객체와 전역객체를 가르키는 this에서 모두 1이 출력됩니다.

이 이유는 **자바스크립트의 모든 변수는 특정 객체의 프로퍼티로서 동작하기 때문입니다.**

그렇다면 객체의 프로퍼티인 a를 그냥 호출하여도 1이 나오는 이유는 무엇일까요? 이는 변수 a에 접근하고자 하면 스코프체인에서 a를 검색했다가 가장 마지막에 도달하는 전역 스코프의 LE 즉 전역객체에서 해당 프로퍼티 a를 발견해서 그 값을 반환하기 때문입니다.

또한 var로 변수를 선언하는 대신 window에 프로퍼티를 직접 할당하더라도 같은 결과를 얻을 수 있습니다. 하지만, 한가지 차이점은 delete 연산자를 활용하면, window에 프로퍼티를 직접 할당한 경우 삭제가 되지만,  var로 할당한 경우에는 그렇지 않습니다. 그 이유는 전역변수를 선언하면 자바스크립트 엔진이 이를 자동으로 전역객체의 프로퍼티로 할당하면서 추가적으로 해당 프로퍼티의 configurable 속성(변경 및 삭제 가능성)을 false로 정의하기 때문입니다



### 메서르소 호출할 때 그 메서드 내부에서의 this

#### 함수 vs 메서드

함수와 메서드를 구분하는 유일한 차이는 **독립성**입니다. 함수는 그 자체로 독립적인 기능을 수행하는 반면, 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행합니다.

JS에서 가장 간단한 구분법은 '.'의 여부입니다. 함수앞에 . 이 있다면 메서드, 그렇지 않다면 함수입니다.



#### 메서드 내부에서 this

**this에는 호출한 주체에 대한 정보가 담깁니다.**

메서드로서 함수를 호출하는 경우 호출한 주체는 객체입니다. 즉 객체가 곧 this가 되는 것입니다.



#### 함수 내부에서의 this

this에는 호출한 주체에 대한 정보가 담깁니다. 하지만 함수로서 호출하는 것은 호출 주체를 명시하지 않았기 때문에 호출 주체의 정보는 알 수 없습니다.

**결국  this가 지정되지 않았기 때문에 this는 전역객체를 바라봅니다.**

이는 아래에 예처럼, 메서드안에서 함수를 호출하는 경우에도 함수는 전역객체를 바라보게 됩니다.

```
var obj1 = {
  outer: function() {
    console.log(this); // (1)
    var innerFunc = function() {
      console.log(this); // (2) (3)
    };
    innerFunc();

    var obj2 = {
      innerMethod: innerFunc,
    };
    obj2.innerMethod();
  },
};
obj1.outer();
```

(1) : obj1 (2) : window (3) :obj2



#### 메서드 내부 함수에서 this를 우회하는 방법

메서드 내부 함수에서도 this를 활용한 객체접근이 필요한 경우가 있을 수 있습니다. 

1. 변수를 활용

```js
var obj = {
  outer: function() {
    console.log(this); // (1) { outer: f }
    var innerFunc1 = function() {
      console.log(this); // (2) Window { ... }
    };
    innerFunc1();

    var self = this;
    var innerFunc2 = function() {
      console.log(self); // (3) { outer: f }
    };
    innerFunc2();
  },
};
obj.outer();
```

- self, _this를 활용하여(이름은 상관없음) 메서드 안에서 this를 저장한후 활용하는 방법입니다.



2. this를 바인딩하지 않는 함수

   ES6의 문법 중 하나인 arrow function을 도입하는 것입니다. 에로우함수는 실행컨텍스트를 생성할 때 this바인딩 과정 자체가 빠지기 때문에 상위 스코프의 this를 그대로 활용 할 수 있습니다.



### 콜백함수 호출 시 그 내부에서의 this

콜백함수도 함수이기 때문에 기본적으로 전역 객체를 참조하지만, 함수에서 콜백함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 됩니다.

```js
setTimeout(function() {
  console.log(this);
}, 300); // (1)

[1, 2, 3, 4, 5].forEach(function(x) {
  // (2)
  console.log(this, x);
});

document.body.innerHTML += '<button id="a">클릭</button>';
document.body.querySelector('#a').addEventListener('click', function(e) {
  // (3)
  console.log(this, e);
});
```

(1), (2)는 함수에서 콜백함수에게 this를 결정해주지 않기 때문에 전역객체를 참조하게 됩니다.

하지만 (3)의 경우에서는 addEventLister의 앞 객체를 this로 지정해 줍니다.





### 생성자 함수 내부에서의 this

생성자 함수란 어떤 공통된 성질을 지니는 객체들을 생성할 때 사용하는 함수 입니다. 프로그래밍적으로 '생성자'는 구체적인 인스턴스를 만들기 위한 일종의 틀입니다. 이 틀에는 클래스의 공통 속성들이 미리 준비돼 있고, 여기에 구체적인 인스턴스의 개성을 더해 개별 인스턴스를 만들 수 있습니다.

**자바스크립트는 함수에 생성자로서의 역할을 함께 부여했습니다.** new 명령어와 함꼐 함수를 호출하면 해당함수가 생성자로서 동작하게 됩니다. 

어떤 함수가 생성자 함수로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 됩니다.

생성자 함수를 new 명령어와 함께 호출하면 우선 생성자의 prototype 프로퍼티를 참조하는 proto라는 프로퍼티가 있는 객체를 만들고, 미리 준비된 공통 속성 및 개성을 해당 객체(this)에 부여합니다.

```js
var Cat = function(name, age) {
  this.bark = '야옹';
  this.name = name;
  this.age = age;
};
var choco = new Cat('초코', 7);
var nabi = new Cat('나비', 5);
console.log(choco, nabi);

/* 결과
Cat { bark: '야옹', name: '초코', age: 7 }
Cat { bark: '야옹', name: '나비', age: 5 }
*/
```



## 명시적으로 this를 바인딩 하는 방법

### call 메서드

```js
Funtion.prototype.call(thisArg[,arg1[,arg2[,...]]])
```

- call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령입니다.  이 때 call메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 합니다



### apply 메서드

```
Funtion.prototype.call(thisArg[,argsArray])
```

- call메서드와 기능적으로 완전히 동일합니다. 한가지 차이는 두번째 인자로 매개변수의 '배열'을 받습니다.



#### call, apply의 활용

1. 유사배열객체에 배열 메서드를 적용

```js
var obj = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3,
};
Array.prototype.push.call(obj, 'd');
console.log(obj); // { 0: 'a', 1: 'b', 2: 'c', 3: 'd', length: 4 }

var arr = Array.prototype.slice.call(obj);
console.log(arr); // [ 'a', 'b', 'c', 'd' ]
```

obj의 형태는 배열이지만 객체입니다. 즉 obj에는 push나 slice를 사용하지 않습니다. 하지만 call을 활용하여 

push 함수에 this를 obj로 바인딩 하고 매게변수 d를 넣어 값을 증가 시킬 수 있습니다.





2. NodeList에 배열메서드를 적용

```js
function a() {
  var argv = Array.prototype.slice.call(arguments);
  argv.forEach(function(arg) {
    console.log(arg);
  });
}
a(1, 2, 3);

document.body.innerHTML = '<div>a</div><div>b</div><div>c</div>';
var nodeList = document.querySelectorAll('div');
var nodeArr = Array.prototype.slice.call(nodeList);
nodeArr.forEach(function(node) {
  console.log(node);
});
```

위와 마찬가지로, NodeList역시 배열이 아니지만, slice를 활용하여 배열로 형변환 한 후 사용할 수 있습니다



**참고**

ES6부터 Array.from()을 통해 형변환을 간편하게 할 수 있습니다



3. 생성자 내부에서 다른 생성자를 호출

```js
function Person(name, gender) {
  this.name = name;
  this.gender = gender;
}
function Student(name, gender, school) {
  Person.call(this, name, gender);
  this.school = school;
}
function Employee(name, gender, company) {
  Person.apply(this, [name, gender]);
  this.company = company;
}
var by = new Student('보영', 'female', '단국대');
var jn = new Employee('재난', 'male', '구골');
```

- 생성자 내부에 this에는 해당 생성자의 proto가 this입니다. 이를 활용하여 이미 만들어진 생성자에 this 를 넣어 보내 중복을 줄일 수 있습니다.



4. 여러 인수를 묶어 하나의 배열로 전달하고 싶을 때

```js
var numbers = [10, 20, 3, 16, 45];
var max = (min = numbers[0]);
numbers.forEach(function(number) {
  if (number > max) {
    max = number;
  }
  if (number < min) {
    min = number;
  }
});
console.log(max, min); // 45 3
```

```js
var numbers = [10, 20, 3, 16, 45];
var max = Math.max.apply(null, numbers);
var min = Math.min.apply(null, numbers);
console.log(max, min); // 45 3
```

js의 Math.max / min은 인자를 배열로 받을 수 없습니다. 이를 apply를 활용하여 배열인 인수를 전달할 수 있습니다. 

이 역시 ES6의 구조분해연산자를 활용하면 매우 간편합니다



### bind메서드

```js
Function.prototype.bind(thisArg[,arg1[,arg2[,...]]])
```

ES5에 처음 추가된 기능으로 call과 비슷하지 않지만 함수를 즉시 호출하지 않고 넘겨 받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드입니다.

```js
var func = function(a, b, c, d) {
  console.log(this, a, b, c, d);
};
func(1, 2, 3, 4); // Window{ ... } 1 2 3 4

var bindFunc1 = func.bind({ x: 1 });
bindFunc1(5, 6, 7, 8); // { x: 1 } 5 6 7 8

var bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6, 7); // { x: 1 } 4 5 6 7
bindFunc2(8, 9); // { x: 1 } 4 5 8 9
```



bind를 활용해 this와 변수를 넘겨 준 이후로 함수호출 시 마다 this와 매게변수가 넘어가있는 것을 알 수 있습니다.

#### name 프로퍼트

bind메서드를 활용해 새로만든 함수는 name프로퍼티가 생성되며 어떤 원본함수에 bind됐는지 명시적으로 알려줍니다. 이는 apply와 call보다 코드 추적을 쉽게 도와줍니다



#### 상위 컨텍스트의 this를 내부함수나 콜백함수에 저장하기

bind를 활용하면 this를 전달할 때 더 수월하게 전달 할 수 있다.





### 화살표 함수의 예외사항

ES6부터 도입된 화살표 함수는 실행컨텍스트생성시 this바인딩 과정이 제외된다. 이 말은 this를 호출 할 때 스코프체인상 가장 가까운 this를 호출할 수 있어 call/ apply/ bind를 적용할 필요가 없어진다.



### 별도의 인자로 this를 받는 경우 (콜백 함수 내에서의 this)

콜백함수를 인자로 받는 메서드 중 일부는 추가로 this로 지정할 객체를 인자로 지정할 수 있습니다. thisArg값을 지정하면 콜백 함수 내부에서 this값을 원하는 대로 변경할 수 있습니다.

아래의 함수들을 메서드 내에서 사용할 경우 객체에 접근이 가능합니다.

```js
Array.prototype.forEach(callback[, thisArg])
Array.prototype.map(callback[, thisArg])
Array.prototype.filter(callback[, thisArg])
Array.prototype.some(callback[, thisArg])
Array.prototype.every(callback[, thisArg])
Array.prototype.find(callback[, thisArg])
Array.prototype.findIndex(callback[, thisArg])
Array.prototype.flatMap(callback[, thisArg])
Array.prototype.from(arrayLike[, callback[, thisArg]])
Set.prototype.forEach(callback[, thisArg])
Map.prototype.forEach(callback[, thisArg])
```



### 정리

this는 다음과 같은 규칙을 따릅니다

- 전역공간에서는 window / Node.js를 참조
- 함수를 메서드로서 호출한 경우 메서드 호출 주체
- 함수로 호출한 경우 전역 객체
- 콜백함수 내부에서의 this는 해당 콜백함수의 제어권을 넘겨받은 함수가 정의한 바에 따르며, 그렇지 않은 경우 전역객체를 참조
- 생성자 함수에서의 this는 생성될 인스턴스를 참조

명시적 바인딩

- call, apply메서드는 this를 명시적으로 지정
- .bind는 this및 매게변수를 지정하여 새로운 함수 생성
- 요소를 순회하면서 콜백함수를 반복호출하는 일부 메서드는 별도의 인자로 this를 받기도 함