# Promise (동기 프로그래밍을 위한 Promise)


## 1. Overview
Promises are an alternative to callbacks for delivering the results of an asynchronous computation. They require more effort from implementors of asynchronous functions, but provide several benefits for users of those functions.

  프로미스는 비동기 연산의 결과를 전달하기 위한 콜백함수를 대체하는 것이다.
  비동기 함수의 구현체에 대한 더 많은 노력을 요구하지만 , 비동기 함수 사용자에게 이익을 준다.


The following function returns a result asynchronously, via a Promise:
다음은 Promise를 통해서 비동기적인 결과를 리턴하는 함수이다.
```js

function asyncFunc() {
    return new Promise(
        function (resolve, reject) {
            ···
            resolve(result);
            ···
            reject(error);
        });
}

```


You call `asyncFunc()` as follows:
우리는 다음과 같이 `asyncFunc()`를 호출할수있다.

```js
asyncFunc()
.then(result => { ··· })
.catch(error => { ··· });
```


## 2. Chaining `then()` calls( `then()`을 가지고 체이닝 호출하기 )

`then()` always returns a Promise, which enables you to chain method calls:
  `then()`은 항상 메서드 체이닝 메서드 호출이 가능한, Promise를 리턴한다.

```js
asyncFunc1()
.then(result1 => {
    // Use result1
    return asyncFunction2(); // (A)
})
.then(result2 => { // (B)
    // Use result2
})
.catch(error => {
    // Handle errors of asyncFunc1() and asyncFunc2()
});
```

How the Promise P returned by then() is settled depends on what its callback does: 어떻게 `then()`로 프로미스 P 리턴되는지는 무엇이 이걸 콜백하는지에 의존하는 것에 결정된다.

* If it returns a Promise (as in line A), the settlement of that Promise is forwarded to P.That’s why the callback from line B can pick up the settlement of asyncFunction2’s Promise.
 A 라인에서 프로미스가  리턴된다면, 이 프로미스의 settlement 는 P 로 전달한다.
 이런 이유로 라인 B에서의 콜백은 asyncFunction2’s Promise의 settlement로 픽업할수있다.

* If it returns a different value, that value is used to settle P.
 만약 다른 값으로 리턴된다면, 이 값은 P를 결정하는데 사용될수 있다.

* If throws an exception then P is rejected with that exception. 만약 에러를 던지다면, then P 는 에러를 가지고 reject된다.

Furthermore, note how catch() handles the errors of two asynchronous function calls (asyncFunction1() and asyncFunction2()). That is, uncaught errors are passed on until there is an error handler.
더 말하자면, 두 개의 비동기함수를 호출에 대한 에러를 `catch()`에서 핸들링 할지를 주목해라.
즉, 잡히지 않는 에러는 에러 핸들러가 에러 핸들러에 있어도 지나친다.





## 3. Executing asynchronous functions in parallel(병렬로 비동기 함수 실행하기)


If you chain asynchronous function calls via then(), they are executed sequentially, one at a time:
만약 비동기 함수들을`then()`을 가지고 체이닝 한다면, 함수들은 순서적으로 한번에 실행된다.
```js
asyncFunc1()
.then(() => asyncFunc2());
```


If you don’t do that and call all of them immediately, they are basically executed in parallel (a fork in Unix process terminology):
만약 그렇게 하고 싶지 않거나 즉시 모두 실행하고 싶다면, 기본적으로 병렬적으로 실행된다.(유닉스 프로세스 용어에서는 fork 처럼)

```js
asyncFunc1();
asyncFunc2();
```
`Promise.all()` enables you to be notified once all results are in (a join in Unix process terminology). Its input is an Array of Promises, its output a single Promise that is fulfilled with an Array of the results.

`Promise.all()`은 한번에 결과를 통지할수있다. 이것의 입력은 프로미스들의 배열이고, 이것의 출력은 결과의 배열을 가진 완료상태인 하나의 프로미스이다.

```js
Promise.all([
    asyncFunc1(), // Promise1
    asyncFunc2(), // Promise2
])
.then(([result1, result2]) => {
    ···
})
.catch(err => {
    // Receives first rejection among the Promises
    ···
});
```

## 4. Glossary: Promises (용어: 프로미스)

The Promise API is about delivering results asynchronously. A Promise object (short: Promise) is a stand-in for the result, which is delivered via that object.

States:

* A Promise is always in one of three mutually exclusive states:
  * Before the result is ready, the Promise is pending.
  * If a result is available, the Promise is fulfilled.
  * If an error happened, the Promise is rejected.
* A Promise is settled if “things are done” (if it is either fulfilled or rejected).
* A Promise is settled exactly once and then remains unchanged.

Reacting to state changes:

* Promise reactions are callbacks that you register with the Promise method then(), to be notified of a fulfillment or a rejection.
* A thenable is an object that has a Promise-style then() method. Whenever the API is only interested in being notified of settlements, it only demands thenables (e.g. the values returned from then() and catch(); or the values handed to Promise.all() and Promise.race()).

Changing states: There are two operations for changing the state of a Promise. After you have invoked either one of them once, further invocations have no effect.

* Rejecting a Promise means that the Promise becomes rejected.
* Resolving a Promise has different effects, depending on what value you are resolving with:
  * Resolving with a normal (non-thenable) value fulfills the Promise.
  * Resolving a Promise P with a thenable T means that P can’t be resolved anymore and will now follow T’s state, including its fulfillment or rejection value. The appropriate P reactions will get called once T settles (or are called immediately if T is already settled).

## 5. Introduction: Promises

Promises are a pattern that helps with one particular kind of asynchronous programming: a function (or method) that returns a single result asynchronously. One popular way of receiving such a result is via a callback (“callbacks as continuations”):


```js
asyncFunction(arg1, arg2,
    result => {
        console.log(result);
    });
```

Promises provide a better way of working with callbacks: Now an asynchronous function returns a Promise, an object that serves as a placeholder and container for the final result. Callbacks registered via the Promise method then() are notified of the result:

```js
asyncFunction(arg1, arg2)
.then(result => {
    console.log(result);
});
```
Compared to callbacks as continuations, Promises have the following advantages:

* No inversion of control: similarly to synchronous code, Promise-based functions return results, they don’t (directly) continue – and control – execution via callbacks. That is, the caller stays in control.
* Chaining is simpler: If the callback of then() returns a Promise (e.g. the result of calling another Promise-based function) then then() returns that Promise (how this really works is more complicated and explained later). As a consequence, you can chain then() method calls:

```js
  asyncFunction1(a, b)
  .then(result1 => {
      console.log(result1);
      return asyncFunction2(x, y);
  })
  .then(result2 => {
      console.log(result2);
  });
```

* Composing asynchronous calls (loops, mapping, etc.): is a little easier, because you have data (Promise objects) you can work with.
* Error handling: As we shall see later, error handling is simpler with Promises, because, once again, there isn’t an inversion of control. Furthermore, both exceptions and asynchronous errors are managed the same way.
* Cleaner signatures: With callbacks, the parameters of a function are mixed; some are input for the function, others are responsible for delivering its output. With Promises, function signatures become cleaner; all parameters are input.
* Standardized: Prior to Promises, there were several incompatible ways of handling asynchronous results (Node.js callbacks, XMLHttpRequest, IndexedDB, etc.). With Promises, there is a clearly defined standard: ECMAScript 6. ES6 follows the standard Promises/A+ [1]. Since ES6, an increasing number of APIs is based on Promises.
