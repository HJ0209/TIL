200128

* 책추천

"인사이드 자바스크립트" 

"러닝자바스크립트"

"코어자바스크립트"



# 화면출력

> `display`는 어떻게 표시할지를 선택하고, `visibility`는 요소를 보일지 말지 결정
>
> display: 앞뒤로 줄바꿈이 되지 않습니다.

* `div` : division의 약자로 웹사이트의 레이아웃을 만들 때 주로 사용(스타일적용)

  `element` : 요소 태그

  `attribute` : 속성

```html
<html>
<head>


<script>

window.onload = function() {
                for (let dan = 2; dan <= 9; dan ++) {
                    for (let num = 1; num <= 9; num ++) {
                        let div = document.getElementById("display");
                        div.innerText = div.innerText + `${dan} * ${num} = ${dan*num}\n`;
                    }                
                }
            }



</script>
</head>
<body>
    <div id="display">

    </div>

</html>
    
# body에 display 넣기
# 엔터로 하기 위해 \n`이용하기
# div.innerText = div.innerText + `${dan} * ${num} = ${dan*num}\n`;
    이 명령어는
     div.innerText += `${dan} * ${num} = ${dan*num}\n`; 줄여쓸수 있다.
```

![image-20200128093749247](C:\Users\HPE\Desktop\TIL\images\image-20200128093749247.png)



```html
<html>
    <head>
        <script>
            window.onload = function() {
                let div = document.getElementById("display");
                for (let num = 1; num <= 9; num ++) {
                    for (let dan = 2; dan <= 9; dan ++) {
                        div.innerText = div.innerText + `${dan} * ${num} = ${dan*num} \t`;
                    }                
                    div.innerText = div.innerText + '\n';
                }
            }
        </script>
    </head>
    <body>
        <pre id="display"></pre>
    </body>
</html>

# id : div 태그 요소의 값/ 위의 문서에서 element명을 가지고 찾아야 하는데, getElementById로 'display' 통해 이름 부여
# tab은 공백인식 > pre로 정리
# pre는 입력한 그대로 출력
```

![image-20200128101546257](C:\Users\HPE\Desktop\TIL\images\image-20200128101546257.png)



# 반복문

## For 반복문

> 조건(while 반복문)보다 횟수에 비중을 둘 때, 사용하는 반복문

```html
for (초기식[변수초기화]; 조건식; 종결식[변수의 증감]) {
문장
}
```



* 숫자 작아지기

```html
for (let i = 10 ; i >= 0 ; i -- ) {
    console.log(i);
}
## i --는 하나씩 빼는것
10부터 0까지 숫자가 나타남
```

![](C:\Users\HPE\Desktop\TIL\images\image-20200128093113657.png)



* 구구단

```html
for (let dan=2; dan <=9 ; dan ++) {
    for (let num = 1 ; num <= 9; num ++) {
        console.log(`${dan} * ${num} = ${dan*num}`);
    }
}
> 구구단 2단부터 하나씩 커짐
```

![image-20200128093749247](C:\Users\HPE\Desktop\TIL\images\image-20200128093749247.png)



* 초당 연산횟수

  ```html
  <html>
      <head>
          <script>
              window.onload = function() {
                  //  P107 코드 4-13
                  //  1초 동안 for 문이 실행된 회수를 출력
  
                  //  현재 시간을 밀리세컨드(1/1000초) 단위로 반환
                  const startTime = new Date().getTime();
                  let cps = 0;
                  for (cps = 0; new Date().getTime() < startTime + 1000; cps ++) {
                      // do nothing ...            
                  }
                  document.getElementById("display").innerText = `1초 동안 수행된 for 문 회수 : ${cps}`;
              }
          </script>
      </head>
      <body>
          <div id="display"></div>
      </body>
  </html>
  ```

  ![image-20200128102950803](C:\Users\HPE\Desktop\TIL\images\image-20200128102950803.png)

## For in 반복문

> 배열이나 객체를 단순 for 반복문과 동일한 기능 수행

```html
          window.onload = function() {
                let fruits = [ '사과', '딸기', '바나나', '배' ];

                console.log('for 문을 이용한 출력');
                for (let i = 0; i < fruits.length; i ++) {
                    console.log(fruits[i]);
                }

                console.log('for in 문을 이용한 출력');
                for (let i in fruits) {
                    console.log(fruits[i]);
                }

                console.log('forEach 문을 이용한 출력 1');
                fruits.forEach(function(i) {
                    console.log(i);
                });

                console.log('forEach 문을 이용한 출력 2');
                fruits.forEach(i => {
                    console.log(i);
                });
            }
