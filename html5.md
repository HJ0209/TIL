20.01.30



# Ch13. 기본

## 속성선택자

```html
<a href="http://..."> 링크</a>
```

* a는 태그로, Element(요소)

  href="http://.."는 속성으로, Attribute

* ```html
          <input type="text">
          <input type="password">
          <input type="radio">
          <input type="checkbox">
          <input type="file">
  ```

  ![image-20200130092443943](C:\Users\HPE\Desktop\TIL\images\image-20200130092443943.png)

  ![image-20200130092512113](C:\Users\HPE\Desktop\TIL\images\image-20200130092512113.png)

  text / password(글자보이지 않음) / radio (배타적으로 선택) / checkbox(중복 선택 가능) / file

* 회원가입 창 만들기

  ```html
  성 : <input type="text" name="lastName">
  이름 : <input type="text" name="firstName"><br>
  패스워드 : <input type="password" name="pw"><br>
  결혼여부 : <input type="radio" name="isMarried" value="Y">네 
          <input type="radio" name="isMarried" value="N">아니오<br>
  좋아하는 색깔 :
          <input type="checkbox" name="color" value="red">빨강
          <input type="checkbox" name="color" value="red">파랑
          <input type="checkbox" name="color" value="red">노랑 <br>
  증명사진 : <input type="file" name="photo"><br>
  ```

  ![image-20200130093413478](C:\Users\HPE\Desktop\TIL\images\image-20200130093413478.png)

  ![image-20200130093629572](C:\Users\HPE\Desktop\TIL\images\image-20200130093629572.png)

  * `<br>` 하면 한 줄 아래로 내려감

    성과 이름/ 결혼여부는 `<br>` 하지 않아서 가로로 나란히 나타남

    하지만 다른 것들은 `<br>` 통해 줄 바뀜

  * 명령문 앞뒤로 한글로 쓴 것은 속성 선택자 없이 설명처럼 나타남

  * `radio`는 둘 중 하나 선택

    `checkbox`는 중복 선택 가능

  * 전송하면 `[http://localhost:8080/test.html?lastName=Park&firstName=Changryum&pw=1234&pw2=1234&ismarried=Y&color=red&photo=3.+GROOT_%EA%B8%B0%EC%88%A0%EC%9E%90%EB%A3%8C+%EC%9E%84%EC%B9%98%EC%8B%9C%EC%8A%A4%ED%85%9C.pdf#](http://localhost:8080/test.html?lastName=Park&firstName=Changryum&pw=1234&pw2=1234&ismarried=Y&color=red&photo=3.+GROOT_기술자료+임치시스템.pdf#)`

    이는 시킴인 `http`를 통해 내가 현재 작성하고 있는 호스트인 `localhost:8080`로 파일 경로인 `test.html`을 전달

    안에 들어가는 내용은 lastName&firstName&pw&isMarried&color&photo로 요청 전달

    -> GET방식 : 파일 위치인 주소 뒤에 `?`붙여 내용 전달

* `입력하세요` 입력하기

  ```html
      <script>
          $(function(){
              $("#lastName").val("성을 입력하세요.");
              $('input[name="firstName"]').val("이름을 입력하세요.");
              $('input[type="password"]').val("비밀번호를 입력하세요.");
  
          });
          </script>
  
   성 : <input type="text" name="lastName" id="lastName">
  ```

  ![image-20200130095059264](C:\Users\HPE\Desktop\TIL\images\image-20200130095059264.png)

  * 성 : id를 매 상자마다 선택하여 입력하는 방식 
  * 이름 : 주어진 상자 이름을 통해 입력하는 방식
  * 패스워드 : 타입을 통해 선택하는데, 패스워드는 전부 보이지 않음

