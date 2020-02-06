20.02.05



# REACT02

* BASE64 : 6Bit > 한 비트당 0~63으로 64글자

  a~z  A~Z  0~9 / + : 총 64글자

  

## 코드를 분할하기

> 첫 페이지 로딩 시간 줄이기 & 불필요한 코드 다운을 막기 위함
>
> (첫째날, WEBPACK을 이용한 코드 합치는 것 연습)



### 1. js파일 만들기

* C:\react\cra-test\src\Todo.js

```javascript
import React from 'react';

export function Todo({title}) {
    return <div>{title}</div>;
}

또는

function Todo({title}) {
    return <div>{title}</div>;
}
export default Todo;
```

원래 해당하는 모듈 쓰려고 하면 {React}처럼 중괄호로 물어야
그러나 앞에서 ESM예제에서 export default function을 통해 React를 Default 모듈로 선언해 두어 중괄호를 쓰지 않고 import React가능

export가 없으면 그냥 함수에 해당(컴포넌트를 선언할 때, 함수로 선언하는 것이 있고, class를 통해 선언하는 것이 있음). 부모가 필요없거나 단순히 화면 출력이 목적인 경우에는 함수로 선언

이런 함수를 다른 모듈에서 쓰도록 하기 위해서는 선언과 동시에 export를 통해 가능

title은 props로 다른 모듈에서 전달되는 것으로 div를 통해 반환되는 것

<주의!!!> 함수 정의할 때, 대소문자 구별하기!



* C:\react\cra-test\src\TodoList.js

```javascript
import React, {Component} from 'react';
//react라는 파일에 여러 모듈이 있는데 component라는 파일을 쓰겠다는 것
//React는 Default이므로 중괄호 필요없으나, component는 default가 아니라 중괄호 필요
import Todo from './Todo.js';

class TodoList extends Component {
    state = {
        //상태변수
        //해당 component 내에서 사용 또는 유지되는 값
        todos: [],
        //아직 배열은 선언되지 않음
    };
    doClick = () => {
        // 아래에서 지정한 {this.doClick}의 함수
            const {todos} = this.state;
            const position = todos.length + 1;
            const newTodo=<Todo key={position} titel={`할 일 ${position}`} />;
            this.setState({todos : [...todos, newTodo]});
        });
    };

    render() {
        const {todos} = this.state;
        // render이라는 함수 내에서만 사용하는 지역변수
        // 위에서 정의한 todos 함수를 가지고 와서 넣어주는 것
        // todos가 배열을 가지고 있는데, {todos}=this.state;는 위에서 todos의 배열을 가지고 오는 것
        // 만약 이름도 가지고 오려면 {todos, name}=this.state;를 통해 가지고 올 수 있음
        return (
            <div>
                <button onClick={this.doClick}>할일 추가</button>
                {todos}
//render 아래에서 this.state를 통해 가지고 온 {todos}를 가지고 온 것
//{} 안에 지정한 것은 내가 설정하는 이름으로 doClick으로 지정한 것 
            </div>
        );
    }
}

export default TodoList;
```

또는

```javascript
import React, {Component} from 'react';

class TodoList extends Component {
    state = {
        todos: [],
    };

    onClick = () => {
        import('./Todo.js').then(({Todo}) => {
            //Todo.js에서 가지고 와서 {Todo}라는 함수를 시행하라
            // 위에서 처럼 import Todo from './Todo.js' 대신 아래에서 함수로 시행
        const {todos} = this.state;
        const position=todos.length+1;
        const newTodo = <Todo key={position} title={`할일 ${position}`} />
        this.setState({todos:[...todos, newTodo]});
        })
    };


    render() {
        const {todos} = this.state;
        return (
            <div>
                <button onClick={this.onClick}>할일 추가</button>
                {todos}
            </div>
        );
    }

}
export default TodoList;
```

원래 html은 대소문자 가리지 않지만, JSX문법은 onClick과 같은 이벤트명에서 대소문자 구별



* App.js

  ```javascript
  import React from 'react';
  import TodoList from './TodoList';
  
  function App() {
    return (
      <div className="App">
        <TodoList />
      </div>
    );
  }
  
  export default App;
  ```



* react.js, react-dom.js, index.js, App.js, TodoList.js, Todo.js 등이 하나의 파일(main.chunck.js)로 묶여서 내려옴 → 첫 화면 로딩에 부하가 발생 ⇒ 불필요한 부하를 줄이기 위해 코드 분할이 필요
  크롬 개발자도구의 Network 탭에서 확인 가능