```



## 중첩반복문

> 중첩해서 반복하는 것

```html
<html>
    <head>
        <script>
            window.onload = function() {
                let output = '';
                for (let i=1; i<=9; i++){
                    //세로줄이 9까지
                    output='';
                    // 이게 있어야 줄바뀌면 초기화
                    for (let j=1; j<=i; j++){
                        //가로 숫자가 i만큼만 증가(123456789)
                        output += j;
                        //j 앞뒤에 ''표시하면 j가 찍히고, 그냥 j쓰면 숫자가 찍힘
                    }
                    console.log(output);
                    //각 줄에서 줄의 숫자만큼만 찍혀서 표시된다
                }
                document.getElementById("display").innerHTML += output+'<br>';
                //br 줄바꾸기
                //pre는 '/n'쓰면되지만, 예쁘지 않아 HTML형태로 보여주는것을 좋아함>br사용
            }
        </script>
    </head>
    <body>
        <div id="display"></div>
    </body>
</html>
```



## break 

> switch 조건문이나 반복문을 벗어날 때 사용 
>
> 반복문에서 조건이 항상 참이면 무한 반복할 수 있으므로, 이런 무한루프 벗어날 때 break 키워드 사용해야 벗어날 수 있다.

```html
for (let i =0; true; i ++){
    //'0번째 반복입니다' 부터 시행
    //가운데가 true이므로 무한반복 > true일 때 if 시행
    console.log(`${i}번째 반복입니다.`)
    // 글자는 작은따옴표''가 아닌 ``이용
    if (!confirm("계속할까요?")) {
   // '취소'를 누르면 !로 if값인 break 시행 
(취소 > false > !false> break> 반복문 빠져나옴)
        break;
//break는 반복문을 빠져나올때 사용 > for 반복문을 벗어나 '프로그램을 종료합니다'시행
//반대로 continue를 만나면 for 반복문 다시 시행(다음 루프 시행)
    }
}
console.log(`프로그램을 종료합니다.`);
            }
```



# 익명함수

## 선언문 형태

```html
// 선언
function 함수이름 (매개변수) { 함수본문 }

// 호출
함수이름(매개변수);

```

```html
function sigma(n) {
//sigma에 숫자가 들어옴

let sum = 0;
for (let i=1; i <=n ; i ++){
    sum += i
}
return sum;
}

let num = prompt("숫자를 입력하세요.");
console.log(`1~${num} 합은 ${sigma(num)}입니다.`)
```

![image-20200128141948206](C:\Users\HPE\Desktop\TIL\images\image-20200128141948206.png)

* 동일한 이름으로 함수를 정의하면, 마지막에 정리한 것으로 호출된다

```html
function doSome(x,y) {return x+y; }
function doSome(x,y) {return x*y; }

console.log(doSome(3,4));

// 결과값 : 12
```

* 선언의 경우에는 console을 먼저 호출하고, 이후 let을 통해 함수를 선언해도 동작가능

  그러나 표현식의 경우에는 console을 먼저 호출하면 오류 발생

  따라서 큰 프로젝트의 경우에는 선언식을 사용하는 것을 추천



## 표현식 형태

```html
//선언
let 함수이름변수 = function (매개변수) {함수본문};

// 호출
함수이름변수(매개변수);
```

```html
let sigma = function(n) {

let sum = 0;
for (let i=1; i <= n ; i ++){
    sum += i;
}
return sum;
}

let num = prompt("숫자를 입력하세요.");
console.log(`1~${num} 합은 ${sigma(num)}입니다.`)
```

* 표현식도 마찬가지로 동일한 함수의 이름을 정의한 경우, 마지막 결과가 호출된다.

```html
var doThing = function(x,y) {return x+y;}
var doThing = function(x,y) {return x*y;}

console.log(doThing(4,5));

// 결과값 : 20
```

* console을 먼저 호출한 경우에는 뒤의 함수가 아직 정의되지 않아 오류 발생



## 재정의

```html
var f = function () {console.log("#1 f is called."); };
// 표현식
function f() { console.log("#2 f is called."); };
//선언문

f();
```

```html
function f() { console.log("#2 f is called."); };
var f = function () {console.log("#1 f is called."); };

f();
```

* 두 순서에 대해 모두 결과  "#1 f is called."가 나타난다

동일한 f에 대해 두번 정의되었는데, 동일한 함수에 대해서는 마지막에 정의한 것이 실행된다.

하지만 선언문형태와 표현식 형태로 다른 경우에는 선언문이 먼저 생성되고, 익명함수가 나중에 생성된다. 따라서 나중에 생성된 표현식 형태인 #1이 호출된다.



## Array

## Console

* console.log : 값을 그대로 보여줌

* | **메소드 이름** | **설명**                                          |
  | --------------- | ------------------------------------------------- |
  | dir(object)     | 자바스크립트 객체의 속성들을 출력합니다.          |
  | time(id)        | 실행 시간을 측정하기 위한 시작 시간을 기록합니다. |
  | timeEnd(id)     | 실행 시간을 측정하기 위한 끝 시간을 기록합니다.   |



## Argument

* 배열 비슷한 개체

```html
//매개변수로 전달된 숫자 값의 합을 구하는 함수를 정의

