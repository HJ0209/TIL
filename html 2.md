---
typora-root-url: images
---

20.01.23

* 클라우드에서 왜 이 기술이 마이크로서비스에 적합한지 고려 필요 > 분화되는 이유



# HTML

> [HTML사이트](https://www.w3schools.com/tags/tag_doctype.asp)



* 닫는 표시 : </a>. </p> 
  * 닫는 표시가 없는 경우가 있음 
  * ex) IMG의 경우에도 닫는 표시가 없지만, <img /> 등으로 닫는 것 추천

* LIST : 동일한 레벨의 정보가 나열되는 것

  ```html
  <ul> > : unordered list_순서에 관계없이 나열 > 그냥 나열만 되면 되는 것이지 순서는 상관없음 > 숫자가 아닌 동그라미나 네모 등으로 표시된다
       
  </ul>
  ```

* table tag

  * tr : row / th : table header / td : table data

  ```html
  <th colspan="2">Name</th>
  : 열 두개를 병합해 셀 내용을 'Name'으로 설정
  
  <td rowspan="3">70</td>
  : 행 3개를 병합해 셀 내용을 '70'으로 설정
  ```

  * 서로 다른 국가에 대해서도 'Cities'라고 묶어 전체 항목에 지정 가능 > Select : `#`, `.a`

  ```html
  h2{color: blue;} _ 제목만 파랑색으로 변경가능
  #first {color: yellow;} _ class 내용을 노랑색으로 변경가능
  #은 뒤에 나오는 div에서 id를 찾는 기호 (#[ID이름])
  class를 찾을 때에는 .a
  
  <div class="cities" id="first">
  </div>
  : div로 나누어 우선 class의 색인 white가 먼저 지정되고,
  이후 first의 yellow가 해당하는 부분을 덮어쓰고
  이후 H2의 파랑색이 해당부분을 덮어쓴다
  ```

  

* `let`은 선언 변수로, 한 번 실행하면 이미 실행되고 있어 다시 실행하면 오류 발생

  

  

# JAVA Script

> cmd에서 javascript 저장된 폴더 열고,
>
> `npm init -y`입력하고, 
>
> `npm install http-server -g`입력하고, 
>
> `npx http-server`입력하면  
>
> 크롬에서 `localhost:8080/[파일명].html` 입력하면 창 열림 + `F12`로 console 확인



## 1. 연산

```html
'52 + 123'
> "52 + 123" : ''가 문자로 인식됨

typeof('52 + 123')
> "string" : 문자

52 + 123
> 175 : ''표시 없으면 숫자로 인식

typeof(52+123)
> "number" : 숫자

'52'+'123'
> "52123" : ''표시를 문자로 인식해 나열
```

* 숫자와 문자열 덧셈 연산은 문자열이 우선해 문자열 결합으로 인식

* 숫자와 문자열 뎃셈을 제외한 곱셈, 뺄셈, 나눗셈은 숫자가 우선

  문자 등 다른 데이터 타입을 숫자형으로 변환할 때에는 number() 함수를 사용 : 명시적 형변환

  숫자 등 다른 데이터 타입을 문자형으로 변환할 때에는 string()함수를 사용

  ```html
  '52' * 100
  > 5200 : 문자와 숫자 곱하기는 숫자로 나타남 (강제적으로 형변환 = 명시적 형변환)
  ```

  함수를 직접 사용하는 것이 명시적 형변환

  

* `NaN` : 숫자가 아닌 데이터 (Not a Number)

  자료형은 숫자이나 자바스크립트로 나타낼 수 없는 숫자

  ```html
  Number("abcd")
  > NaN : number 함수는 숫자로 바꾸는 것이지만, abcd가 문자열
  ```

* `isNaN`: javascript의 다른 모든 값과 달리 `NaN`은 같음 연산(==, ===)으로 판별할 수 없음 > 모두 false로 평가되므로 NaN여부를 알기 위해서는 `isNaN()`이 필요하다.



## 2. 변환

* Booleam() 함수 : 다른 자료형을 불(bool) 자료형으로 변환 (교재 61p)

* 자동 형변환(=암시적 형변환)의 문제점

  ```html
  <script>
  	console.log(''=false); 
      console.log(''=0);
      console.log(0=false);
      console.log('273'=273);
  </script>
  ```

  * 4개 모두 자동 형변환되어 true로 바뀜
  * 혼란을 야기할 가능성이 있음
  * 이를 해결하기 위한 것이 `일치연산자`

