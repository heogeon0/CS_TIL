코어 자바스크립트를 읽으면서 공부한 내용을 저의 입맛에 맞춰 정리하였습니다.
혹시 이글을 읽게 되시면 잘못된 내용이 있을 수 있으니 비판적인 시각으로 읽으시는걸 추천드립니다.

![img](https://blog.kakaocdn.net/dn/cmElE7/btrTth1WTEp/sc1nc3MC5LobUX4oTOWV2k/img.jpg)



## undefined와 null

자바스크립트에는 '없음'을 나타내는 값이 두가지 있습니다. 바로 undefined와 null입니다. 두 값의 의미는 같은 것 같지만 미세하게 다르고, 사용하는 목적 또한 다릅니다.



### undefined

undefined는 사용자가 명시적으로 지정할 수도 있지만 값이 존재하지 않을 때 자바스크립트 엔진이 자동으로 부여하는 경우도 있습니다.

자바스크립트 엔진은 사용자가 값을 지정할 것이라고 예상되는 상황임에도 실제로 그렇게 하지 않을 때 undefined를 반환합니다. 다음 세가지가 이에 해당합니다.

1. 값을 대입하지 않은 변수. 즉 데이터 영역의 메모리 주소를 지정하지 않은 식별자에 접근할 때
2. 객체 내부의 존재하지 않는 프로퍼티에 접근하려고 할 때
3. return문이 없거나 호출되지 않는 함수의 실행 결과

``` js
var a;
console.log(a) // undefined

var obj = { a : 1}
console.log(obj.b) // ReferenceError : b is not defined

var func = function() {}
var c = func()
console.log(c) // undefined
```

- 위의 1번 상황 ( 값을 대입하지 않은 경우)를 배열에 대입하면 조금 특이한 동작을 확인할 수 있습니다.

```js
var arr1 = []
arr1.length = 3
console.log(arr1) // [empty x 3]

var arr2 = new Array(3)
console.log(arr2) // [empty x 3]

var arr3 = [undefined, undefined, undefined]
console.log(arr3) // [undefined, undefined, undefined]

```

 arr1과 arr2 그리고 arr3의 결과가 다른 것을 확인할 수 있습니다.

'비어있는 요소'와 'undefined를 할당한 요소'는 출력 결과부터 다릅니다. 이는 순회와 관련된 배열 메서드들의  작동에서도 차이가 발생하는데 비어있는 요소는 순회와 관련된 많은 배열 메서드들의 순회 대상에서 제외됩니다.



```js
var arr1 = [undefined, 1];
var arr2 = [];
arr2[1] = 1;

arr1.forEach(function(v, i) {
  console.log(v, i);
}); // undefined 0 / 1 1
arr2.forEach(function(v, i) {
  console.log(v, i);
}); // 1 1

arr1.map(function(v, i) {
  return v + i;
}); // [NaN, 2]
arr2.map(function(v, i) {
  return v + i;
}); // [empty, 2]

arr1.filter(function(v) {
  return !v;
}); // [undefined]
arr2.filter(function(v) {
  return !v;
}); // []

arr1.reduce(function(p, c, i) {
  return p + c + i;
}, ''); // undefined011
arr2.reduce(function(p, c, i) {
  return p + c + i;
}, ''); // 11
```

 사용자가 undefined를 할당한 arr1에 대해서는 일반적으로 알고 있는 대로 배열의 모든 요소를 순회해서 결과를 출력합니다. 그러나, 각 메서드들이 비어 있는 요소에 대해서는 어떠한 처리도 하지 않고 건너뛰었음을 알 수 있습니다.

사실 이는 자연스러운 현상인데, 객체와 마찬가지로 특정 인덱스에 값을 지정할 때 비로소 빈 공간을 확보하고 인덱스를 이름으로 지정하고 데이터의 주솟값을 저장하는 등의 동작을 합니다. 
즉, 값이 지정되지 않은 인덱스는 '아직은 존재하지 않는 프로퍼티'인 것입니다.



위에서 본 것처럼, 사용자가 지정한 undefined와 지정하지 않은 undefined가 차이가 발생하는 것을 알 수 있늡니다. 이렇게 되면 undefined에 대해 혼란이 생기는데, 이런 혼란을 막기 위해 undefined를 직접 할당하는 것을 지양하고 있습니다.

그리고 우리에게는 **null**이 있습니다. 



### null

null은 사용자가 '비어있음'을 명시적으로 나타내고 싶을 때 사용 할 수 있습니다. 

위의 내용을 보았듯이 undefined를 사용자가 직접 명시 할 경우 기존의 undefined와 다른 작동을 하며 혼돈의 여지가 있습니다. 이것이 null을 사용하는 이유입니다.



**null 사용시 주의할점**

+ type of null 의 결과가 object입니다. 이는 자바스크립트의 버그입니다.
+ 따라서 어떤 변수값이 null인지 여부를 판별하기 위해서는 '===' 연산자를 활용해야 합니다

```js
var n = null;
console.log(typeof n); // object

console.log(n == undefined); // true
console.log(n == null); // true

console.log(n === undefined); // false
console.log(n === null); // true
```





### 정리

'없음'을 나타내는 값은 두 가지가 있는데, undefined는 어떤 변수에 값이 존재하지 않을 경우를 의미하고 null은 사용자가 명시적으로 '없음'을 표현하기 위한 대입값입니다. 

undefined 역시 명시적으로 대입할 수 있지만, 이렇게 되면 기존의 undefined와 기능적으로 달라지게 되고 혼돈을 줄 수 있습니다. 그렇기에 사용자가 '없음'을 표현할 때는 null을, undefined는 자바스크립트 엔진이 자동으로 할당 될 때 사용합니다.