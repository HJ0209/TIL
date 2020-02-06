20.01.29



# 함수

* 함수 설정방식

```html
        function sum (a, b = 0, c = 0) {
            return a + b + c;
        }

또는
        let sum = function (a, b = 0, c = 0) {
            return a + b + c;
        };

또는

        let sum = (a, b = 0, c = 0) => {
            return a + b + c;
        };
```

이렇게 정리하면 꼭 인수 3개가 들어가지 않아도 오류 나지 않음



## 전개연산자

> 함수 또는 배열에 적용 / 마침표 3개(...)를 찍어 표기

* 함수 선언할 때의 전개 연산자

```html
function test(...values) {
    console.log(arguments[0])
    console.log(arguments[1])
    console.log(arguments[2])
}
test(1,2,3);
```

`arguments` 대신 `values`라고 입력해도 가능

![image-20200129093737601](C:\Users\HPE\Desktop\TIL\images\image-20200129093737601.png)

1,2,3 배열형태로 결과값이 나타남

배열인 test(1,2,3)에서 첫 번째 숫자가 0부터 지정되기 때문이다.

만약 `test(1,4,6,7,8)`에서 `console.log(arguments[3])`으로 입력하면 4번째 숫자인 `7`이 나타난다.



* 반드시 포함되어야 할 값이 있는 경우

```html
function test(a,b,...values) {
    console.log('a',a);
    console.log('b',b);
    values.forEach(i => console.log('values',i));
}
test(1,2,3,4,5);
```

![image-20200129094050166](C:\Users\HPE\Desktop\TIL\images\image-20200129094050166.png)

a와 b를 별도로 빼서 반드시 포함되도록 형성

예를 들어, 회원가입 페이지에서 이름, 성별 등을 반드시 포함되도록  하고 나머지는 선택 사항일 때 주로 이용



* 값을 쓰지 않고 값 분배

  ```html
  function test(a,b,...values) {
      console.log('a',a);
      console.log('b',b);
      values.forEach(i => console.log('values',i));
  }
  
  let value=[1,2,3,4,5];
  test(value);
  ```

  * test함수 안에 값을 `,`로 넣지 않고 한꺼번에 넣으면 모두 A로 처리된다.

    ![image-20200129094349406](C:\Users\HPE\Desktop\TIL\images\image-20200129094349406.png)

    이렇게 모두 `a`에 포함된 것으로 나타남

    

  ```html
  function test(a,b,...values) {
      console.log('a',a);
      console.log('b',b);
      values.forEach(i => console.log('values',i));
  }
  let value=[1,2,3,4,5];
  test(...value);
  ```

  * 이렇게 `value`앞에 `...`으로 전개를 넣어야 값이 전개되어 각 value값에 뿌려져 원하는 결과가 나타난다.

    ![image-20200129094558532](C:\Users\HPE\Desktop\TIL\images\image-20200129094558532.png)

  ```html
  function test(a,b,...values) {
      console.log('a',a);
      console.log('b',b);
      values.forEach(i => console.log('values',i));
  }
  let value=[1,2,3,4,5];
  test(...value, ...value);
  ```

  * 만약 이렇게 `...value`가 두 번 들어가면 1부터 5까지의 배열이 두 번 전개된다.

    ![image-20200129094715925](C:\Users\HPE\Desktop\TIL\images\image-20200129094715925.png)

    

* 전개연산자는 객체, 배열을 활용해 데이터를 정리할 수 있다.

  | 학번 | 이름 | 전공 |
  | ---- | ---- | ---- |
  | 2019 | A    | IT   |
  | 2020 | B    | ECO  |

  * 학번, 이름, 전공이 `객체`가 되고, A와 B가 배열이 된다. 전개 연산자를 이용해 [2019,A,IT]와 [2020,B,ECO]가 각각의 배열이 되어 데이터를 입력하거나 불러올 수 있다.



# 객체

* 속성 이름은 `['name']`으로 정리하거나 `.name`으로 정리할 수 있다. 그러나, 공백문자를 가지고 있는 경우에는 `.`으로 작성할 수 없다.

* 배열은 요소에 접근할 때, 인덱스를 이용 (배열 위치 숫자)

  반면 객체는 요소의 이름을 이용해 접근 (문자로 된 이름)



## 메서드