* 다양한 속성 선택자

  ```html
  <head>
      <script src="/node_modules/jquery/dist/jquery.js"></script>
      <script>
          $(function() {
              //  성, 이름 입력창에 OOO을 입력하세요. 문장을 추가
              //  $("#lastName").val('성을 입력하세요.');
              $('input[name="lastName"]').val("성을 입력하세요.");
              $('input[name="firstName"]').val("이름을 입력하세요.");
              /*  
                  p426
                  https://api.jquery.com/category/selectors/attribute-selectors/
                  E[A=V]  속성값이 같은 문서 객체 선택
                  E[A!=V] 속성값이 다른 문서 객체 선택
                  E[A~=V] 속성값에 단어가 포함된 객체를 선택    
                  E[A^=V] 속성값이 글자로 시작하는 객체를 선택
                  E[A$=V] 속성값이 글자로 끝나느느 객체를 선택
                  E[A*=V] 속성값에 글자를 포함한 객체를 선택
              */
  
              //  파일 선택창을 제외하고 나머지 입력창에 대해서 (필수입력) 표시
              $('input[type!="file"]').prev().append("(필수입력)").css('color', 'red');
  
              //  name 속성에 pw이 포함된 것을 검색
              $('input[name~="pw"]').css('background', 'red');
  
              //  name 속성이 pw로 시작하는 입력창
              $('input[name^="pw"]').css('border', '3px dotted blue');
  
              //  name 속성이 Name으로 끝나는 입력창
              $('input[name$="Name"]').css('border', '3px solid black');
  
              //  type 속성에 o가 들어가 있는 입력창을 삭제
              $('input[type*="o"]').hide();
          });
      </script>
  </head>
  <body>
  <form action="#" method="get">
      <label>성</label>
      <input type="text" name="lastName" value=""><br>
      <label>이름</label>
      <input type="text" name="firstName"><br>
      <label>패스워드</label>
      <input type="password" name="pw"><br>
      <label>패스워드(확인)</label>
      <input type="password" name="pw2"><br>
      <label>결혼여부</label>
      <input type="radio" name="ismarried" value="Y"> 네
      <input type="radio" name="ismarried" value="N"> 아니오<br>
      <label>좋아하는 색깔</label>
      <input type="checkbox" name="color" value="red"> 빨강
      <input type="checkbox" name="color" value="blue"> 파랑
      <input type="checkbox" name="color" value="yellow"> 노랑<br>
      <label>사진</label>
      <input type="file" name="photo"><br>
      <input type="submit" value="전송">
  </form>
  </body>
  ```

  ![image-20200130103335048](C:\Users\HPE\Desktop\TIL\images\image-20200130103335048.png)



## 필터 선택자

* 스타일 지정

   https://developer.mozilla.org/ko/docs/Web/CSS/Attribute_selectors

```html
    <head>
        <style>
            // style 아래 있는 것은 css 구문
            div {
                border: 1px solid black;
            }
            div.num {
                background: grey;
            }
            div.red {
                color: red;
            }
        </style>
    </head>

    <body>
        //div를 통해 class 지정
        //div는 한 줄을 다 차지하도록 지정하는 block요소. 반면 span은 입력된 값만큼만 공간을 차지하도록 지정하는 비block요소
        <div class="num red">1</div>
        <div class="num blue">2</div>
        <div class="num black">3</div>

        <div class="char red">a</div>
        <div class="char blue">b</div>
        <div class="char blakc">c</div>
    </body>
```

![image-20200130103844845](C:\Users\HPE\Desktop\TIL\images\image-20200130103844845.png)

* div 스타일 설정

  ```html
      <style>
       div {
              border: 1px solid black;
              padding: 10px;
           // 글자 상하 여백 만들기
              margin: 10px 20px;
           // 줄 단위로 박스 여백 설정
              width: auto;
              height: auto;
              font-size: 30px;
              float: left;
           //float는 한 줄이 아니라 내용만큼만 쓰고 옆에 이어씀. left는 왼쪽정렬, right는 오른쪽정렬
          }
  
          </style>
  ```

  * margin : 10px 20px 30px 40px

  ​       top / right / botton / left

  ![image-20200130111143313](C:\Users\HPE\Desktop\TIL\images\image-20200130111143313.png)

* jQuery 이용하기 (.CSS)



## 위치필터 선택자

* 순서에 따라 색지정

  * 첫 번째가 0, 두번째가 1이라서 홀수가 두번째 네번째에 나타남

  ```javascript
  <html>
  <head>
      <script src="/node_modules/jquery/dist/jquery.js"></script>
      <script>
  $(function(){
      $('div:odd').css('background', 'red');
      $('div:even').css('background', 'blue');
      $('div:first').css('background', 'yellow');
      $('div:last').css('background','green');
      //index기준으로 0부터 시작함 
  
  });
      </script>
      <style>   
      div {
          width: 100px;
          height: 100px;
          border: 1px solid blue;
          float: left;
          margin: 20px;
      }     
      </style>
  </head>
  <body>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
  </body>
  </html>
  ```

  ![image-20200130142023260](C:\Users\HPE\Desktop\TIL\images\image-20200130142023260.png)

