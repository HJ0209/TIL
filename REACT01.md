20.02.04



# REACT

## 설치 및 실행 : `creat-react-app`

#1 작업 디렉터리를 생성#1 명령 프롬프트(cmd.exe) 실행
\#2 작업 디렉터리 생성 및 이동C:\Users\myanj>mkdir c:\reactC:\Users\myanj>cd C:\reactC:\react>
\#3 Visual Studio Code 실행
\#4 File > Open Folder … > C:\react 를 선택 후 Open
\#5 create-react-app 패키지 설치C:\react>npm install -g create-react-app
\#6 create-react-app으로 리액트 프로젝트 생성C:\react>create-react-app hello-react
\#7 디렉터리 이동 후 실행C:\react>cd hello-reactC:\react>npm start



## 외부 패키지 사용하지 않고 개발환경 구축 : 불편!

### 1.리액트 라이브러리 다운로드



 https://unpkg.com/react@16.12.0/umd/react.development.js

https://unpkg.com/react@16.12.0/umd/react.production.min.js

https://unpkg.com/react-dom@16.12.0/umd/react-dom.development.js

https://unpkg.com/react-dom@16.12.0/umd/react-dom.production.min.js

각 url 들어가서 오른쪽 마우스 클릭해 다른이름으로 `C:/react`에 저장



development : 개발환경 용도에서 사용하는 파일로, 에러메시지 등을 확인 가능

production : 실행(배포) 환경 파일로, 최적화되어 있음

react : 리액트 기반의 무언가를 할 때, 플랫폼 구분없이 공통으로 사용되는 파일로 기반이 됨



### 2. 실행

```cmd
C:\react > npx http-server
http://localhost:8080/hello-world/sample1.html
```





### 3. jquery

https://developers.google.com/speed/libraries#jquery

`jquery.com`에서 `Google CDN`다운 : 제일 위에 있는 url  복사해야

![image-20200204110358421](C:\Users\HPE\Desktop\TIL\images\image-20200204110358421.png)

```javascript
<html>
    <body>
        <h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.        </h2>
        <div id="react-root"></div>
        <button>좋아요</button>
        <script src="react.development.js"></script>
        <script src="react-dom.development.js"></script>
        <script src="sample1.js"></script>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
        <script>
            $(function() {
                let liked = false;
                // liked라는 변수가 거짓이 되면, 좋아요 취소를 보여주고 / true가 되면 좋아요를 보여줌
                // button을 클릭하면, liked 변수의 값은 토글(false>true, true>false)
                $('button').click(function(){
                    if($(this).text() == '좋아요'){
                    } else {
                        $(this).text('좋아요 취소');
                    }
                });
            });
        </script>
    </body>
</html>
```



### 4-1. 리액트를 사용하지 않고 구현

```html
<html>
    <body>
        <h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.</h2>
        <div id="react-root">
            <button>좋아요</button>
        </div>
        
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
        <script>
            $(function() {
                let liked = false;
                // liked라는 변수가 거짓이 되면, 좋아요 취소를 보여주고 / true가 되면 좋아요를 보여줌
                // button을 클릭하면, liked 변수의 값은 토글(false>true, true>false)
                $('button').click(function() {
                    liked =! liked;
                    if (liked) $(this).text('좋아요');
                    else $(this).text('좋아요 취소');
                });

                $('button').trigger('click');
            });
        </script>
    </body>
</html>
```

상태값인 ` <button>좋아요</button> `와 사용자 화면인 `let liked = false;`의 불일치가 발생 > 이를 보완하기 위해 ` $('button').trigger('click')`같은 코드 추가 필요

POINT : 화면과 상태가 다르게 갈 수 있다. 따라서 이를 관리하기 위한 것이 복잡하다.



### 4-2. 리액트를 사용해 구현