* 객체 내부의 값인 method (객체가 가진 속성)

  ```html
  let person={
      firstName : "길동",
      lastName : "홍",
      id : 1234,
      getFullName : function() {
          return this.lastName+this.firstName;
          //this를 통해 person객체의 위치를 나타냄
          // person이 가지고 있는 lastName, firstName을 나타냄
      }
  };
  console.log(person);
  console.log(person.lastName+person.firstName);
  console.log(person.getFullName());
  // 이렇게 많이 쓸 것 같은 메소드를 getFullName처럼 별도로 정의하기
  ```



* 객체 내부의 주소 지정

  ```html
  let person={
      firstName : "길동",
      lastName : "홍",
      id : 1234,
      'favorite color' : [ 'aaa', 'bbb'],
      getFullName : function() {
          console.log(this);
          return this.lastName+this.firstName + this['favorite color'][1];
          // 'favorite color'은 공백 있어 대괄호 이용해 언급
          // [1]은 'aaa','bbb'에서 앞의 주소가 0, 뒤의 주소가 1이므로 1인 'bbb' 나타남
      }
  };
  console.log(person);
  console.log(person.lastName+person.firstName);
  console.log(person.getFullName());
  ```

  

## 반복문

### For in



## 키워드

### in

### with



## 속성 추가 및 제거

* 속성 추가

  ```html
  let person = {};
  
  person.name = '홍길동';
  person.age = 23;
  person.isMarried = false;
  // person 객체가 가지고 있는 모든 속성과 속성값을 반환하는 메소드
  person.toString=function(){
  let output = '';
  for (let key in person) {
     if (key!= 'toString') {
      output += `${key} : ${person[key]}\n`;
  }
  }
  return output;
  };
  
  console.log(person.toString())
  ```

  ![image-20200129113934913](C:\Users\HPE\Desktop\TIL\images\image-20200129113934913.png)

* 속성 삭제

  ```html
  let person = {};
  
  person.name = '홍길동';
  person.age = 23;
  person.isMarried = false;
  person.toString=function(){
  let output = '';
  for (let key in person) {
     if (key!= 'toString') {
      output += `${key} : ${person[key]}\n`;
  }
  }
  return output;
  };
  
  delete person.name;
  // 속성 삭제. 단항 연산자
  console.log(person.toString())
  ```

  ![image-20200129114059064](C:\Users\HPE\Desktop\TIL\images\image-20200129114059064.png)

  * `Name:홍길동` 사라진 것 확인 가능



## 데이터 관리

* 가로 한 행이 객체 (학번, 이름, 성적 등의 내용이 속성. 이에 대응되는 값이 객체)

  이를 {}로 표현한 것을 나열한 것이 배열

  push를 통해 배열을 객체로 넣을 수 있음

```html
let students = [];
    students.push({ name: 'aaa', korean: 46, math: 65, english: 25, science: 64 });
    students.push({ name: 'bbb', korean: 56, math: 63, english: 85, science: 62 });
    students.push({ name: 'ccc', korean: 56, math: 63, english: 22, science: 43 });
    students.push({ name: 'ddd', korean: 12, math: 25, english: 26, science: 23 });
    students.push({ name: 'eee', korean: 18, math: 85, english: 25, science: 25 });
    students.push({ name: 'fff', korean: 32, math: 22, english: 79, science: 25 });
    students.push({ name: 'ggg', korean: 52, math: 26, english: 42, science: 42 });
    students.push({ name: 'hhh', korean: 22, math: 25, english: 41, science: 56 });
    students.push({ name: 'iii', korean: 87, math: 79, english: 25, science: 86 });
    students.push({ name: 'jjj', korean: 24, math: 42, english: 71, science: 88 });

//학생별 총점, 평균
students.forEach(student => {
    // 앞에는 배열 전체 이름인 students를 넣고 뒤에 불러오는 것은 배열 하나이므로 students가 아닌 student
    console.log(typeof student);
    console.log(student);
    student.getSum = function() {
        return this.korean+ this.math + this.english + this.science;
    };
        // stuentd에 불러온 점수라는 것을 알리기 위해 this 이용
        // 함수 끝나면 ; 넣는 것 까먹지 말기

    student.getAvg = function() {
        return this.getSum()/4;
    };
        // student.이 아닌 this 이용
        // getSum은 함수이므로 뒤에 ()나와야
});

//학생별 총점, 평균점 출력
students.forEach(student => {
    // 배열불러와야 하므로 students.로 나와야 
    with(student) {
                console.log(`이름 : ${name}, 총점 : ${getSum()}, 평균: ${getAvg()}`);
            }
    //함수 끝나면 까먹지 말고 ; 입력
});
```

