코어 자바스크립트를 읽으면서 공부한 내용을 저의 입맛에 맞춰 정리하였습니다.
혹시 이글을 읽게 되시면 잘못된 내용이 있을 수 있으니 비판적인 시각으로 읽으시는걸 추천드립니다.

![img](https://blog.kakaocdn.net/dn/cmElE7/btrTth1WTEp/sc1nc3MC5LobUX4oTOWV2k/img.jpg)



## 클로저

### 클로저

> **클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수를 B를 외부로 전달한 경우 A의 실행컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상 입니다**



클로저는 여러 함수형 프로그래밍 언어에서 등장하는 보편적인 특성입니다. 자바스크립트의 고유의 개념이 아니라서 ECMAScript 명세에서도 클로저의 정의를 다루지 않고 있고, 다양한 문헌에서 클로저를 다르게 정의하고 있습니다



MDN에서는 클로저에 대해 A closure is the combination of a function and the lexcial environment within which that function was declared 라고 소개하고 있습니다.

이를 직역했을 때, "클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 상호 관계에 따른 현상"입니다.

클로저를 이해하기 위해 첫 번째로 외부 함수의 변수를 참조하는 내부함수 코드를 이해해야합니다.

```js
var outer = function() {
  var a = 1;
  var inner = function() {
    console.log(++a);
  };
  inner();
};
outer();
```

```js
var outer = function() {
  var a = 1;
  var inner = function() {
    return ++a;
  };
  return inner();
};
var outer2 = outer();
console.log(outer2); // 2
```

위 코드는 외부함수 outer의 변수 a를 내부함수 inner가 스코프 채인을 활용하여 참조하고 있습니다. 하지만, 이 함수는 outer 함수가 종료될 때, 변수 a와 inner함수를 참조하는 값이 없기 때문에 가비지 컬렉터의 수집 대상이 됩니다. 즉, 다시 outer를 실행한다 해도 당연히 a = 1이 됩니다.



```js
var outer = function() {
  var a = 1;
  var inner = function() {
    return ++a;
  };
  return inner;
};
var outer2 = outer();
console.log(outer2()); // 2
console.log(outer2()); // 3
```

하지만 위의 코드는 결과가 다릅니다. outer함수를 실행할 때 마다 a값이 증가됨을 알 수 있습니다. 그 이유는 outer2가 inner 함수를 참조하고 있기 때문입니다. 

.inner 함수의 실행컨텍스트의 EnvironmentRecord에는 수집할 정보가 없습니다. 하지만 outer-EnvironmentReference에는 inner 함수가 선언된 위치의 LE가 참조 복사됩니다. inner 함수는 outer 함수 내부에서 선언됐으므로, outer 함수의 LexicalEnvironment가 담길 것입니다. 이제, 스코프 체이닝에 따라 outer에서 선언한 변수 a에 접근해서 1만큼 증가시킨 후 그 값인 2를 반환하고, inner 함수의 실행 컨텍스트가 종료됩니다.

가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있다면 그 값은 수집 대상에 포함시키지 않습니다. 외부함수인 outer의 실행이 종료되더라도 내부 함수인 inner 함수는 언젠가 outer2를 실행함으로써 호출될 가능성이 있는 것입니다.



클로저는 어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상입니다.

첫번째, 두번째 코드는 outer LE의 포함된 변수가 모두 가비지 컬렉팅 대상에 포함된 반면 마지막 코드는 변수 a가 가비지 콜렉터 대상에서 제외 됐씁니다.

이처럼 실행 콘텍스트가 종료된 후에도 LE가 가비지 컬렉터의 수집 대상에서 제외되는 경우는 세번쨰 코드처럼 지역 변수를 참조하는 내부함수가 외부로 전달된 경우가 유일합니다.

즉, "어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상"이란 "**외부 함수의 LE가 가비지 컬렉팅 되지 않는 현상"**을 말합니다

정리해보면, **클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수를 B를 외부로 전달한 경우 A의 실행컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상 입니다**



### 클로저와 메모리 관리

'메모리 누수'라는 표현은 개발자의 의도와 달리 어떤 값의 참조 카운트가 0이 되지 않아 GC(Garbage Collector)의 대상이 되지 않는 경우 입니다. 클로저를 적절히 활용하는 것은 개발자의 의도로 인해 변수를 참조하는 것이므로 '누수'보다는 메모리 '소모'와 어울리는 표현입니다

메모리를 관리하기 위해서는 더 이상 클로저를 활요하지 않을 때 null이나 undefined를 식별자에 대입하면 됩니다.

```js
// (1) return에 의한 클로저의 메모리 해제
var outer = (function() {
  var a = 1;
  var inner = function() {
    return ++a;
  };
  return inner;
})();
console.log(outer());
console.log(outer());
outer = null; // outer 식별자의 inner 함수 참조를 끊음
```



### 클로저 활용 사례

#### 1. 콜백함수 내부에서 외부 데이터를 사용하고자 할 때

```js
var fruits = ['apple', 'banana', 'peach'];
var $ul = document.createElement('ul'); // (공통 코드)

fruits.forEach(function(fruit) {
  // (A)
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListener('click', function() {
    // (B)
    alert('your choice is ' + fruit);
  });
  $ul.appendChild($li);
});
document.body.appendChild($ul);
```

$li는 외부함수 식별자입니다. 원래라면 forEach 함수가 끝이 났을 때, GC가 외부함수의 변수들을 수거해 가야하지만, 내부함수인 B가 fruit에 대해 참조하고 있기 때문에 GC의 대상에서 제외되어 계속 참조가능 합니다.

B함수를 하나의 함수로 만들어 위 코드를 바꾸면 아래와 같습니다.

```js
var fruits = ['apple', 'banana', 'peach'];
var $ul = document.createElement('ul');

var alertFruit = function(fruit) {
  alert('your choice is ' + fruit);
};
fruits.forEach(function(fruit) {
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListener('click', alertFruit);
  $ul.appendChild($li);
});
document.body.appendChild($ul);
alertFruit(fruits[1]);
```

하지만 이렇게 바꾼 경우에는 alert가 [object MouseEvent]를 출력할 것입니다. 그 이유는 addEventListenr는 항상 첫번째 인자로 '이벤트 객체'를 주입하기 때문입니다.

이를 이전에 배운 bind로 해결하면 되지만 this값이 사라지게 된다는 단점이 생깁니다.

```js
var fruits = ['apple', 'banana', 'peach'];
var $ul = document.createElement('ul');

var alertFruit = function(fruit) {
  alert('your choice is ' + fruit);
};
fruits.forEach(function(fruit) {
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListener('click', alertFruit.bind(null, fruit));
  $ul.appendChild($li);
});
document.body.appendChild($ul);
```

이런 문제를 해결하기 위해서는 고차함수를 활용할 필요가 있습니다.

```js
var fruits = ['apple', 'banana', 'peach'];
var $ul = document.createElement('ul');

var alertFruitBuilder = function(fruit) {
  return function() {
    alert('your choice is ' + fruit);
  };
};
fruits.forEach(function(fruit) {
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListener('click', alertFruitBuilder(fruit));
  $ul.appendChild($li);
});
document.body.appendChild($ul);
```

클릭 이벤트가 발생하면 비로소 이 함수의 실행 컨텍스트가 열리면서 alertFruitBuilder의 인자로 넘어온 fruit를 outerEnvironmentReferce에 의해 참조할 수 있게 됩니다. 즉 alertFruitBuilder의 실행 결과로 반환된 함수에는 클로저가 존재합니다.



#### 2. 접근 권한 제어(정보 은닉)

정보은닉은 어떤 모듈의 내부 로직에 대해 외부로의 노출을 최소화해서 모듈간의 결합도를 낮추고 유연성을 높이고자 하는 현대 프로그래밍 언어의 중요한 개념 중 하나입니다.

자바스크립트는 기본적으로 변수 자체에 접근 권한을 직접 부여하도록 설계돼 있지 않습니다. 하지만 클로저를 이용하면 함수 차원에서 public한 값과 private한 값을 구분하는 것이 가능합니다.

즉, 외부에 제공하고자 하는 정보들을 모아서 return 하고, 내부에서만 사용할 정보들을 return하지 않는 것으로 접근 권한 제어가 가능해집니다.

```js
var car = {
  fuel: Math.ceil(Math.random() * 10 + 10), // 연료(L)
  power: Math.ceil(Math.random() * 3 + 2), // 연비(km/L)
  moved: 0, // 총 이동거리
  run: function() {
    var km = Math.ceil(Math.random() * 6);
    var wasteFuel = km / this.power;
    if (this.fuel < wasteFuel) {
      console.log('이동불가');
      return;
    }
    this.fuel -= wasteFuel;
    this.moved += km;
    console.log(km + 'km 이동 (총 ' + this.moved + 'km)');
  },
};
```

위와 같은 객체를 만들었을 때, fuel과 power, moved를 직접 할당하면서 코드를 수정할 수 있습니다. 이를 클로저를 활용하여 숨긴다면,

```js
var createCar = function() {
  var fuel = Math.ceil(Math.random() * 10 + 10); // 연료(L)
  var power = Math.ceil(Math.random() * 3 + 2); // 연비(km / L)
  var moved = 0; // 총 이동거리
  return {
    get moved() {
      return moved;
    },
    run: function() {
      var km = Math.ceil(Math.random() * 6);
      var wasteFuel = km / power;
      if (fuel < wasteFuel) {
        console.log('이동불가');
        return;
      }
      fuel -= wasteFuel;
      moved += km;
      console.log(km + 'km 이동 (총 ' + moved + 'km). 남은 연료: ' + fuel);
    },
  };
};
var car = createCar();
```

fuel과 power는 비공개 멤버로 직접 접근 할 수 없게 되고 외부에서는 run만 실행 할 수 있도록 됩니다. 물론 run 함수를 여전히 다른 내용으로 덮어씌우는게 가능합니다. 이 것 까지 막기위해서는 return하기전에 변경 할 수 없게 끔 조치를 취해주어야합니다

```js
var createCar = function() {
  var fuel = Math.ceil(Math.random() * 10 + 10); // 연료(L)
  var power = Math.ceil(Math.random() * 3 + 2); // 연비(km / L)
  var moved = 0; // 총 이동거리
  var publicMembers = {
    get moved() {
      return moved;
    },
    run: function() {
      var km = Math.ceil(Math.random() * 6);
      var wasteFuel = km / power;
      if (fuel < wasteFuel) {
        console.log('이동불가');
        return;
      }
      fuel -= wasteFuel;
      moved += km;
      console.log(km + 'km 이동 (총 ' + moved + 'km). 남은 연료: ' + fuel);
    },
  };
  Object.freeze(publicMembers);
  return publicMembers;
};
var car = createCar();
```



#### 3. 부분 적용 함수

부분적용함수란 n개의 인자를 받는 함수에 미리 m개의 인자만 넘겨 기억시켰다가, 나중에 n-m개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수입니다. bind란 함수가 있긴 하지만 bind는 this를 항상 넘겨주어야 하기 때문에 this를 넘겨주고 싶지 않을 때 클로저를 활용하여 구현할 수 있습니다

```js
var partial = function() {
  var originalPartialArgs = arguments;
  var func = originalPartialArgs[0];
  if (typeof func !== 'function') {
    throw new Error('첫 번째 인자가 함수가 아닙니다.');
  }
  return function() {
    var partialArgs = Array.prototype.slice.call(originalPartialArgs, 1);
    var restArgs = Array.prototype.slice.call(arguments);
    return func.apply(this, partialArgs.concat(restArgs));
  };
};

var add = function() {
  var result = 0;
  for (var i = 0; i < arguments.length; i++) {
    result += arguments[i];
  }
  return result;
};
var addPartial = partial(add, 1, 2, 3, 4, 5);
console.log(addPartial(6, 7, 8, 9, 10)); // 55

var dog = {
  name: '강아지',
  greet: partial(function(prefix, suffix) {
    return prefix + this.name + suffix;
  }, '왈왈, '),
};
dog.greet('입니다!'); // 왈왈, 강아지입니다.
```



실무에서 자주 사용하는 부분 함수는 디바운스가 있습니다. 디바운스는 짧은 시간 동안 동일한 이벤트가 많이 발생한 경우 이를 전부 처리하지 않고 처음 또는 마지막에 발생한 이벤트에 대해 한 번만 처리하는 것으로 프론트엔드 성능 최적화에 큰 도움을 주는 기능 중 하나입니다.

```js
var debounce = function(eventName, func, wait) {
  var timeoutId = null;
  return function(event) {
    var self = this;
    console.log(eventName, 'event 발생');
    clearTimeout(timeoutId);
    timeoutId = setTimeout(func.bind(self, event), wait);
  };
};

var moveHandler = function(e) {
  console.log('move event 처리');
};
var wheelHandler = function(e) {
  console.log('wheel event 처리');
};
document.body.addEventListener('mousemove', debounce('move', moveHandler, 500));
document.body.addEventListener(
  'mousewheel',
  debounce('wheel', wheelHandler, 700)
);
```



#### 4. 커링함수

커링함수란 여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 것을 말합니다.

커링은 한 번에 하나의 인수만 전달하는 것을 원칙으로 합니다. 중간 과정상의 함수를 실행한 결과는 그 다음 인자를 받기 위해 대기만 할 뿐으로, 마지막 인자가 전달되기 전까지는 원본 함수가 실행 되지 않습니다.

부분 적용 함수는 여러 개의 인자를 전달할 수 있고, 실행 결과를 재실행할 때 원본 함수가 무조건 실행 됩니다.

```js
var curry3 = function(func) {
  return function(a) {
    return function(b) {
      return func(a, b);
    };
  };
};

var getMaxWith10 = curry3(Math.max)(10);
console.log(getMaxWith10(8)); // 10
console.log(getMaxWith10(25)); // 25

var getMinWith10 = curry3(Math.min)(10);
console.log(getMinWith10(8)); // 8
console.log(getMinWith10(25)); // 10
```

이를 화살표 함수로 처리한다면 아래와 같습니다.

```js
var curry5 = func => a => b => c => d => e => func(a,b,c,d,e)
```



### 정리

클로저란 어떤 함수에서 선언한 변수를 참조하는 내부함수를 외부로 전달할 경우, 함수의 실행 컨텍스트가 종료된 후에도 해당 변수가 사라지지 않는 현상입니다.

내부함수를 외부로 전달하는 방법에는 함수를 return 하거나 콜백으로 전달하는 경우도 포함됩니다.

클로저는 본질이 메모리를 계속 차지하는 개념이므로 더는 사용하지 않게 된 클로저에 대해서는 메모리를 차지하지 않도록 관리해줄 필요가 있습니다.

