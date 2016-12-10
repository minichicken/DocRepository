
UPDATE     : 2016.12.05
Translator : ByoengGiKim


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


### 1-1. Chaining `then()` calls( `then()`을 가지고 체이닝 호출하기 )

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





### 1-2. Executing asynchronous functions in parallel(병렬로 비동기 함수 실행하기)


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

### 1-3. Glossary: Promises (용어: 프로미스)

The Promise API is about delivering results asynchronously. A Promise object (short: Promise) is a stand-in for the result, which is delivered via that object.
Proamise API는 비동기적으로 결과를 보내는 것이다. Promise object(일명: promise)는 Promise 통해서 전달되는 결과의 대역이다.
States:

* A Promise is always in one of three mutually exclusive states: Promise는 항상 3가지 서로 배타적인 상태중에 있다.
  * Before the result is ready, the Promise is pending. 결과가 완료되기전에 , promise는 대기하고 있다.
  * If a result is available, the Promise is fulfilled. 만약 결과가 접근 가능하다면, promise는 실행되었다.
  * If an error happened, the Promise is rejected. 만약 에러가 발생한다면, promise는 거부된다.
* A Promise is settled if “things are done” (if it is either fulfilled or rejected). 만약에 완료된것이라면, promise는 변화가 없이 유지된다.
* A Promise is settled exactly once and then remains unchanged. promise 가 정확하게 변화가 없이 유지되고 나면 나머지 변화하지 않는다.

Reacting to state changes(상태 변화에 반응하는 것):

* Promise reactions are callbacks that you register with the Promise method `then()`, to be notified of a fulfillment or a rejection.
  Promise 반응들은 Promise `then()` 메서드를 가지고 등록하여 호출된다, 처리나 거부를 알리기 위해서.
* A thenable is an object that has a Promise-style `then()` method. Whenever the API is only interested in being notified of settlements, it only demands thenables (e.g. the values returned from `then()` and `catch()`; or the values handed to `Promise.all()` and `Promise.race()`).
  thenable은 promise 스타링의  `then()` 메서드를 가진 object이다. 매번 API 가 안정에 대한 알림에 대한 통지에 되한것만 관심을 둘때마다, 이것은 오로지 thenable만 요구한다.(예를들면,`then()` 그리고 `catch()`을 가지고 리턴되는 값 또는  `Promise.all()` 그리고 `Promise.race()` 를 가지고 핸들링한 값들)

Changing states: There are two operations for changing the state of a Promise. After you have invoked either one of them once, further invocations have no effect.
상태를 변화하는것: promise 의 상태에 대한 2가지 규칙을 가진다. promise들중에 하나가 invoked 된후에, 더 많은 실행은 효과가 없다.

* Rejecting a Promise means that the Promise becomes rejected. 프로미스가 거부되는것은 promise 거부가 됨을 의미한다.
* Resolving a Promise has different effects, depending on what value you are resolving with: Promise 가 처리하는것은  어떤 처리하고 있는 값에 따라서 다른 효과를 가진다.
  * Resolving with a normal (non-thenable) value fulfills the Promise.보통 값을 가지고 처리하는 것은 Promise 를 완료시킨다.
  * Resolving a Promise P with a thenable T means that P can’t be resolved anymore and will now follow T’s state, including its fulfillment or rejection value. The appropriate P reactions will get called once T settles (or are called immediately if T is already settled).
    thenable T 가지고 Promise P를 처리하는것은 P는 더이상 처리할수 없고 지금 T의 완료 또는 거부 값을 포함한 상태를 따라갈것을 의미한다.

## 2. Introduction: Promises (소개 :Promises)

Promises are a pattern that helps with one particular kind of asynchronous programming: a function (or method) that returns a single result asynchronously. One popular way of receiving such a result is via a callback (“callbacks as continuations”):

Promises는 특정 종류의 비동기 프로그래밍에 도움을 주는 패턴이다. 비동기적으로 하나의 결과를 리턴하는 함수( 또는 메서드). 결과 같은 것을 받는 일반적인 방법은 호출을 통한 것이다.(지속적인 콜백)

```js
asyncFunction(arg1, arg2,
    result => {
        console.log(result);
    });
```

Promises provide a better way of working with callbacks: Now an asynchronous function returns a Promise, an object that serves as a placeholder and container for the final result. Callbacks registered via the Promise method then() are notified of the result:
Promise는 호출을 가지고 동적하는 가장 좋은 방법을 제공한다. 지금 비동기 함수는 마지막 결과에 대한 컨테이너와 위치에 대한 것을 것을 제공하는 Promise를 리턴한다.

```js
asyncFunction(arg1, arg2)
.then(result => {
    console.log(result);
});
```
Compared to callbacks as continuations, Promises have the following advantages:
지속적인 콜백과 비교괴는, Promises는 다음 이점을 가진다.

* No inversion of control: similarly to synchronous code, Promise-based functions return results, they don’t (directly) continue – and control – execution via callbacks. That is, the caller stays in control.