* 함수를 먼저 입력하는 경우

```html
function makeStudent(name, korean, math, english, science) {
    let result = {
        name : name,
        korean : korean,
        math : math,
        english : english,
        science : science,
        getSum : function(){
            return this.korean + this.math + this.english + this.science;
        },
        getAverage : function(){
            return this.getSum()/4;

        }
    };
    return result;
}
let students=[];
students.push(makeStudent('aaa', 46, 65, 25, 64));
students.push(makeStudent('bbb', 56, 63, 85, 62));
students.push(makeStudent('ccc', 56, 63, 22, 43));
students.push(makeStudent('ddd', 12, 25, 26, 23));
students.push(makeStudent('eee', 18, 85, 25, 25));
students.push(makeStudent('fff', 32, 22, 79, 25));
students.push(makeStudent('ggg', 52, 26, 42, 42));
students.push(makeStudent('hhh', 22, 25, 41, 56));
students.push(makeStudent('iii', 87, 79, 25, 86));
students.push(makeStudent('jjj', 24, 42, 71, 88));
// makeStudent 함수를 먼저 정의한 경우에는 배열을 넣기 위해 push(makeStudent 함수 이용)
// 앞에 함수로 위치별 이름을 미리 지정했기 때문에 ,만으로도 데이터 입력 가능

students.forEach(student => {
    with(student) {
        console.log(`이름 : ${name}, 총점 : ${getSum()}, 평균 : ${getAverage()}`)
        //함수 뒤에는 반드시 ()가 들어가야한다.
    }
})
```



## 매개변수

* obj 객체는 초기화를 하는 것이 중요

  ```html
  //test 함수는 obj 객체를 매개변수로 받아들인다.
  
  function test(obj) {
      obj.valueA = obj.valueA || 0;
      obj.valueB = obj.valueB || 0;
      obj.valueC = obj.valueC || 0;
  
      with(obj) {
          console.log(`${valueA} : ${valueB} : ${valueC}`);
  // obj.valueA 같은 방식보다는 with(obj)로 묶고 값을 나열하는 방식을 추천!
      }
  }
  
  test({valueA : 52, valueB : 273});
  ```

  ![image-20200129134315585](C:\Users\HPE\Desktop\TIL\images\image-20200129134315585.png)

  * 결과가 이렇게 배열 형태로 나타나게 되는데,

    자료가 입력되지 않은 경우에는 초기화로 설정된 값인 0 이 대입된다.

  * 예를 들어, 회원가입의 경우 A : 이름, B : 나이, C : 지역 이런식으로 입력하는 매개변수로 작동할 수 있다.

## 참조 복사, 값 복사

> 값, 객체, 배열 복사 모두 가능

* 값 복사 (하나는 연동되지 않지만, 두개 이상의 값은 연동된다)

```html
let oldValue = 10;
let newValue = oldValue;
console.log(oldValue, newValue); // 10,10

oldValue = 100;
console.log(oldValue, newValue); 
// 100, 10 (이는 값 복사로 서로 연동되지 않는다)

let oldArray = [10,20];
let newArray = oldArray;
console.log(oldArray, newArray); // [10,20] [10,20]

oldArray[0] = 100;
newArray[1] = 999;
console.log(oldArray, newArray);
// [100,999] [100,999] (주소복사되어 서로 연동)
```

![image-20200129135925098](C:\Users\HPE\Desktop\TIL\images\image-20200129135925098.png)



값이 복사되는 것을 `깊은 복사 `/ 주소가 복사되는 것을 `얕은 복사`

(깊은 복사는 원본을 변경해도 영향을 주지 않지만,

얕은 복사는 원본이 변화하면 영향을 받는다)



* 개체의 얕은 복사 (연동 O)

```html
let oldObject = {name : 'aaa', age : 50};
let newObject = oldObject;
console.log(oldObject, newObject);

oldObject.name = 'bbb';
newObject.age = 30 ;
console.log(oldObject, newObject);
```

![image-20200129140331323](C:\Users\HPE\Desktop\TIL\images\image-20200129140331323.png)

이름과 나이가 모두 함께 변경되는 얕은 복사 시행



* 개체의 깊은 복사 (연동 X)

