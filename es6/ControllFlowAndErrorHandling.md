# 제어의 흐름과 에러 처리


## 1. Block 문

```js
{
  // Steatement1
  // Steatement2  
}
```
ES6 이전에는 독립적인 블록범위를 가지고 있지않다.

```js
{
  let x = 1;

}
console.log(x); // result : undefiend
```


## 2. if else 문

틀리기 쉬운 논리 연산자 부분
```js
var a1 =  true && true;     // t && t returns true
var a2 =  true && false;    // t && f returns false
var a3 = false && true;     // f && t returns false
var a4 = false && (3 == 4); // f && f returns false
var a5 = "Cat" && "Dog";    // t && t returns Dog
var a6 = false && "Cat";    // f && t returns false
var a7 = "Cat" && false;    // t && f returns false
다음의 코드는 || (논리 합) 연산자의 예제를 보여주고 있습니다.

var o1 =  true || true;     // t || t returns true
var o2 = false || true;     // f || t returns true
var o3 =  true || false;    // t || f returns true
var o4 = false || (3 == 4); // f || f returns false
var o5 = "Cat" || "Dog";    // t || t returns Cat
var o6 = false || "Cat";    // f || t returns Cat
var o7 = "Cat" || false;    // t || f returns Cat
다음의 코드는 ! (논리 부정) 연산자의 예제를 보여주고 있습니다.

var n1 = !true;  // !t returns false
var n2 = !false; // !f returns true
var n3 = !"Cat"; // !t returns false
```
틀리기 쉬운 단축 연산

논리 연산자가 왼쪽에서 오른쪽으로 연산될때, 논리연산자는 다음의 규칙을 따라서 "단축 연산"으로 검사됩니다:

false && anything 는  false로 단축 계산됩니다.
true || anything 는 true로 단축 계산됩니다.
이 논리 규칙들은 이러한 평가가 언제나 정확하다고 보증합니다.
위에서 anything 부분은 연산되지 않았고,
어떤 부작용도 아무런 효과를 미치지 못한다는 것에 주목하세요.
```js

if(true && true) {
   console.log("TEST");
}

if(false && false) {
   console.log("TEST");
}

if(true || false) {
 console.log("TEST");
}

if(false || false) {
   console.log("TEST");
}

```

거짓으로 취급하는 값

다음과 같은 값은 거짓으로 평가됩니다:

* false
* undefined
* null
* 0
* NaN
* the empty string ("")

```js

실전에서 자주 사용되는 기법
function shortCircuitConfig(option) {
    let defaultConfig = {option001 : 'option001'}
    let config = option || defaultConfig; //
    console.log(config);
}



```


## 3. switch 문

```js
switch (expression) {
  case label_1:
    statements_1
    [break;]
  case label_2:
    statements_2
    [break;]
    ...
  default:
    statements_def
    [break;]
}
```

```js

const BROWSER = {
  Chrome : Symbol('Chrome'),
  Edge : Symbol('Edge'),
  Fiefox :Symbol('Firefox')
};

var browserName = BROWSER.Firefox;


switch (browserName) {
  case BROWSER.Chrome:
    console.log("Chrome");
    break;
  case BROWSER.Edge:
    console.log("Edge");
    break;
  case BROWSER.Firefox:
    console.log("Firefox");
    break;  
  default: throw Error("");
    break

}
```

## 4. throw 문
```js
try{
  // 다음과 같이 에러를 던지면 Catch 블록에서 처리한다.
  throw new Error('test');
} catch(e){
  console.log(e.message);
}


```

## 5. try ... catch ...finally 문

```js
try{
  //
  throw new Error('test');
} catch(e){
  console.log(e.message);
} finally{
  // 에러 여부와 상관없이 실행되는 finally 블록
  console.log("finally!");
}
```