### 2. 로컬호스트 확인하기

```cmd
C:\react>npx create-react-app cra-test
C:\react\cra-test>npm start
```



![image-20200205100843928](C:\Users\HPE\Desktop\TIL\images\image-20200205100843928.png)



* ⇒ main.chunck.js 파일에 Todo.js 파일의 본문이 포함되지 않음→ "할일추가" 버튼을 클릭하면 2.chunck.js 파일(Todo.js 파일의 본문 내용을 포함)이 내려옴→ 2.chunck.js 파일은 최초 한번만 다운로드

  



## 객체와 배열의 사용성(p57)

### 단축속성명(shorthand property names)

> 속성의 이름만 가지고, 변수의 값과 연곂

```javascript
<script>
    const name = 'mike';
    const obj_new = {
        age: 21,
        name,// 객체의 속성값이 변수로 존재하면 간단하게 변수 이름만 기록
        getName() {return this.name;},
    };
    console.log(obj_new.getName());

</script>
```

![image-20200205105214399](C:\Users\HPE\Desktop\TIL\images\image-20200205105214399.png)



```javascript
<script>
{
    const obj_old = {
        age: 21, 
        name: 'mike',
        getName() { return this.name; },        
    }
    console.log(obj_old.getName()); // mike

    const name = 'mike';
    const obj_new = {
        age: 21,
        name,
        getName() {return this.name;},
    };
    console.log(obj_new.getName());
}

{
    function makePerson_old(age, name) {
        return {age: age, name: name};
    }
        console.log(makePerson_old(12, 'mike'));
            //속성이 같으면 객체에 전달하는 변수 이름으로 연결

    function makePerson_new(age, name) {
        return {age: age, name: name};
        }
        console.log(makePerson_old(12, 'mike'));
    }

{
    const name = 'John';
    const age = 21;
    console.log('name = ', name, ', age = ', age);
    console.log({name, age});
    //객체와 동일한 속성이 있으면 이름, 객체로 출력됨
}

</script>
```

* 괄호 잘 맞추는 것 주의하기!

![image-20200205110913383](C:\Users\HPE\Desktop\TIL\images\image-20200205110913383.png)



### 계산된 속성명(Computed property names)

> 객체의 속성명을 동적으로 결정

```javascript
<script>
{
    function makeObject_unused(key, value) {
        const obj = {};
        obj[key]=value;
        return obj;
    }

    function makeObject_used(key,value) {
        return { [key] : value};
    }

    console.log(makeObject_unused("name", "John"));
    console.log(makeObject_used("name", "Mike"));
}

</script>
```

![image-20200205112130326](C:\Users\HPE\Desktop\TIL\images\image-20200205112130326.png)



```javascript
<script>
{
    let i =0;
    let obj = {
        ["val" + i ++] : i,
        ["val" + i ++] : i,
        ["val" + i ++] : i,
    };
    console.log(obj.val0, obj.val1, obj.val2);
}
</script>
```

![image-20200205112452091](C:\Users\HPE\Desktop\TIL\images\image-20200205112452091.png)



```javascript
    let param = 'size';
    let config = {
        [param]: 12, 
        ["mobile" + param.charAt(0).toUpperCase() + param.slice(1)]: 4
    };

    console.log(config);
```

![image-20200205114315946](C:\Users\HPE\Desktop\TIL\images\image-20200205114315946.png)



### 전개연산자 (spread operator)

```javascript
{
    let arr1 = [1,2,3];
    let arr2 = [...arr1];
    let arr3 = arr1;

    console.log(arr1);
    console.log(arr2);
    console.log(arr3);
}
```

동일한 결과 나타남

![image-20200205114559574](C:\Users\HPE\Desktop\TIL\images\image-20200205114559574.png)



```javascript
{
    let arr1 = [1,2,3];
    let arr2 = [...arr1];
    let arr3 = arr1;

    arr1[0] = 10;
    console.log(arr1);
    console.log(arr2);
    console.log(arr3);
}
```

![image-20200205114633674](C:\Users\HPE\Desktop\TIL\images\image-20200205114633674.png)

`=[...arr1]`은 값이 복사되어 arr1의 값이 바뀌어도 그대로 유지

`=arr1`은주소 자체가 복사되어 값이 바뀌면 함께 바뀜 

