#1. Promise 란


프로미스는 비동기 연산의 결과를 전달하기 위한 콜백함수를 대체하는 것이다.

## 1-1 기본적인사용법


```js

function asyncFunc() {
    return new Promise(
      function (resolve, reject) {
        let req = new XMLHttpRequest();
        req.open('GET',URL, true);

        // 성공적으로 실행되고 난후 리턴 값은 resolve를 호출
        if (req.status === 200) {
            resolve(JSON.parse(req.responseText));

        // 에러로 실패한 경우는 reject   
        } else {
          reject(new Error(req.statusText));
        }

        req.onerror = function(){
            reject(new Error(req.statusText));
        }      

        req.send();
      }
  );
}

```


```js
asyncFunc()
.then((value) => {
  console.log("Value" + value);
}).catch((err) => {
  console.log(err.message);
});
```