```html
<h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.</h2>
<div id="react-root"></div>
<script src="react.development.js"></script>
<script src="react-dom.development.js"></script>

<script>

            class LikeButton extends React.Component {
//likebutton을 만들어 react 실행
                constructor(props) {
//최초 호출되면 실행
                    super(props);
//내가 extend한 상위(부모) 객체를 초기화 > constructor은 반드시 부모를 초기화하고 시작
                    this.state = { liked: false };
// state는 자기자신이 가지고 있는 값인 liked를 생성할 때 default로 false로 가지고 있음
                }

                render() {
//사용자의 화면에 보여주는 값
                    const text = this.state.liked ? '좋아요 취소' : '좋아요'
                    return React.createElement(
//react는 태그를 만들어 주는것으로, 내가 만드려는 것은 button
                        'button',
                        { onClick: () => this.setState({liked: true}) },
// 클릭하면 이벤트 시행(liked를 true로 바꿈)
// 1회성에 불과 (취소에서 다시 좋아요 되지 않음)
                        text,
                    );
                }
            }
const domContainer = document.querySelector('#react-root');
ReactDOM.render(React.createElement(LikeButton), domContainer);
</script>
```

반면, 좋아요와 취소를 반복하려면 아래처럼 입력

```javascript
 render() {

const text = this.state.liked ? '좋아요 취소':'좋아요'
return React.createElement(
       'button',
{ onClick: () => this.setState({liked: !this.state.liked}) },
    // 버튼을 클릭하면 현재 상태와 반대로 바꿈
         text,
);
}
}
```



### 5 여러 개의 돔 요소를 렌더링

#### 5-1. jQuery를 이용한 구현

```html
<html>
    <body>
        <h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.        </h2>
        
    <div id="react-root">
        <button id="btn1">좋아요</button>
        <button id="btn2">좋아요</button>
        <button id="btn3">좋아요</button>
    </div>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>

    <script>
         $(function() {
let liked1 = false;
$('#btn1').click(function() {
       liked1 =! liked1;
       if (liked1) $(this).text('좋아요');
       else $(this).text('좋아요 취소');
                });

let liked2 = false;
       $('#btn2').click(function() {
       liked2 =! liked2;
       if (liked2) $(this).text('좋아요');
       else $(this).text('좋아요 취소');
                });

let liked3 = false;
      $('#btn3').click(function() {
      liked3 =! liked3;
      if (liked3) $(this).text('좋아요');
      else $(this).text('좋아요 취소');
                });

$('#btn1').trigger('click');
$('#btn2').trigger('click');
$('#btn3').trigger('click');
            });

    </script>
    </body>
</html>
```

* jQuery는 동일한 구조를 반복해서 서술해야 하는 문제 발생



#### 5-2. REACT를 이용해 구현

```javascript
<html>
    <body>
        <h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.</h2>
        <div id="react-root1"></div>
        <div id="react-root2"></div>
        <div id="react-root3"></div>
        <script src="react.development.js"></script>
        <script src="react-dom.development.js"></script>
       
        <script>
class LikeButton extends React.Component {
  constructor(props) {
  super(props);
  this.state = { liked: false };
  }
        
render() {

const text = this.state.liked ? '좋아요 취소':'좋아요'
return React.createElement(
       'button',
{ onClick: () => this.setState({liked: !this.state.liked}) },
         text,
);
}
}
  ReactDOM.render(React.createElement(LikeButton), document.querySelector('#react-root1'));
        ReactDOM.render(React.createElement(LikeButton), document.querySelector('#react-root2'));
        ReactDOM.render(React.createElement(LikeButton), document.querySelector('#react-root3'));

        </script>
    </script>
    </body>
</html>
```





## 바벨(babel)

> 자바스크립트 코드를 변환해주는 컴파일러
>
> 표준화 또는 버전이 낮은 경우에도 돌아가게 만들어주는 역할
>
> (지원하지 않는 문법 등을 사용가능)