## jQuery 충돌 방지

* jQuery는 식별자를 `$`를 사용하는데, prototype에서도 식별자 $를 사용함. 이렇게 여러 플러그인을 함께 사용할 때 플러그인 간 충돌이 발생할 수 있음

* 충돌을 방지하기 위해서는 `$.noConflict()`를 사용

  -> 이를 사용한 이후에는 더이상 $를 jQuery의 식별자로 사용할 수 없음

  * 방법1

  ```javascript
  <script>
      $.noConflict();
  jQuery(document).ready(function(){
      
  });
  </script>
  ```

  * 방법2

  ```javascript
  $.noConflict();
  let J=jQuery;
  
  J(document).ready(function() {
      J('h1').removeClass('high-light');
  });
  ```

  

# Ch16. 이벤트

* 이벤트 핸들링

```javascript
<html>
    
    <head>
        <script src="/node_modules/jquery/dist/jquery.js"></script>
        <script>
$(function() {
$('div').css('cursor','pointer').click(function(){
   // 커서를 두면 손가락 모양의 포인트가 나타남
    //클릭이라는 이벤트를 감지하면 function이라는 함수를 실행
    let value = $(this).text();
    //클릭이라는 이벤트를 받은 div의 값을 콘솔에 출력
    let attr=$(this).attr('class');
    //클릭이라는 이벤트를 받은 div class의 속성을 함께 출력
    console.log(value, attr);

});
});

        </script>
    </head>

    <body>
        <div class="num red">1</div>
        <div class="num blue">2</div>
        <div class="num black">3</div>

        <div class="char red">a</div>
        <div class="char blue">b</div>
        <div class="char blakc">c</div>
    </body>

</html>
```

![image-20200130113424736](C:\Users\HPE\Desktop\TIL\images\image-20200130113424736.png)

1을 클릭하면, 값인 `1`과 속성인 `num red`가 함께 출력된다

* 선생님 실습

```html
<!DOCTYPE html>

<html>
    <script src="/node_modules/jquery/dist/jquery.js"></script>
    <script>
        $(function() {
            //$("div").css('background','blue');
            //  num : 배경색을 회색
            //  char : 배경색을 적당한 색
            //  red, yellow, green, blue -> 글자색
            $('div.num').css('background', 'gray');
            $('div.char').css('background', '#105090');
            
            /*
            $('div.red').css('color', 'red');
            $('div.blue').css('color', 'blue');
            $('div.green').css('color', 'green');
            $('div.yellow').css('color', 'yellow');
            */

            $('div.num').each(function(index, item) {
                let color = $(item).attr('class').substr(4);
                $(item).css('color', color);
            });

            //  이벤트 핸들링
            $('div#input div').css('cursor','pointer').click(function() {
                let value = $(this).text();
                let attr = $(this).attr('class');
                console.log(value, attr);
                $('#disp').append(value, attr);
            });
        });
    </script>
    <style>
        div {
            border: 1px solid black;
            padding: 10px;
            margin: 10px 20px;
            width: auto;
            height: auto;
            font-size: 30px;
            float: left;
        }
    </style>
</head>
<body>
    <div id="disp"></div>

    <div id="input">
        <div class="num red">1</div>
        <div class="num blue">2</div>
        <div class="num yellow">3</div>
        <div class="num green">4</div>

        <div class="char red">하나</div>
        <div class="char blue">둘</div>
        <div class="char yellow">셋</div>
        <div class="char green">넷</div>
    </div>
</body>
</html>
```

![image-20200130114315585](C:\Users\HPE\Desktop\TIL\images\image-20200130114315585.png)