(깊은복사/ 얕은복사)



```javascript
{
let obj1 = {age:23, name:"Mike"};
let obj2 = {...obj1}
let obj3 = obj1;

obj1["age"] =30;
console.log(obj1);
console.log(obj2);
console.log(obj3);
}
```

![image-20200205114923118](C:\Users\HPE\Desktop\TIL\images\image-20200205114923118.png)

배열복사뿐만 아니라 개체 복사에서도 동일하게 깊은/얕은복사 수행됨



* ```javascript
  {
      console.log([1, ...[2,3], 4]);
  }
  ```

  ![image-20200205131452953](C:\Users\HPE\Desktop\TIL\images\image-20200205131452953.png)

* ```javascript
  {
      console.log(new Date(...[2020, 0, 12]));
      console.log(new Date(2020, 0, 12));
  
      let today=[2020,0,12];
      console.log(new Date(today[0], today[1], today[2])); 
  }
  ```

  위의 3가지 모두 동일한 결과

  ![image-20200205131711078](C:\Users\HPE\Desktop\TIL\images\image-20200205131711078.png)





### 배열 비구조화

* 객체 비구조화에서는 속성이름이 중요

  ```javascript
  {
      const obj1 = {age:21, name:"Mike"};
      const obj2 = {age:22, name:"John"};
  
      const {age,name} = obj1;
      console.log(age);
      console.log(name);
  }
  ```

  ![image-20200205141400629](C:\Users\HPE\Desktop\TIL\images\image-20200205141400629.png)

  *     객체의 속성이름과 변수 이름이 동일해야 출력 가능
        {name,age}로 순서바꾸어 정의해도 {Mike,21}로 오류발생하지 않고 출력
        변수명을 임의로 가져갈 수 없다. 개체속성인 name과 age로 정의해야하지, 속성과 다른 a나 b로 정의하면 오류 발생

* ```javascript
  {
      const obj1 = {age:21, name:"Mike"};
      const obj2 = {age:22, name:"John"};
  
      const {name:b,age:a} = obj1;
      console.log(a);
      console.log(b);
  }
  ```

  a와 b로 쓰려면, 별도로 정의 필요



* 객체 비구조화에서 기본값설정

  ```javascript
  {
      const obj = {age: undefined, name: null, grade: 'A'};
      const {age=0, name='noname', grade='F'}=obj;
      console.log(age);
      console.log(name);
      console.log(grade);
  }
  ```

  ![image-20200205142407121](C:\Users\HPE\Desktop\TIL\images\image-20200205142407121.png)

* 기본값과 별칭을 동시에 사용

  ```javascript
  {
      const obj = {age:undefined, name:"Mike"};
      const {age: newAge =0, name} = obj;
      console.log(newAge); //0
      console.log(age);	 //error
  }
  ```

  ![image-20200205142633441](C:\Users\HPE\Desktop\TIL\images\image-20200205142633441.png)



* ```javascript
  {
  function getDefaultAge() {
      return 0;
  }
  
  const obj1 = {age:21, grade:'A'};
  const {age=getDefaultAge(), grade} = obj1;
  console.log(age);
  console.log(grade);
  }
  ```

  ![image-20200205142825510](C:\Users\HPE\Desktop\TIL\images\image-20200205142825510.png)

  * 21값을 집어 넣으면 21로 출력됨

* ```javascript
  {
  function getDefaultAge() {
      return 0;
  }
  
  const obj1 = {age:undefined, grade:'A'};
  const {age=getDefaultAge(), grade} = obj1;
  console.log(age);
  console.log(grade);
  }
  ```

  * ![image-20200205143031478](C:\Users\HPE\Desktop\TIL\images\image-20200205143031478.png)
  * 반면, undefined로 정의하면, getDefaultAge()로 기본값이 0으로 정의되어 0이 도출됨

* 제외하고 출력하기 : rest

  ```javascript
  {
  const obj = {age:21, name:"Mike", grade:"A"};
  const {age, ...rest} =obj;
  console.log(rest);
  }
  
  {
  const obj = {age:21, name:"Mike", grade:"A"};
  const {name, ...rest} =obj;
  console.log(rest);
  }
  ```

  * {}를 통해서 지역함수별로 설정하는 것 필요 > 별도로 설정하지 않으면 이미 선언되었다는 오류 발생

  * ![image-20200205143639535](C:\Users\HPE\Desktop\TIL\images\image-20200205143639535.png)

    `{age, ...rest}`는 age를 제외한 값들만 도출

    `{name, ...rest}`는 name을 제외한 값들만 출력