* 일반적으로 javascript를 javascript 형태로 실행

  그러나 바벨은 소스가 javascript가 아니어도 다른 소스를 활용해 작성한 이후에 javascript로 실행가능

  * 높은 버전이나 typescript 등의 다른 형태의 문법 사용가능

  * 불필요한 요소 제거 (주석문, 공백, 빈 이름의 식별자)

  * 다른 형태를 표준화

  * 표준화하는 과정에서 이를 해석할 수 있도록 `프리셋`을 이용

    (모든 내용을 바벨로 변환하려면 바벨이 너무 무거워져 원본 소스를 해석하는 플러그인을 끼워넣음)

    (원본소스가 무엇인지에 따라 원본을 해석할 수 있도록 플러그인, 플러그인의 집합인 프리셋 삽입하는 것)

```javascript
<html>
    <body>
        <h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.</h2>
        <div id="react-root"></div>

        <script src="react.development.js"></script>
        <script src="react-dom.development.js"></script>
        
        <script>
            class LikeButton extends React.Component {
                constructor(props) {
                    super(props);
                    this.state = {liked: false};
                }
                render(){
                    const text = this.state.liked ? '좋아요 취소': '좋아요';
                    return React.createElement(
                        'button',
                        { onClick: () => this.setState({liked: !this.state.liked}) },
                        text,
                    );
                }
            }
        
ReactDOM.render(React.createElement(LikeButton), document.querySelector('#react-root'));
        </script>

    </body>
</html>
```

```html
 <h2> 안녕하세요. 이 프로젝트가 마음에 드시면 좋아요 버튼을 클릭해주세요.</h2>
 <div id="react-root"></div>

<script src="react.development.js"></script>
<script src="react-dom.development.js"></script>
        
        <script>
class LikeButton extends React.Component {
      constructor(props) {
      super(props);
      this.state = { liked: false };
          }
 render() {
      const text = this.state.liked ? '좋아요 취소' : '좋아요';
       return React.createElement(
             'button', 
             { onClick: () => this.setState({ liked: !this.state.liked }) },
              text,
                    );
                }
            }


 class Container extends React.Component {
        constructor(props) {
        super(props);
        this.state = { count : 0 }
                }
        render() {
         return React.createElement(
             'div',
              null, //별다른 액션 처리를 하지 않음
React.createElement(LikeButton), //배열(subelement 기술)
React.createElement(
         'div',
         {style: {marginTop: 20}},
                           React.createElement('span', null, '현재 카운트 : '),
React.createElement('span', null, this.state.count),
React.createElement(
      'button',
     { onClick:() => this.setState({ count: this.state.count +1})},
      '증가',
        ),
React.createElement(
      'button',
      {onClick:() => this.setState({count: this.state.count -1})},
      '감소',
     ),
          )
  	   )
   }
 }
            
ReactDOM.render(React.createElement(Container), document.querySelector('#react-root'));
        </script>

```



## EMS

**#1 작업 디렉터리 생성**

```cmd
C:\react\hello-world>cd c:\react

C:\react>mkdir webpack-test

C:\react>cd webpack-test

C:\react\webpack-test>npm init -y

C:\react\webpack-test>mkdir src
> src 아래에 Button.js 와 index.js 생성

C:\REACT\WEBPACK-TEST
|   index.html
|   package.json
|
\---src
        Button.js
        index.js
```



**#2 외부 패키지를 설치**

C:\react\webpack-test>npm install webpack webpack-cli react react-dom

**#3 코드 작성**

 C:\react\webpack-test\index.html

```javascript
<html>
    <body>
        <h2>좋아요 버튼을 클릭해 주세요</h2>
        <div id="react-root"></div>
        <!-- dist/main.js : 웹팩으로 자바스크립트 파일을 결합하면 생성 -->
        <script src="dist/main.js"></script>
    </body>
</html>
```

C:\react\webpack-test\src\index.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import Button from './Button.js';

//  함수형 컴포넌트
function Container() {
    return React.createElement(
        'div',
        null,
React.createElement('p', null, '버튼을 클릭하세요'), 
React.createElement(Button, { label: '좋아요' }),
React.createElement(Button, { label: '싫어요' }),
    );
}

