```js
function myFunc(theObject) {
  theObject.make = "Toyota";
}

var mycar = {make: "Honda", model: "Accord", year: 1998};
var x, y;

x = mycar.make; // x 의 값은 "Honda" 입니다.

myFunc(mycar);
y = mycar.make; // y 의 값은 "Toyota" 입니다.
                // (make 속성은 myFunc에서 변경되었습니다.)




function myFunc(theObject) {
  // 새로운 주소값으로 저장
  theObject = {make: "Ford", model: "Focus", year: 2006};
}

var mycar = {make: "Honda", model: "Accord", year: 1998};
var x, y;

x = mycar.make; // x 의 값은 "Honda" 입니다.

myFunc(mycar);
y = mycar.make; // y 의 값은 여전히 "Honda" 입니다.

```