비제어의 역전 : 동기적인 코드들과 유사하게고, Promise를 기본으로 하는 함수들은 값을 리턴한다, 그것들은 직접적으로 지속 - 제어 - 실행을 콜백을 가지고 하지않는다.

* Chaining is simpler: If the callback of `then()` returns a Promise (e.g. the result of calling another Promise-based function) then `then()` returns that Promise (how this really works is more complicated and explained later). As a consequence, you can chain then() method calls:

체이닝의 단순함 : 만약 `then()`의 콜백이 프로미스(예를들면, 다른 프로미스 베이스 함수들 호출한 결과)를 리턴한다면 , `then()`은 그 프로미스를 리턴한다.(어떻게 이것이 사실상 더 복잡하게 동작하는지는 후에 설명한다.) . 결과적으로 당신은 제이닝 `then()` 메서드를 호출할수 있다.

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
  비동기적인 호출들을 조합하는것(루프,매핑 등.): 이것은 아주 쉽다, 왜냐하면 동작 가능한 프로미스 오브젝트를 데이터로 가지기 때문이다.

* Error handling: As we shall see later, error handling is simpler with Promises, because, once again, there isn’t an inversion of control. Furthermore, both exceptions and asynchronous errors are managed the same way.
  에러 핸들링 : 만약 당신이 후에 보게 되다면, 에러 핸들링은 프로미스를 가지고 쉽다, 왜냐하며냐 , 한번 다시 , 제어에 역전이 없기 때문이다. 더군다나 , 각각 예외들과 비동기적인 에러들은 같은 방법으로 관리할수 있다.
* Cleaner signatures: With callbacks, the parameters of a function are mixed; some are input for the function, others are responsible for delivering its output. With Promises, function signatures become cleaner; all parameters are input.
 깔끔한 시그니처 : 콜백된 함수의 파라미터들은 섞인다; 어떤 것들은 함수에 입력되고 , 다른 것들은 이것의 출력 전달을 위한 역할을 한다. 프로미스를 가진 함수의 시그니처들은 매우 클린하다.