```html
let oldObject = {name : 'aaa', age : 50};
let newObject = oldObject;
console.log(oldObject, newObject);

function clone(obj) {
//clone이라는 함수를 만들자

    let output = {};
    for (let key in obj){
        output[key] = obj[key];
    }
    return output;
    // output을 만들어 obj의 key를 output에 저장된다
	// for을 통해 값을 복사
    // oldobject2와 동일하게 만들어진 객체를 newobject2가 가져가는 것 
}

let oldObject2 = {name : 'xyz', age : 123};
let newObject2 = clone(oldObject2);
console.log(oldObject2, newObject2);

oldObject2.name = 'abc';
newObject2.age = 321;
//숫자는 ''하면 안됨
console.log(oldObject2, newObject2)
```

![image-20200129141011164](C:\Users\HPE\Desktop\TIL\images\image-20200129141011164.png)



* 특히 보안에서 문제된다.

  얕은 복사를 통해 값이 복사되거나, 의도하지 않게 변경되는 경우로 인한 보안에서 문제될 수 있다.



* 배열 복사의 경우

  ```html
  let oldA = [1,2,3,4];
  let newA = oldA;
  console.log(oldA, newA)
  
  oldA[0]=11 ;
  newA[3]=44 ;
  console.log(oldA, newA) 
  // 주소를 공유하기 때문에 값이 둘다 변경 
  [11,2,3,44] [11,2,3,44]로 동일하게 나타남
  
  let newA2 = [...oldA]
  oldA[1] = 101;
  newA2[2] = 202;
  console.log(oldA, newA2)
  // 전개연산자를 통해 배열의 깊은 복사 가능
  [1,101,3,4] [1,2,202,4]로 서로 변경한 값만 바뀐다
  ```

  ![image-20200129143843327](C:\Users\HPE\Desktop\TIL\images\image-20200129143843327.png)



* 객체도 전개함수를 통해 깊은 복사가능

  ```html
  let objA = {name:'aaa', age:10};
  let objB = {...objA}
  console.log(objA,objB);
  
  objA.name = 'bbb';
  objB.age = 20;
  console.log(objA, objB);
  ```

  * 결과값이 {aaa,10} {aaa,10}에서

    {bbb,10} {aaa,20}으로 서로 연동되지 않고 깊은 복사

  * 아직 표준은 아니지만, 3단계까지 통과해 거의 표준화



# 13장. jQuery

## 개요

* 모든 브라우저에서 동작하는 클라이언트 자바스크립트 라이브러리

* 2006년 1월, 존 레식이 BarCamp NYC에서 발표

* 문서 객체 모델(DOM)과 관련된 처리를 쉽게 구현

  ```dom
  <html>
  <head> </head>
  <body> </body>
  </html>
  문서를 작성해 마우스 태그에 따라서 색이 변하는 등으로 처리
  ```

  일관된 이벤트 연결을 쉽게 구현 (마우스클릭 등의 이벤트가 발생하면 이를 일관된 방식으로 알려줌. 원래 브라우저마다 알람이 다름)

  시각적 효과를 쉽게 구현

  ajax 애플리케이션을 쉽게 개발

  -> 이런 기술들로 인해 jQuery가 나오면서 javascirpts의 위상이 올라갔다.