* 일치연산자



## 3. LET

* `Var` 키워드와의 비교

  * `Var`보다는 `let`키워드 사용 추천

  * 스코프(유효범위)는 `{}`로 감싼 부분 > 특정 스코프 안에서 선언한 변수는 해당 스코프 안에서만 사용할 수 있다.

  * javaScript는 스코프가 다름에도 불구하고, 다른 스코프에서 선언한 변수를 참조할 수 있다.

  * `var`은 '전역 스코프 위치'에 변수를 선언하는 키워드로, 문서의 최상위 영역(윈도우 브라우저)에 위 변수를 선언한다는 것 

    → 이는 규모가 작을 때에는 눈으로 볼 수 있어 문제되지 않지만, 문서가 길어지게 되면 값이 어디서 변화하는지 모르기 때문에 문제 발생할 수 있다. 

  * `let`은 특정 스코프 내부에서만 변수를 사용

    

* 익명함수 : 1회적으로 시행

  ```html
  <!DOCTYPE html>
  
  <html>
      <head>
          <script>
              for (let i = 0; i < 3; i ++) {
                  setTimeout(() => {
                      console.log(i);
                  }, 1000 * i);
              }
          </script>
      </head>
      <body>
          
      </body>
  </html>
  
  ```

  * 일정시간이 지나면 i값에 시간 입력

  * '3'만 반복적으로 수행 : 앞에 동그라미 숫자는 같은 값이 반복적으로 찍히는 것

    

  * 동기화 방식 : 순서대로 진행(하나가 끝나야 다음 일 진행)

    비동기 방식 : 하나가 끝나지 않아도 다음 일 진행 가능

  * ```html
    <!DOCTYPE html>
    
    <html>
        <head>
            <script>
                for (var i = 0; i < 3; i ++) {  >> i 변수 입력
                    ((i)=> {
                       setTimeout(() => {       >> i변수가 입력된 이후에 settime
                           console.log(i);
                       }, 1000*i)               >> 1000*i 이후에 실행
                    })(i);
                }
            </script>
        </head>
        <body>
    
        </body>
    </html>
    ```

    ∴ `var`이 `let`보다 들어가는 변수가 더 많으므로, 되도록 `let`이용하자!



## 4. 연습문제 (p.76)

* 표현식 :
* 키워드 : 예약어
* 식별자 : 변수명, 함수명, 속성명, 메소드명 등 이름



* ```html
  <!DOCTYPE html>
  
  <html>
      <head>
          <script>
            
            function add(x,y) {
                return x + y;
            }
  
            console.log("4+5= ", add(4,5));
  
  
          </script>
      </head>
      <body>
  
      </body>
  </html>
  ```

  add 함수를 정의하고, console에서 add(4,5)를 수행하면 '9'라는 값이 나온다.

  순서를 바꾸어 함수 정의하기 전에 console부터 나오고, 이후 add 함수 정의해도 동일하게 '9'라는 값이 나타난다.





## 5. 조건문

* Date (p.80)

```html
        <script>
          
let date = new Date();
let hour = date.getHours();

console.log('현재는 ' + hour + '시입니다.');

        </script>
```

![image-20200123143447246](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200123143447246.png)



* 삼항연산자 : 항이 3개

  ```html
  조건식 ? 참일때 실행하는 문장 : 거짓일때 실행하는 문장
  ```

* 짧은 조건문

  ```html
  A||B ; A 또는 B 둘 중 하나가 참이면 참 (A와 B 모두 거짓이어야 거짓)
  A&&B ; A와 B 모두 참이어야 참 (하나라도 거짓이면 거짓)
  ```

  ```html
  let breakTime = false;
  breakTime || console.log ("공부를 합니다.");
  > 거짓이므로 "공부를 합니다." 나타남 / 반대로 true로 두면, 아무것도 나타나지 않음
  ```

  ```html
  let input =prompt("숫자를 입력하세요");
  > 팝업창으로 '숫자를 입력하세요' 나타남
  
  input %2 === 0 || console.log ("홀수")
  input %2 === 0 && console.log ("짝수")
  > 나누기2 해서 0이 아니면 '홀수', 0이면 '짝수' 나타남
  ```



## 6. 반복문

```html
for ([초기값]; [조건문]; [증가분]) {
[조건문을 만족하는 경우 수행할 구문]
}
```



