코어 자바스크립트를 읽으면서 공부한 내용을 저의 입맛에 맞춰 정리하였습니다.
혹시 이글을 읽게 되시면 잘못된 내용이 있을 수 있으니 비판적인 시각으로 읽으시는걸 추천드립니다.

![img](https://blog.kakaocdn.net/dn/cmElE7/btrTth1WTEp/sc1nc3MC5LobUX4oTOWV2k/img.jpg)



---

## 프로토타입

> 자바스크립트는 프로토타입 기반 언어입니다. 클래스 기반 언어에서는 '상속'을 사용하지만 프로토타입 기반 언어에서는 어떤 객체를 원형으로 삼고 이를 복제(참조)함으로써 상속과 비슷한 효과를 얻습니다.



### 1.프로토타입 개념 이해

![TIL 98 | 코어자바스크립트(6) 프로토타입](data:image/jpeg;base64,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)

- 어떤 생성자 함수를 new 연산자와 함께 호출하면
- Constructor에서 정의된 내용을 바탕으로 새로운 인스턴스가 생성됩니다
- 이때 instance에는 __proto__라는 프로퍼티가 자동으로 부여되는데
- 이 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조합니다.

prototype이라는 프로퍼티와 __proto__의 관계가 프로토타입 개념의 핵심입니다. 이 둘 모두 객체인데 prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장합니다. 그러면 인스턴스에서도 숨겨진 프로퍼티인 __proto__ 를 통해 이 메서드들에 접근할 수 있게 됩니다.



__proto__는 생략가능한 프로퍼티입니다. 그렇기 떄문에 생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있게 됩니다.

```js
suzi.__proto__.getName
-> suzi(.__proto__).getName
-> suzi.getName
```



```js
var arr = [1, 2]
console.dir(arr)
console.dir(Array)
```

<img src="C:\Users\heo37\AppData\Roaming\Typora\typora-user-images\image-20221222104315332.png" alt="image-20221222104315332" style="zoom: 25%;" align="left"/><img src="C:\Users\heo37\AppData\Roaming\Typora\typora-user-images\image-20221222104411984.png" alt="image-20221222104411984" style="zoom: 33%;" />

위의 코드를 실행했을 때, arr은 __proto__를 통해 push, pop과 같은 배열 메서드를 사용할 수 있습니다. array를 확인해 보면 함수의 정적 메서드인 from, isArray 등이 보이고 prototype에는 __proto__와 동일한 메서드 들이 들어있습니다.





### 2. constructor 프로퍼티

생성자 함수의 프로퍼팅인 prototype객체 내부에는 constructor객체도 들어있습니다. 이 프로퍼티는 원래의 생성자 함수를 참조합니다. 이는 인스턴스로부터 그 원형이 무엇인지 알 수 있는 수단이 됩니다.

```js
var arr = [1, 2];
Array.prototype.constructor === Array; // true
arr.__proto__.constructor === Array; // true
arr.constructor === Array; // true

var arr2 = new arr.constructor(3, 4);
console.log(arr2); // [3, 4]
```

한편 읽기 전용 속성이 부여된 예외적인 경우를 제외하고는 값을 바꿀 수 있습니다. 하지만 constructor를 변경하더라도 참조하는 대상이 변경될 뿐 이미 만들어진 인스턴스의 원형이 바뀐다거나 데이터 타입이 변하는 것은 아님을 알 수 있습니다.



### 3. 프로토타입 체인

#### 1. 메서드 오버라이드

메서드 오버라이드는 메서드 위에 메서드를 덮어 씌웠다는 표현입니다.

자바스크립트 엔진이 메서드를 찾는 방식은 가장 가까운 대상인 자신의 프로퍼티를 검색하고, 없으면 그 다음으로 가까운 대상인 __ㅔ개새__를 검색하는 순서로 진행 됩니다.



#### 2. 프로토타입 체인

![객체의 최상위 객체 Object](https://hdw0903.github.io/images/%EA%B0%9D%EC%B2%B4%EC%9D%98_%EC%B5%9C%EC%83%81%EC%9C%84_%EA%B0%9D%EC%B2%B4_Object.png)

__proto__ 프로퍼티 내부에 다시 __proto__프로퍼티가 연쇄적으로 이어진 것은 프로토타입 체인이라고 하고, 이 체인을 따라가며 검색하는 것을 프로토타입 체이닝이라고 합니다.

#### 3. 객체 전용 메서드의 예외사항

어떤 생성자 함수이든 prototype은 반드시 객체이기 때문에 Object.prototype이 언제나 프로토타입 체인의 최상단에 존재하게 됩니다. 따라서 객체에서만 사용할 메서드는 다른 여느 데이터 타입처럼 프로토타입 객체 안에 정의할 수 없습니다.

그렇기에 객체 전용 메서드들은 부득이 Object.portotype이 아닌 Object에 스태틱메서드로 부여할 수 밖에 없습니다.



### 4. 정리

어떤 생성자 함수를 new 연산자와 함께 호출하면 Consrtuctor에서 정의된 내용을 바탕으로 새로운 인스턴스가 생성되는데, 이 인스턴스에는 __proto__라는, Constructor의 prototype프로퍼티를 참조하는 프로퍼티가 자동으로 부여됩니다. __proto__는 생략 가능한 속성이라서 마치 자신의 메서드인 것처럼 호출 할 수 있습니다