ReactDOM.render(
React.createElement(Container), 
document.querySelector('#react-root')
);
```

C:\react\webpack-test\src\Button.js

```javascript
import React from 'react';

function Button(props) {
    return React.createElement('button', null, props.label);
}

export default Button;
```

**#4 웹팩을 이용해서 두개의 자바스크립 파일을 하나로 결합**

C:\react\webpack-test>npx webpack

![img](https://lh5.googleusercontent.com/-acuGBNY9DoBpMLX8ERR-Ke4Vt6wZSVkcGIFGqSSRVEqmxuT7am5y-4qLvPY6qL_JF9-0KizfWmAlbjAgkGxn3y9YxxjsgmSbgkaVMDkypkd18l6WswJqUO-pKkSKO0BWPT4CJLL)



**#5 브라우저를 통해서 확인**

http://localhost:8080/webpack-test/index.html
![img](https://lh3.googleusercontent.com/wOBwTmCByey4izp_esM-KdBvEvV5bayj4E2zgzmjU5RcaALpdDJYCNVTKL2oiMGtpEAFNYZuHpXmVP7xGp2qHe04QVhGVXUaUQojcFHBToTMyF8G3pTZGlR4UhT1PQ_aMmGQHl7L)



## Create-React-App

* 리액트로 웹 애플리케이션을 만들기 위한 환경 제공
* 바벨과 웹팩도 포함
* 테스트 용이
* HMR(Hot-Module-Replacement), ES6+(6이후) 문법, CSS 후처리 등을 제공



### 1. 개발환경 설정

```cmd
C:\react>cd c:\react
C:\react>npx create-react-app cra-test
C:\react>cd cra-test
```

### 2. 개발서버 실행

```cmd
C:\react\cra-test>npm start
```

`http://localhost:3000/` 자동으로 접속

![image-20200204164933374](C:\Users\HPE\Desktop\TIL\images\image-20200204164933374.png)

![img](https://lh6.googleusercontent.com/RsUGRDA0bwD7moQApMkV-uQmn3HeliHei2ESxVyF-Z3FqoiTNoti_P_mRPZ1Em3DyM1-DiaoOiE40ZnFLTE_srfU2L5ysse2J2bpoQoYK5JkKS09fzBFXvsXvDD-HxXG10zlzIl6)

![image-20200204165450019](C:\Users\HPE\Desktop\TIL\images\image-20200204165450019.png)

> 별도의 enact 등이 없는 JSX 문법

* 바로 `src`를 통해 css를 다운 받기 보다는 소스 아래 있는 파일을 통해 `import`통해 다운받기

  import 구문을 사용해야 자동으로 압축해주어 효율적

  

##  Build

```cmd
C:\react\cra-test> npm run build
```

![image-20200204170126574](C:\Users\HPE\Desktop\TIL\images\image-20200204170126574.png)

`build` 폴더가 생긴 것을 확인할 수 있음

자바스크립트 파일에서 import 키워드를 이용해서 가져온 CSS 파일 → build/static/css/main.{해시값}.chunk.css 파일에 모두 저장

자바스크립트 파일에서 import 키워드를 이용해서 가져온 폰트, 이미지 등의 리소스 파일 → build/static/media 폴더에 저장 (10k 이하의 작은 파일은 data url 형식으로 자바스크립트 파일에 저장)



## HASH(해쉬)

1) 임의 크기 입력 : 고정 크기 출력

2) 유일성 보장 : a와 b가 같지 않으면, H(a)와 H(b)도 같지 않음

-> 유일성을 통해 무결성 보장

3) 일방향성, 단방향성 : a에서 H(a)를 구할 수 있지만, H(a)에서 a를 유추할 수 없음

-> 인증정보저장, 처리 (다른 사람이 알 수 없음)

-> 개인정보보호법에서는 무결성, 단방향성으로 OTP를 제작하도록 규정되어 있음

* 양방향성

암호화 : P(평문) -> KEY -> E(암호문)

복호화 : E(암호문) -> KEY -> P(평문)



4)  빠른 연산

5) 충돌회피