function sumAll() {
    console.log(typeof arguments);
    // typeof : 뒤의 요소가 어떤 형태인지 알려준다
console.log(arguments);
//모든 함수는 argument를 가진다
}
console.log(sumAll(1, "하나", 2, "둘", 3, "셋"));
```

![image-20200128152332144](C:\Users\HPE\Desktop\TIL\images\image-20200128152332144.png)

```html
function sumAll() {
    let sum=0;
for (i of arguments) {
    if (!isNaN(Number(i))){
// !isNaN : 숫자가 아닌게 아니면 = 숫자이면
       sum += i; 
    }
}
return sum;
}
console.log(sumAll(1, "하나", 2, "둘", 3, "셋"));
// 1,2,3만 숫자이므로 이를 더하면 결과 값은 6이다.
```



# 가변인자함수

* 매개변수의 개수가 변할 수 있는 함수

```html
console.log("f() 호출 전")
function f() {
    console.log("return 전");
    return;
//return이 나오면 함수를 벗어나 "return후"가 실행되지 않음
console.log("return 후")
}
console.log("f() 호출 후")
```

* 첫번째 3의 배수 호출

```html
function f() {
for(let i of arguments) {
    if(i%3===0) return i;
};
// foreach는 length만큼 인수를 하나씩 가져올 수 있음
// foreach 대신에 find함수를 이용해 하는 방식을 추천
// 배수는 곱하기로 표현하는 것이 아닌 인수를 배수인 숫자로 나누어 나머지로 확인
}

console.log(f(3));
console.log(f(3,7,11));
console.log(f(1,7,11,15,20,12));
console.log(f(1,7,11,15));
```

```html
function f2(){
return Array.from(arguments).find(i => i%3 === 0);
}
function f2(){
return [...arguments].find(i => i%3 ===0);
//arguments는 전달인자
//...는 펼쳐보이는 것
}
let f = f2
```



# 내부함수

* 여러사람이 함께 프로그램을 개발하다보면 함수의 이름이 겹칠 수 있음. 따라서 함수의 범위를 일정 범위로 제한하기 위해 '내부함수'를 이용
* 함수 내부에서 함수를 정의(선언)

```html
function 외부함수() {
	function 내부함수1() {...}
	function 내부함수2() {...}
}
```

```html
function pythagoras (width,height) {
    return Math.sqrt( square(width) + square(height) );
//아래서 구현한 square 함수를 인수로 넣기
}
function square(x) {
    return x*x;
}
console.log(pythagoras(3,4));
```

```html
function pythagoras (width,height, hypotenuse) {

    if (square(width)+square(height)===square(hypotenuse)){
        return true;
    } else {
        return false;
    }

}
function square(x) {
    return x*x;
}
console.log(pythagoras(3,4,5));
// 직각삼각형이므로 'true' 결과값으로 나타남
```



* 동일한 이름의 외부함수와 내부함수가 있다면, 내부함수를 우선 활용한다.

```html
 //  피타고라스 정리를 이용한 빗변의 길이를 구하는 pythagoras 함수를 정의       
            function pythagoras (width, height) {
                function square(x) {
                    return x * x;
                }   
                return Math.sqrt(square(width) + square(height));
            }
            
            //  같은 이름의 다른 기능을 함수로 구현
            function square(width, height, hypotenuse) {
                if (width * width + height * height === hypotenuse * hypotenuse) 
                    return true;
                else 
                    return false;
            }            
            console.log(pythagoras(3, 4)); 
```



# 자기호출함수

* 생성하자마자 한 번만, 지금바로 호출되는 함수

* 기존의 let은 정의하고, 별도로 호출해야한다.

  자기호출함수는 function을 괄호로 묶고, ();로 바로 호출

```html
(function(){
};)();
```



# 콜백함수

* 동기화 방식 : 호출하는 결과를 확인하고, 다음으로 넘어간다

  비동기화 방식 : 함수를 호출하고 결과가 아직 도달하지 않아도 다음 처리로 넘어감

```html
    let f1 = function(x, y) { return x + y; };
            f1(2,3);

            function callTenTimes(paramc) {
                for (var i = 0; i < 10; i ++) {
                    paramc();
                }
            }
            var fc = function() {
                console.log('함수 호출');
            };
            callTenTimes(fc);
```



# 함수를 리턴하는 함수

* 함수를 매개변수로 리턴 가능

* 함수를 리턴하는 함수를 사용하는 이유는 클로저(closure) 때문

  클로저란, 개체 지향 비슷하게 활용하는 것이다. 정의가 다양한데, 함수안의 변수인 지역변수를 남겨두거나 TEST 함수로 생성된 공간, 리턴된 함수 자체, 살아남은 지역 변수 output

  즉, 지역변수를 함수 외부에서 사용할 수 있게 한다.

```html
            function returnFunction() {
                return function() { 
                    console.log("^^"); 
                };
            }

            let f = returnFunction();
            console.log(f);
            f();
            
            returnFunction()();
```