* for 함수 이용한 배열 비구조화

  ```javascript
  {
      const people = [
          { age: 21, name: "Mike" }, 
          { age: 22, name: "Johe" },
      ];
      
      for (i of people) {
          let age = i.age;
          let name = i.name;
          console.log(age, name);
      }
      
      for ({ age, name } of people) {
          console.log(age, name);
      }
  }
  ```

  ![image-20200205144511169](C:\Users\HPE\Desktop\TIL\images\image-20200205144511169.png)



* 중첩된 객체의 비구조화

  ```javascript
  const obj = {name: "Mike", mother: {name: "Sara"}};
  const { name, mother: {name: motherName} } = obj;
  
  console.log(name);
  console.log(motherName);
  console.log(mother);
  ```

  ![image-20200205145000933](C:\Users\HPE\Desktop\TIL\images\image-20200205145000933.png)

  * `mother: {name: motherName}`으로 별도로 정의 필요. 이는 앞에 마이크의 속성도 Name이고 사라의 속성도 name이기 때문에 중첩되어 별도로 설정이 필요하기 때문이다

    바로 `mother`로 쓰면 오류 발생

  * 반면, 속성의 이름이 중첩되지 않으면 별도로 정의할 필요없이 바로 받아오면 가능

    별도로 `mother: {name:motherName}`으로 설정하기 보다는, 처음부터 다른 이름을 주어 `mother:{motherName}`을 통해 바로 변수로 이용가능

    ```javascript
    const obj = {name: "Mike", mother: {motherName: "Sara"}};
    const { name, mother: {motherName} } = obj;
    
    console.log(name);
    console.log(motherName);
    console.log(mother);
    ```



* 비구조화에서 기본값은 변수 단위가 아니라 패턴 단위로 적용가능

  ```javascript
  {
      //  우측 배열이 비어 있기 때문에 객체 기본값을 이용
      const [ { prop: x1 } = { prop: 123 } ] = [ ];
      console.log(x1);        // 123
  }
  
  {
      //  우측 배열이 비어 있지 않기 때문에 객체 기본값을 이용하지 않음
      const [ { prop: x1 } = { prop: 123 } ] = [ {} ];
      console.log(x1);        // undefined
  }
  
  //  객체 비구조화에서 계산된 속성명을 사용
  //  객체 비구조화에서 계산된 속성명을 사용할 때는 반드시 별칭을 입력해야 함
  {
      const index = 1;
      // const { key1 } = { key1: 123 };
      const { [`key${index}`]: valueOfTheIndex } = { key1: 123 };
      console.log(valueOfTheIndex);   // 123
  }
  
  //  별칭을 이용해서 다른 객체와 배열의 속성값 할당
  {
      const obj = {};
      const arr = [];
      const res = { foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true };
      console.log(obj);   // { prop: 123 }
      console.log(arr);   // [ true ]
  }
  ```

  

### 매개변수 기본값 설정

```javascript
{
    function printLog(a=1) {
        console.log(a);			//숫자
        console.log({a});		//객체
    }

    printLog();		//1 매개변수의 기본값이 사용
					//{a:1}
    printLog(2);	//2
}					//{a:2}
```

![image-20200205153345524](C:\Users\HPE\Desktop\TIL\images\image-20200205153345524.png)

`a`라는 숫자를 받아들이면, a=1로 1이 나타남



* 매개변수 기본값으로 함수 호출

```javascript
function getDefault() {
        return 1;
    }
function printLog(a = getDefault()) {
        console.log({ a });
    }
    printLog();         // { a: 1 }
    printLog(2);        // { a: 2 }
```

* 매개변수 기본값을 이용해서 필수입력 여부를 표현

  ```javascript
   function required() {
          throw new Error('필수입력입니다.');
      }
  function printLog(a = required()) {
          console.log({ a });
      }
      printLog(2);        // { a: 2 }
      printLog();         // Uncaught Error: 필수입력입니다.
  ```

  

### 나머지 매개변수

