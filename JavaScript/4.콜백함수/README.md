코어 자바스크립트를 읽으면서 공부한 내용을 저의 입맛에 맞춰 정리하였습니다.
혹시 이글을 읽게 되시면 잘못된 내용이 있을 수 있으니 비판적인 시각으로 읽으시는걸 추천드립니다.

![img](https://blog.kakaocdn.net/dn/cmElE7/btrTth1WTEp/sc1nc3MC5LobUX4oTOWV2k/img.jpg)



## 콜백 함수

간단히 말해서 콜백 함수는 다른 코드의 인자로 넘겨주는 함수입니다.

콜백함수는 다른 코드에게 인자로 넘겨줌으로써 그 **제어권**도 함께 위임한 함수입니다. 콜백 함수를 위임받은 코드는 자체적인 내부 로직에 의해 이 콜백 함수를 적절한 시점에 실행합니다.

```js
var count = 0;
var cbFunc = function() {
  console.log(count);
  if (++count > 4) clearInterval(timer);
};
var timer = setInterval(cbFunc, 300);

// -- 실행 결과 --
// 0  (0.3초)
// 1  (0.6초)
// 2  (0.9초)
// 3  (1.2초)
// 4  (1.5초)
```

위 코드는 setInterval  함수가  cbFunc의 제어권을 받아 0.3초마다 cbFunc을 실행합니다.



### 콜백함수에 this를 바인딩 하는 방법

1.  전통적인 방식 : self, _this를 활용

```js
var obj1 = {
  name: 'obj1',
  func: function() {
    var self = this;
    return function() {
      console.log(self.name);
    };
  },
};
var callback = obj1.func();
setTimeout(callback, 1000);
```

- 실제로 this를 사용하는 것도 아니며, 번거로운 코드가 되었습니다.



2. 함수 내부에서 this를 사용하지 않는 경우

```js
var obj1 = {
  name: 'obj1',
  func: function() {
    console.log(obj1.name);
  },
};
setTimeout(obj1.func, 1000);
```

- 위의 코드보다 훨신 간결해졌지만, 함수를 다른 객체에서 활용할 수 없게 됐습니다.



3. bind를 사용하는 경우

```js
var obj1 = {
  name: 'obj1',
  func: function() {
    console.log(this.name);
  },
};
setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = { name: 'obj2' };
setTimeout(obj1.func.bind(obj2), 1500);
```

- ES5의 bind를 활용하여 콜백함수의 인자에 bind로 this를 지정해 줄 수 있습니다.
- 이 방법은 코드 가독성과 재활용성 모두 만족할 만한 결과를 얻을 수 있습니다.



### 콜백 지옥과 비동기 제어

콜백 지옥은 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 가독성이 매우 떨어지는 현상으로, 이벤트나 서버 통신 같은 비동기 작업을 수행할 때 많이 일어나는 현상입니다

```js
setTimeout(
  function(name) {
    var coffeeList = name;
    console.log(coffeeList);

    setTimeout(
      function(name) {
        coffeeList += ', ' + name;
        console.log(coffeeList);

        setTimeout(
          function(name) {
            coffeeList += ', ' + name;
            console.log(coffeeList);

            setTimeout(
              function(name) {
                coffeeList += ', ' + name;
                console.log(coffeeList);
              },
              500,
              '카페라떼'
            );
          },
          500,
          '카페모카'
        );
      },
      500,
      '아메리카노'
    );
  },
  500,
  '에스프레소'
);
```

#### 참고 ) 동기적 코드 vs 비동기적 코드

동기적인 코드는 현재 실행 중인 코드가 완료된 후에야 다음 코드를 실행하는 방식이며, 비동기적인 코드는 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어가는 것입니다.

사용자의 요청에 의해 특정 시간이 경과되기 전까지 어떤 함수의 실행을 보류 한다거나, 사용자의 개입이 있을 때 함수를 실행하도록 대기하거나, 웹브라우저 자체가 아닌 별도의 대상에 무언가를 요청하고 그 응답이 왔을 때 실행하도록 하는 등, 별도의 요청, 실행 대기, 보류 등과 관련된 코드는 비동기적인 코드 입니다.



#### 콜백 지옥 해결

1. 익명함수를 기명함수로 변경

```js
var coffeeList = '';

var addEspresso = function(name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addAmericano, 500, '아메리카노');
};
var addAmericano = function(name) {
  coffeeList += ', ' + name;
  console.log(coffeeList);
  setTimeout(addMocha, 500, '카페모카');
};
var addMocha = function(name) {
  coffeeList += ', ' + name;
  console.log(coffeeList);
  setTimeout(addLatte, 500, '카페라떼');
};
var addLatte = function(name) {
  coffeeList += ', ' + name;
  console.log(coffeeList);
};

setTimeout(addEspresso, 500, '에스프레소');
```

2. Promise 활용

```js
new Promise(function(resolve) {
  setTimeout(function() {
    var name = '에스프레소';
    console.log(name);
    resolve(name);
  }, 500);
})
  .then(function(prevName) {
    return new Promise(function(resolve) {
      setTimeout(function() {
        var name = prevName + ', 아메리카노';
        console.log(name);
        resolve(name);
      }, 500);
    });
  })
  .then(function(prevName) {
    return new Promise(function(resolve) {
      setTimeout(function() {
        var name = prevName + ', 카페모카';
        console.log(name);
        resolve(name);
      }, 500);
    });
  })
  .then(function(prevName) {
    return new Promise(function(resolve) {
      setTimeout(function() {
        var name = prevName + ', 카페라떼';
        console.log(name);
        resolve(name);
      }, 500);
    });
  });




// 클로저를 활용한 방법
var addCoffee = function(name) {
  return function(prevName) {
    return new Promise(function(resolve) {
      setTimeout(function() {
        var newName = prevName ? prevName + ', ' + name : name;
        console.log(newName);
        resolve(newName);
      }, 500);
    });
  };
};
addCoffee('에스프레소')()
  .then(addCoffee('아메리카노'))
  .then(addCoffee('카페모카'))
  .then(addCoffee('카페라떼'));
```

3. Generator 활용

```js
var addCoffee = function(prevName, name) {
  setTimeout(function() {
    coffeeMaker.next(prevName ? prevName + ', ' + name : name);
  }, 500);
};
var coffeeGenerator = function*() {
  var espresso = yield addCoffee('', '에스프레소');
  console.log(espresso);
  var americano = yield addCoffee(espresso, '아메리카노');
  console.log(americano);
  var mocha = yield addCoffee(americano, '카페모카');
  console.log(mocha);
  var latte = yield addCoffee(mocha, '카페라떼');
  console.log(latte);
};
var coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```

4. async await

```js
var addCoffee = function(name) {
  return new Promise(function(resolve) {
    setTimeout(function() {
      resolve(name);
    }, 500);
  });
};
var coffeeMaker = async function() {
  var coffeeList = '';
  var _addCoffee = async function(name) {
    coffeeList += (coffeeList ? ',' : '') + (await addCoffee(name));
  };
  await _addCoffee('에스프레소');
  console.log(coffeeList);
  await _addCoffee('아메리카노');
  console.log(coffeeList);
  await _addCoffee('카페모카');
  console.log(coffeeList);
  await _addCoffee('카페라떼');
  console.log(coffeeList);
};
coffeeMaker();
```



### 정리

- 콜백함수는 다른 코드에 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수
- 제어권을 넘겨받은 코드는 다음과 같은 제어권을 가짐
  - 콜백 함수를 스스로 판단해서 실행
  - 콜백함수가 this를 무엇을 바라보도록 할지 결정 해줌 (기본은 window) -> 함수로써 실행되기 때문

