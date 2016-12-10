
# 루프와 반복


## 1. for문

```js
for (var i = 0; i < 5 ; i++) {
  console.log(i);
}
```
## 2. do while 문
do 블럭안의 문장들이 실행되고 while문에서 조건 체크함
```js

var i=0;
do {
  ++i;
  console.log(i);
} while ( i< 5);

```
## 3. while 문
```js

// 무한 Loop
while (true) {
  console.log('TEST');
}

var i = 0;
while (true) {
  ++i;
  console.log('TEST'+i);
  if (i = 5) {
    break; // Loop 탈출
  }
}
```
## 4. Break And Continue
```js
var i = 0;
markLabel:
while (true) {
 ++i;
 if(i == 5) {
   break markLabel;
 }

}
console.log("Loop Escape!");
```
```js
var i = 0;
markLabel :
while (true) {
  ++i;
  if(i == 5) {
    continue markLabel;
  } else if( i == 7){
    break markLabel;
  }
  console.log("Continue Check"+i);

}
```

## 5. for ... in 문
객체의 열거속성을 이용하여 지정된 객체명을 가져올때 사용한다.

```js

var obj = {a:1, b:2, c:3};

for (var prop in obj) {
  console.log(prop);
}
```

## 6. for ... of 문

Array, Map ,Set 에서 값을 순회할때 사용한다.

```js
var arr = [1, 2, 3];

for (var val of arr) {
  console.log(val);
}
```