* 속성따라 숨기기 보이기

  ```javascript
  <!DOCTYPE html>
  <html>
  <head>
      <script src="/node_modules/jquery/dist/jquery.js"></script>
      <script>
          $(function() {
              // div 박스를 클릭하면 동일한 value가 
              //  화면에 있으면 다른 class의 박스를 숨기(hide)
              //  화면에 없으면 다른 class의 박스를 보임(show)
              $('div').click(function() {
                  let cls = $(this).attr('class');
                  let val = $(this).attr('value');
  
                  //  현재 클릭된 div와 value가 같고 class가 다른 div를 선택
                  let div = $('(div[value="'+val+'"][class!="'+cls+'"]');
                  /*
                  if (div.is(':visible')) {
                      div.hide();
                  } else {
                      div.show();
                  }
                  */
                  div.toggle();
              });
          });
      </script>
      <style>
          div {
              border: 1px solid black;
              padding: 10px;
              margin: 10px 20px;
              width: auto;
              height: auto;
              font-size: 30px;
              float: left;
          }
      </style>
  </head>
  <body>
      <div class="no" value="1">1</div>
      <div class="no" value="2">2</div>
      <div class="no" value="3">3</div>
      <div class="no" value="4">4</div>
  
      <div class="ch" value="1">one</div>
      <div class="ch" value="2">two</div>
      <div class="ch" value="3">three</div>
      <div class="ch" value="4">four</div>
      
  </body>
  </html>
  ```

  

* 옵션을 클릭하면 구구단 나오게 만들기

  * 방법1)

  ```javascript
  <!DOCTYPE html>
  <html>
  <head>
      <script src="/node_modules/jquery/dist/jquery.js"></script>
      <script>
          $(function() {
              $('select').change(function() {
                  console.log($(this).val());
                  for (let i =1; i<=9; i ++) {
                      $('div').append(`${$(this).val()}*${i} = ${$(this).val()*i}<br>`);
                  }
              });
  
  
          });
      </script>
      <style>        
      </style>
  </head>
  <body>
      <select>
          <option value="">선택하세요.</option>
          <option value="1">1</option>
          <option value="2">2</option>
          <option value="3">3</option>
          <option value="4">4</option>
          <option value="5">5</option>
          <option value="6">6</option>
          <option value="7">7</option>
          <option value="8">8</option>
          <option value="9">9</option> 
      </select>
      <div></div>
  </body>
  </html>
  
  ```

  ![image-20200130133416074](C:\Users\HPE\Desktop\TIL\images\image-20200130133416074.png)

  	* 방법2) 더 간단하게 만들기

  ```javascript
          $(function() {
              $('select').change(function() {
                  let dan=$(this).val();
                  for (let i =1; i<=9; i ++) {
                  
                      $('div').append(`${dan}*${i} = ${dan*i}<br>`);
                  }
              });
  
          });
  ```

  

* 마우스 올라가면 색 바꾸기

  ```javascript
  <!DOCTYPE html>
  <html>
  <head>
      <script src="/node_modules/jquery/dist/jquery.js"></script>
      <script>
  $(function(){
      $('div:odd').css('background', 'red');
      $('div:even').css('background', 'blue');
      $('div:first').css('background', 'yellow');
      $('div:last').css('background','green');
  
      let orgColor;
      $('div').mouseover(function() {
          orgColor = $(this).css('background');
          //마우스 올라가면 바뀌었다가 다시 돌아와야 하기 때문에 원래색 저장 필요
          $(this).css('background', 'black');
          //마우스 올라가면 배경색 까맣게 변함
      }).mouseleave(function() {
          $(this).css('background', orgColor);
          //마우스 떠나면 원래 색으로 돌아옴
      });
  });
      </script>
      <style>   
      div {
          width: 100px;
          height: 100px;
          border: 1px solid blue;
          float: left;
          margin: 20px;
      }     
      </style>
  </head>
  <body>
      <div></div>
      <div></div>
      <div></div>
      <div></div>
  </body>
  </html>
  ```

  

# jQuery UI