* 배열 (p.98)

  ```html
  let array = [];
  > [] 안의 내용을 순서대로 출력
  
  console.log(arr[0]);
  > 0번째 내용을 출력 (배열은0부터 숫자 올라감!)
  
  console.log(arr.length);
  > 배열의 개수(배열은 0부터 올라가므로, length값은 배열 마지막수+1 )
  ```

* 배열 방법 4가지

  ```html
  console.log(arr[0]);
  console.log(arr[1]);
  console.log(arr[2]);
  console.log(arr[3]);
  console.log(arr[4]);
  console.log(arr[5]);
  ```

  ```html
  for(let i = 0; i < arr.length; i ++){
                                console.log(arr[i]);
                                }
  ```

  ```html
  arr.forEach(function(item) { 
      console.log(item); 
  });
  ```

  ```html
  arr.forEach(ii => console.log(ii));
  > forEach : 배열의 값. 내가 직접 값을 몰라도 자동으로 배정
    익명함수로 화살표(=>)로 바꾼것
  ```

  > 코딩은 같은 결과라도 짧게 만드는 것이 좋으므로, 맨아래 명령어가 더 좋음!

* 배열 추가

  ``` html
  let arr = ['가','나','다','라','마','바','사'];
  arr.push("xyz");
  > push 하면 배열의 가장 마지막에 추가되고, length 추가
  > 결과 : 가나다라마바사xyz
  ```

* While 반복문 : ()안의 조건문이 참인 경우, 계속 반복해서 동작

  ```html
         let value = 0;
   
   // 현재 시간을 밀리세컨드(1/1000초) 단위로 가져오는 것
   let startTime = new Date().getTime();   
  
   while (new Date().getTime() < startTime + 1000) {
       value ++;
   }
  
   console.log("1초 동안 while 루프를 수행한 횟수 : " + value);
  
  > value : 1초 동안 while 루프를 수행한 횟수를 나타내는 함수
  > 결과) 1초 동안 while 루프를 수행한 횟수 : 3387993
  ```

  ```html
  let input= prompt("숫자를 입력하세요.")
  
  while (!isNaN(input)) {
      input %2 === 0 ? console.log("짝수") : console.log("홀수");
      input= prompt("숫자를 입력하세요.");
  }
  console.log("종료")
  > isNaN이면 숫자가 아닌 것을 뜻하므로, 앞에 !를 붙이면 숫자가 아니지 않으므로 숫자를 뜻한다. 따라서 ()안에서 input이 숫자이면 {}의 내용을 반복해서 실행
  > {} 안의 input= prompt("숫자를 입력하세요.")가 없으면 한번 숫자가 입력되면 계속 그것만 실행된다.
  앞에서 한 번 input 하면 짝수 또는 홀수가 나오고 다시 숫자를 입력하세요가 팝업으로 뜨도록 다시 입력하는 것
  > 문자가 입력되면 !isNaN(input)이 false가 되므로, {}가 실행되지 않고 뒤의 내용 실행된다.
  ```

  

* Do While 반복문 : 거짓여부와 상관없이 내부의 문장을 최소 한번은 실행해야 하는 경우

  ```html
          const MIN = 1;
          const MAX = 20;
   
          let answer = Math.floor(Math.random() * (MAX - MIN + 1)) + MIN;
          let guesses = 0; // 사용자가 입력한 회수
          let input;
          do {
              input = prompt(`${MIN} ~ ${MAX} 사이의 숫자를 입력하세요.`);
              input = Number(input);
              guesses ++;
   
              if (input > answer) {
                  console.log("입력한 값 보다 작은 값을 입력하세요");
              } else if (input < answer) {
                  console.log("입력한 값 보다 큰 값을 입력하세요");
              } else {
                  console.log(`정답입니다. (시도회수: ${guesses})`);
              }
          } while (input !== answer);
  ```



* For 반복문

  ```html
          const fruits = [ "사과", "오렌지", "딸기", "바나나" ];
   
          console.log("방법1. for loop");
          for (let i = 0; i < fruits.length; i ++) {
              console.log(fruits[i]);
          }
   
          console.log("방법2. for in");
          for (let i in fruits) {
              console.log(fruits[i]);
          }
   
          console.log("방법3. forEach");
          fruits.forEach(function(fruit) { 
              console.log(fruit);
          });
   
          console.log("방법4. forEach + arrow function");
          fruits.forEach(fruit => console.log(fruit));
  ```

  