* Standardized: Prior to Promises, there were several incompatible ways of handling asynchronous results (Node.js callbacks, XMLHttpRequest, IndexedDB, etc.). With Promises, there is a clearly defined standard: ECMAScript 6. ES6 follows the standard Promises/A+ [1]. Since ES6, an increasing number of APIs is based on Promises.
 표준화 : 프로미스 이전에는, 비동기적인 결과들을 다루는 양립할수 없는 것들이 있었다.((Node.js callbacks, XMLHttpRequest, IndexedDB, etc.). 프로미스를 가진다면, 아주 깔끔하고 정돈된 표준화가 있다: EMCA 6. ES6는 Promises/A+ 를 따른다.  ES6 이후로는 ,  증가하는 API 숫자는 프로미스를 기본으로 한다.



## 3. A first example

Let’s look at a first example, to give you a taste of what working with Promises is like.

With Node.js-style callbacks, reading a file asynchronously looks like this:

```js
fs.readFile('config.json',
    function (error, text) {
        if (error) {
            console.error('Error while reading config file');
        } else {
            try {
                const obj = JSON.parse(text);
                console.log(JSON.stringify(obj, null, 4));
            } catch (e) {
                console.error('Invalid JSON in file');
            }
        }
    });
```

With Promises, the same functionality is used like this:

```js
readFilePromisified('config.json')
.then(function (text) { // (A)
    const obj = JSON.parse(text);
    console.log(JSON.stringify(obj, null, 4));
})
.catch(function (error) { // (B)
    // File read error or JSON SyntaxError
    console.error('An error occurred', error);
});
```

There are still callbacks, but they are provided via methods that are invoked on the result (then() and catch()). The error callback in line B is convenient in two ways: First, it’s a single style of handling errors (versus if (error) and try-catch in the previous example). Second, you can handle the errors of both readFilePromisified() and the callback in line A from a single location.

The code of readFilePromisified() is shown later.




## 4. Three ways of understanding Promises

Let’s look at three ways of understanding Promises.

The following code contains a Promise-based function asyncFunc() and its invocation.

```js
function asyncFunc() {
    return new Promise((resolve, reject) => { // (A)
        setTimeout(() => resolve('DONE'), 100); // (B)
    });
}
asyncFunc()
.then(x => console.log('Result: '+x));

// Output:
// Result: DONE
```
asyncFunc() returns a Promise. Once the actual result 'DONE' of the asynchronous computation is ready, it is delivered via resolve() (line B), which is a parameter of the callback that starts in line A.

So what is a Promise?

  * Conceptually, invoking asyncFunc() is a blocking function call.
  * A Promise is both a container for a value and an event emitter.



### 4-1.Conceptually: calling a Promise-based function is blocking
The following code invokes asyncFunc() from the async function main(). Async functions are a feature of ECMAScript 2017.

```js
async function main() {
    const x = await asyncFunc(); // (A)
    console.log('Result: '+x); // (B)

    // Same as:
    // asyncFunc()
    // .then(x => console.log('Result: '+x));
}
main();
```

The body of main() expresses well what’s going on conceptually, how we usually think about asynchronous computations. Namely, asyncFunc() is a blocking function call:

  * Line A: Wait until asyncFunc() is finished.
  * Line B: Then log its result x.
Prior to ECMAScript 6 and generators, you couldn’t suspend and resume code. That’s why, for Promises, you put everything that happens after the code is resumed into a callback. Invoking that callback is the same as resuming the code.



### 4-2.A Promise is a container for an asynchronously delivered value

If a function returns a Promise then that Promise is like a blank into which the function will (usually) fill in its result, once it has computed it. You can simulate a simple version of this process via an Array:

```js
function asyncFunc() {
    const blank = [];
    setTimeout(() => blank.push('DONE'), 100);
    return blank;
}
const blank = asyncFunc();
// Wait until the value has been filled in
setTimeout(() => {
    const x = blank[0]; // (A)
    console.log('Result: '+x);
}, 200);
```

With Promises, you don’t access the eventual value via [0] (as in line A), you use method then() and a callback.


### 4-3.A Promise is an event emitter
Another way to view a Promise is as an object that emits events.

```js
function asyncFunc() {
    const eventEmitter = { success: [] };
    setTimeout(() => { // (A)
        for (const handler of eventEmitter.success) {
            handler('DONE');
        }
    }, 100);
    return eventEmitter;
}
asyncFunc()
.success.push(x => console.log('Result: '+x)); // (B)
```

Registering the event listener (line B) can be done after calling asyncFunc(), because the callback handed to setTimeout() (line A) is executed asynchronously (after this piece of code is finished).

Normal event emitters specialize in delivering multiple events, starting as soon as you register.

In contrast, Promises specialize in delivering exactly one value and come with built-in protection against registering too late: the result of a Promise is cached and passed to event listeners that are registered after the Promise was settled.


## 5.Creating and using Promises
Let’s look at how Promises are operated from the producer and the consumer side.

### 5-1.Producing a Promise
As a producer, you create a Promise and send a result via it:

```js
const p = new Promise(
    function (resolve, reject) { // (A)
        ···
        if (···) {
            resolve(value); // success
        } else {
            reject(reason); // failure
        }
    });
```









### 5-2.The states of Promises

Once a result was delivered via a Promise, the Promise stays locked in to that result. That means each Promise is always in either one of three (mutually exclusive) states:

  * Pending: the result hasn’t been computed, yet (the initial state of each Promise)
  * Fulfilled: the result was computed successfully
  * Rejected: a failure occurred during computation

A Promise is settled (the computation it represents has finished) if it is either fulfilled or rejected. A Promise can only be settled once and then stays settled. Subsequent attempts to settle have no effect.


The parameter of new Promise() (starting in line A) is called an executor:

  * Resolving: If the computation went well, the executor sends the result via resolve(). That usually fulfills the Promise p. But it may not – resolving with a Promise q leads to p tracking q: If q is still pending then so is p. However q is settled, p will be settled the same way.
  * Rejecting: If an error happened, the executor notifies the Promise consumer via reject(). That always rejects the Promise.

If an exception is thrown inside the executor, p is rejected with that exception.








### 5-3.Consuming a Promise

As a consumer of promise, you are notified of a fulfillment or a rejection via reactions – callbacks that you register with the methods then() and catch():

```js
promise
.then(value => { /* fulfillment */ })
.catch(error => { /* rejection */ });
```
What makes Promises so useful for asynchronous functions (with one-off results) is that once a Promise is settled, it doesn’t change anymore. Furthermore, there are never any race conditions, because it doesn’t matter whether you invoke then() or catch() before or after a Promise is settled:

  * Reactions that are registered with a Promise before it is settled, are notified of the settlement once it happens.
  * Reactions that are registered with a Promise after it is settled, receive the cached settled value “immediately” (their invocations are queued as tasks).
Note that catch() is simply a more convenient (and recommended) alternative to calling then(). That is, the following two invocations are equivalent:
```js
promise.then(
    null,
    error => { /* rejection */ });

promise.catch(
    error => { /* rejection */ });
```

### 5-4 Promises are always asynchronous

A Promise library has complete control over whether results are delivered to Promise reactions synchronously (right away) or asynchronously (after the current continuation, the current piece of code, is finished). However, the Promises/A+ specification demands that the latter mode of execution be always used. It states so via the following requirement (2.2.4) for the then() method:

  onFulfilled or onRejected must not be called until the execution context stack contains only platform code.

That means that your code can rely on run-to-completion semantics (as explained in the previous chapter) and that chaining Promises won’t starve other tasks of processing time.

Additionally, this constraint prevents you from writing functions that sometimes return results immediately, sometimes asynchronously. This is an anti-pattern, because it makes code unpredictable. For more information, consult “Designing APIs for Asynchrony” by Isaac Z. Schlueter.