>  [jQuery UI](https://jqueryui.com/)
>
> [toast](toast.js)

## jQuery UI 설치

```javascript
<head>
<script src="/node_modules/jquery/dist/jquery.js"></script>

<script src="/node_modules//jquery-ui-css/jquery-ui.js"></script>

<link rel="stylesheet" href="/node_modules/jquery-ui-css/jquery-ui.css">
</head>
```

* 설치되었는지 확인

  ```javascript
  <!DOCTYPE html>
  <html>
  <head>
  <script src="/node_modules/jquery/dist/jquery.js"></script>
  <script src="/node_modules//jquery-ui-css/jquery-ui.js"></script>
  <link rel="stylesheet" href="/node_modules/jquery-ui-css/jquery-ui.css">
  
  <script>
  $(function() {
      $("button").button();
  });
  </script>
  </head>
  <body>
  
      <button> 클릭</button>
      <a hret="#">클릭하세요.</a>
  
  </body>
  </html>
  ```

  ![image-20200130145255539](C:\Users\HPE\Desktop\TIL\images\image-20200130145255539.png)

* 실습

  ```javascript
  <!DOCTYPE html>
  <html>
  <head>
      <script src="/node_modules/jquery/dist/jquery.js"></script>
      <script>
          $(function() {
              $('div').mouseover(function() {
                  $(this).attr('class', 'mycolor1');
              }).mouseleave(function() {
                  $(this).attr('class', 'mycolor2');
              }); 
          });
      </script>
      <style>
          div { width: 100px; height: 100px; float: left; border: 1px solid red; }
          .mycolor1 { background: red; }
          .mycolor2 { background: blue; }
  // 이렇게 별도로 지정해두면, 나중에 일일히 다 바꾸지 않고 여기서만 고쳐도 설정 바꿀 수 있음
      </style>
  </head>
  <body>    
      <div>1111</div>
      <div>2222</div>
  </body>
  </html>
  ```

  ![image-20200130151834894](C:\Users\HPE\Desktop\TIL\images\image-20200130151834894.png)

  마우스 커서가 올라가면 빨간색으로, 커서가 나가면 파랑색으로 바뀜

## Tab

```javascript
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="/jquery-ui-1.12.1/jquery-ui.css">
    <link rel="stylesheet" href="/jquery-ui-1.12.1/jquery-ui.theme.css">
    <script src="/jquery-ui-1.12.1/external/jquery/jquery.js"></script>
    <script src="/jquery-ui-1.12.1/jquery-ui.js"></script>
    <script>
        $(function() {
            $("#tabs").tabs();
        });
    </script>
    <style>        
    </style>
</head>
<body>    
    <div id="tabs">
        <ul>
          <li><a href="#matzip">맛집예약</a></li>
          <li><a href="#hospital">병원예약</a></li>
          <li><a href="#car">공유자동차예약</a></li>
        </ul>
        <div id="matzip">
          <p>맛집 예약과 관련한 컨텐츠</p>
        </div>
        <div id="hospital">
          <p>병원 예약과 관련한 컨텐츠</p>
        </div>
        <div id="car">
          <p>공유자동차 예약과 관련한 컨텐츠</p>
        </div>
    </div>
</body>
</html>
```

## Toast

> https://github.com/kamranahmedse/jquery-toast-plugin/
>
> C:\javascript>npm install jquery-toast-plugin

```javascript
<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="/jquery-ui-1.12.1/jquery-ui.css">
    <link rel="stylesheet" href="/jquery-ui-1.12.1/jquery-ui.theme.css">
    <script src="/jquery-ui-1.12.1/external/jquery/jquery.js"></script>
    <script src="/jquery-ui-1.12.1/jquery-ui.js"></script>

    <script src="node_modules/jquery-toast-plugin/dist/jquery.toast.min.js"></script>
    <link rel="stylesheet" href="node_modules/jquery-toast-plugin/dist/jquery.toast.min.css">

    <script>
        $(function() {
            $('input[name="userid"]').focusout(function() {
                if ($(this).val() == "") {
                    // $("#dialog").dialog();
                    $.toast({
                        text: "아이디를 입력하세요.",
                        showHideTransition : 'fade', 
                        bgColor : '#E01A31'
                    });
                }
            });
        });
    </script>
    <style>        
    </style>
</head>
<body>    
    <label>아이디</label>
    <input type="text" name="userid" />

    <div id="dialog" title="알림">
        <p>아이디를 입력하지 않았습니다.</p>
    </div>    
</body>
</html>
```



# Ch22. Ajax

* 브라우저 앱에 외부에서 HTTP 요청으로 들어오면 페이지 화면이 HTML로 불러들어옴.

  이후 client가 행동을 하면 DB에서 연산을 통해 json으로 정보를 제공 