* https://jquery.com/download/

  CDN (어디서나 빠르게 받을 수 있음)

  -> 구글에서 서비스를 제공하는 것을 다운받기 위해서는

  [Google CDN](https://developers.google.com/speed/libraries#jquery)에서 `snippet`을 복사해 붙여 넣기

  ```html
  <html>
      <head>
          <script src="https://ajax.googleapis.com/ajax/libs/d3js/5.15.0/d3.min.js"></script>
          <script>
  
  
          </script>
      </head>
  </html>
  ```

  이를 저장하고 `localhost:8080/test.html`에서 `network`를 확인하면 `jQuery`가 다운받아지는 것을 알 수 있음

  

* 직접 다운받기 위해서는 `visual Studio Code`에서 아래 `Terminal`에서 아래 명령어 입력(구글 이용하면 구글이 서비스를 중단할 우려가 있음)

  ```cmd
  PS C:\Users\HPE\HJ> npm install jquery
  PS C:\Users\HPE\HJ> npx http-server
  ```

  ![image-20200129151652873](C:\Users\HPE\Desktop\TIL\images\image-20200129151652873.png)

  이렇게 콘솔의 네트워크에 `jquery.js`올라온 것 확인하기

  

  ```html
  <html>
      <head>
          <script src="/node_modules/jquery/dist/jquery.js"></script>
          <script>
  
          </script>
      </head>
  </html>
  ```

  소스코드에 추가하기

  ![image-20200129151434553](C:\Users\HPE\Desktop\TIL\images\image-20200129151434553.png)

  이렇게 소스코드 깔린 것 확인하기

  

## 실행

*  문서가 준비되면 매개변수로 전달한 콜백 함수를 실행

```html
window.onload = () => {
    console.log("loaded");
};

$(document).ready( function() {
    console.log("Hello jQuery");
    $("body").text("Hello jQuery");
})
```

![image-20200129152813393](C:\Users\HPE\Desktop\TIL\images\image-20200129152813393.png)

* ```javascript
  window.jQuery = window.$ = jQuery; 동일
  ```



## 기본 선택자

* 블록요소 : h1.p

  `h1`과 `p`는 블록요소여서 한 줄을 통채로 사용한다.

  따라서 p로 작성하면 한 줄이 다 차지 않더라도 다음 명령에서 다음줄로 넘어간다.

  그러나 `span`은 줄이 바뀌지 않고 오른쪽에 연결되어 붙는다.

```javascript
  <body>
        <h1>제목1</h1>
        <p>내용1</p>
        <p> 내용2</p>
        <span> 다른내용</span>
        <span> 다른내용2</span>
    </body>
```

![image-20200129161055843](C:\Users\HPE\Desktop\TIL\images\image-20200129161055843.png)



* 색 변경하기

  ```javascript
         <script>
  $(function() {
      $('*').css(`color`, `blue`)
      // ${*}는 모든 태그 요소를 다 가져오기
      // css는 스타일을 부여
  });
          </script>
  ```

  ![image-20200129154615615](C:\Users\HPE\Desktop\TIL\images\image-20200129154615615.png)

![image-20200129154643606](C:\Users\HPE\Desktop\TIL\images\image-20200129154643606.png)

elements에서도 확인할 수 있음



* 후손 선택자 : Body에서만 색변경

  ```javascript
       <script>
  $(function() {
      $('body *').css(`color`, `blue`)
           // body 아래에 있는 태그만 선택
  });
          </script>
  ```

  ![image-20200129154826165](C:\Users\HPE\Desktop\TIL\images\image-20200129154826165.png)

  * `head`에는 지정되어 있지 않고, `body`에만 색이 지정된 것 알 수 있음



* 요소 선택자 : H1 태그에 대해서만 색변경

  ```javascript
  <html>
      <head>
          <script src="/node_modules/jquery/dist/jquery.js"></script>
          <script>
  $(function() {
      $('h1').css(`background`, `yellow`)
  });
          </script>
      </head>
      <body>
          <h1>제목1</h1>
          <p>내용1</p>
          <h1> 제목2</h1>
          <p>내용2</p>
      </body>
  </html>
  ```

  ![image-20200129155056740](C:\Users\HPE\Desktop\TIL\images\image-20200129155056740.png)

  

* ID 선택자 : 특정한 아이디를 가지고 있는 태그만 지정

  ```javascript
          <script>
  $(function() {
      $('h1').css(`background`, `yellow`);
      $("#title").css('border', '1px solid red');
      // ID는 해당 문서에서 유일해야
  });
          </script>
      </head>
      <body>
          <h1>제목1</h1>
          <p>내용1</p>
          <span> 다른내용</span>
          <h1 id="title">제목2</h1>
          <p>내용2</p>
  ```

  ![image-20200129160129481](C:\Users\HPE\Desktop\TIL\images\image-20200129160129481.png)

* 클래스 선택자

  ```javascript
          <script>
  $(function() {
      $(".right").css('textAlign', 'right');
  
  });
          </script>
      </head>
      <body>
          <h1 class="right">제목1</h1>
          <p>내용1</p>
          <span> 다른내용</span>
          <h1>제목2</h1>
          <p class="right">내용2</p>
  ```

  ![image-20200129160307722](C:\Users\HPE\Desktop\TIL\images\image-20200129160307722.png)

* 다중선택자

  ```javascript
          <script>
  $(function() {
      $("span, #title, .right").css('text-decoration', 'underline');
  });
          </script>
      </head>
      <body>
          <h1 class="right">제목1</h1>
          <p>내용1</p>
          <span> 다른내용</span>
          <h1 id="title">제목2</h1>
          <p class="right">내용2</p>
  ```

  ![image-20200129160520790](C:\Users\HPE\Desktop\TIL\images\image-20200129160520790.png)

## 자식선택자, 후손선택자

https://api.jquery.com/child-selector/

```javascript
자손 선택자 ⇒ $("parent > child") 
// 바로 밑의 자식만 선택
후손 선택자 ⇒ $("parent child")
//모두 선택
```

 ![img](C:\Users\HPE\Desktop\TIL\images\pasted image 0.png)

* DOM

  ```javascript
      <body>
  
          <div>
              <ul>
                  <li>삼각형</li>
                  <li>사각형</li>
                  <li>오각형</li>
                  <ul>
                      <li>111</li>
                      <li>222</li>
                      <li>333</li>
                  </ul>
              </ul>
  
              <ol>
                  <li>a</li>
                  <li>b</li>
                  <li>c</li>
              </ol>
  
          
          </div>
  ```

  ![image-20200129163613572](C:\Users\HPE\Desktop\TIL\images\image-20200129163613572.png)

* div 안의 모든 후손 선택

  ```javascript
     <head>   
  		<script>
  $(function() {
     $("div *").css('color', 'blue')
      // div 밑에 있는 모든 태그 선택.css
  });
          </script>
      </head>
  
      <body>
  
          <div>
              <ul>
                  <li>삼각형</li>
                  <li>사각형</li>
                  <li>오각형</li>
                  <ul>
                      <li>111</li>
                      <li>222</li>
                      <li>333</li>
                  </ul>
              </ul>
  
              <ol>
                  <li>a</li>
                  <li>b</li>
                  <li>c</li>
              </ol>
          </div>
      </body>
  ```

  ![image-20200129164010919](C:\Users\HPE\Desktop\TIL\images\image-20200129164010919.png)

* ```javascript
         <script>
      $(function() {
              //  ID가 menu인 ul 태그 아래 있는 모든 li 태그의 값 색깔을 파란색으로 설정
              $("ul#menu li").css('color', 'blue');
              
              //  ID가 menu인 ul 태그 바로 아래 있는 li 태그의 값에 대해 박스를 출력
              $("ul#menu > li").css('border', '1px solid red');
          });
  
          </script>
      </head>
      <body>
  
          <div>
          <ul id="menu">
              <li>첫번째</li>
              <li>두번째</li>
              <li>세번째
                  <ul>
                      <li>3-1</li>
                      <li>3-2</li>
                      <li>3-3</li>
                  </ul>
              </li>
              <li>네번째</li>
          </ul>
      </div>
  
      </body>
  ```

  ![image-20200129165527151](C:\Users\HPE\Desktop\TIL\images\image-20200129165527151.png)

  * $("ul#menu > li").css('border', '1px solid red');

  바로 자식인 `li`의 내용에 대해 박스 처리가 되었고, 후손인 `ul`에 대해서는 박스 처리가 되지 않음을 확인할 수 있다.

## 속성 선택자

```javascript
<태그명 속성명="속성값" 속성명="속성값">태그값</태그명>
태그 => element
속성 => attribute
```



```javascript
 <head>
        <script src="/node_modules/jquery/dist/jquery.js"></script>
        <script>

        $(function() {
//  속성 선택자 
//  $("엘리먼트이름[속성이름='속성값']")
//  <form> 아래에서 사용하는 사용자 입력을 처리하는 태그를 제어할 때 사용
//  <input type="text"> <input type="number"> <input type="radio"> ...
     $('ul[id="submenu"] li').css('color', 'red');
     $('ul#submenu li').css('background', 'yellow');
        });

        </script>
    </head>

    <body>
        <div>

            <ul id="menu">
                <li>첫번째</li>
                <li>두번째</li>
                <li>세번째
                    <ul id="submenu">
                        <li>3-1</li>
                        <li>3-2</li>
                        <li>3-3</li>
                    </ul>
                </li>
                <li>네번째</li>
            </ul>
        </div>
    
    </body>
```

![image-20200129170701383](C:\Users\HPE\Desktop\TIL\images\image-20200129170701383.png)

	* ID인 `submenu`인 3-1, 3-2, 3-3 에 대해 빨간색 글자 & 노랑 배경