```javascript
<script>
//  나머지 매개변수를 사용하는 코드
{
    function printLog(a, ...rest) {
        console.log({ a, rest });
    }
    printLog(1, 2);         //  {a: 1, rest: [2]}
    printLog(1, 2, 3);      //  {a: 1, rest: [2, 3]}
    printLog(1, 2, 3, 4);   //  {a: 1, rest: [2, 3, 4]}
}
//  arguments 키워드를 이용해서 구현하는 경우
{
    function printLog() {
        let a = Array.from(arguments).slice(0, 1);
        let rest = Array.from(arguments).slice(1);
        console.log({ a, rest });
    }
    printLog(1, 2);         //  {a: 1, rest: [2]}
    printLog(1, 2, 3);      //  {a: 1, rest: [2, 3]}
    printLog(1, 2, 3, 4);   //  {a: 1, rest: [2, 3, 4]}
}
</script>
```



### 명명된 매개변수

> 사용 여부에 따라서 가독성이 달라짐

```javascript
const numbers = [10,20,30,40];
const result1 = getValues(numbers, 5, 25);
const result2 = getValues({numbers, greaterThan:5, lessThan:25});
```

* `result1`에서는 getValues가 어떤 값을 나오는지 알 수 없지만,

  `result2`에서는 5보다 크고 25보다 작은 수가 나온다는 것을 알수 있도록 가독성 높음

  

* 사용하지 않는 매개변수를 명명된 매개변수를 이용해 생략가능

  ```javascript
  const numbers = [10, 20, 30, 40];
  const result1 = getValues(numbers, undefined, 25);
  const result2 = getValues({numbers, lessThan:25});
  ```

  원래는 `undefined`통해 해당하지 않는다고 설명해야 (최소값은 선택하지 않고, 25보다 작은 수)

  그러나 매개변수를 이용하면 undefined를 사용하지 않고 그냥 바로 25보다 작은 수 라고 설정가능



## 함수 종류

* 익명함수 : 함수의 이름이 별도로 정의되지 않음

```java
const add1 = function(a,b) {return a+b;}
console.log(add1(3,4)); // 답 : 7
```

* 화살표 함수

```javascript
const add2 = (a,b) => {return a+b;}
console.log(add2(3,4)); //답 : 7
```

* 화살표 함수에서 중괄호로 감싸지 않으면 화살표의 오른쪽 게산 결과를 반환

```javascript
const add3 = (a,b) => a+b;
console.log(add3(3,4)); //답 : 7
```

* 매개변수가 하나이면 매개변수를 감싸고 있는 소괄호도 생략이 가능

```javascript
const add4 = a => a+5;
console.log(add4(5)); // 답 : 10
```

* 객체를 반환하는 경우에는 소괄호로 감싸야한다.

  객체인 `{result: a+b}`를 반환하기 위해서는 ()로 감싸는것 필요

```javascript
const addAndReturnObject = (a,b) => ({result : a+b});
console.log(addAndReturnObject(10,20));  // 답 : {result: 30}
console.log(addAndReturnObject(10,20).result); // 답 : 30
```

* 화살표 함수의 코드가 여러 줄인 경우, 

  전체를 `중괄호{}`로 묶고 반환 값에는 `return`키워드를 사용

  ```javascript
  const add = (a,b) => {
      if(a<=0 || b<=0) {
          throw new Error("양수를 입력하세요");
      }
      return a+b;
  };
  
  console.log(add(10,20)); // 결과 : 30
  console.log(add(10,-20)); // 결과 : error
  ```

  ![image-20200205160910480](C:\Users\HPE\Desktop\TIL\images\image-20200205160910480.png)

* 화살표 함수에너 나머지 매개변수를 사용

  화살표 함수는 일반 함수와 달리 `this`와 `arguments`가 바인딩되지 않음

  ```javascript
  const obj ={
      value1: 1,
      value2: 2,
      increase: function() {
          console.log(this); //{value1: 1, value2: 2, increase: ƒ, add: ƒ}
          if(this.value1 !== undefined)
              //value1이 정의되지 않는 것이 아니라 값이 있다면
          this.value1 ++;
          // value1의 값을 1을 증가시키고
          else
              // undefined라면
          this.value1 = 0;
          //value1은 0이 된다.
      },
  
      add: () => {
          if (this.value2 !== undefinde)
          this.value2 ++;
          else
          this.value2 = 10;
      }
  };
  
  obj.increase();
  console.log(obj.value1); // 결과값: 2
  
  const increase= obj.increase;
  increase();
  console.log(obj.value1); // 결과값 : 2
  console.log(globalThis.value1); // 결과값 : 0 (value1    이 윈도우 객체로 올라감)
  ```

  