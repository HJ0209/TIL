20.01.31

# HTML

## 요약

* `$("속성")`에서 `$`는 선택자

* `<div>`값`</div>` 에서 `div`는 속성

* 스타일 설정 : `.css`,`.text`, `.val`

* `.click` 등에서 점 뒤의 클릭, 더블클릭 등은 이벤트

* `$.ajax`는 다른 사람들과의 통신

* `UI plug-in`을 통해 외부에서 다른 사람들이 제작한 html을 가지고 오기

* `<div id="content" class="bgblue"> </div>`에서

  div는 tag로 element(요소) > 다른 속성이 없으면 그냥 바로 `<div>`로 닫으면 됨

  id와 classs는 attribute(속성)

  

* `<a href=""> </a>`나 `<img scr="이미지주소"></img>`의 경우에는 속성이 반드시 설정되어야 함

  `""`는 반드시  있어야 할 필요가 없으나, 이름이나 주소에 공백이 있는 경우에는 반드시 설정필요

* 프로퍼티(props) : 속성 (자식이 부모로부터 받은 값)
  자식을 호출하면서 value 등의 값을 지정

  상태(state) 



## CORS

> 웹 교차 자원 요청 기능 (Cross Origin Resource Sharing)

* 내 사이트에서 http://naver.com/main.jsp를 가져오려면, image 태그 등을 가져올 때, 반드시 네이버가 아닌 다른 jquery 사이트 등에서 가져올 수 있음

  즉, 여러 사이트에서 가져와서 짜집기해서 스크립트를 작성할 수 있다.

* CORS는 여러 스크립트를 가져와서 사용할 수 있지만, javascript를 가져와서 사용하는 것을 막고 있다.

  이를 `SOP(Same Origin Policy)_동일출처(기원)정책`이라고 함(보안때문에 다른 출처를 가져와서 사용할 수 없음)

* `https://naver.com/main.jsp`에서 `https`는 스킴, `naver.com`은 호스트, `/`에 생략되어 있지만 80 과 같이 포트번호가 들어있음

  스킴, 호스트, 포트번호 3가지가 동일하면 동일한 출처라고 한다.

* javascript에서 다른 사이트의 정보를 가져오는 것이 `.ajax`임.

  XHR(XMLHttp Request)는 ajax로 가져오는 파일을 의미

  이를 가져와서 사용하지 못하도록 막는 것

* 하지만, 서비스의 확장을 위해 다른 출처(origin)의 리소스를 가져와 사용할 필요가 있어졌다. 이러한 SOP정책을 완화시켜달라는 것이 `CORS`

  (리소스는 오리진이 달라도 우선 전달받을 수는 있다. 하지만 이를 사용하는 것을 브라우저에서 막는 것

  -> CORS는 오리진이 허락하면 브라우저에서 사용할 수 있도록 설정해주는 것으로, 오리진으로부터 리소스를 전달받을 때 `Access-Contorl-Allow-Origin`으로 사용할 수 있도록 응답하는 것)

* 따라서, 실습할 때는 꼭 크롬 확장프로그램에서 cors를 다운받아 C 표시를 눌러서 켜고 실행하자!



## Ajax

![image-20200131134205275](C:\Users\HPE\Desktop\TIL\images\image-20200131134205275.png)

* `status`는 현재 네트워크 가져왔는지 확인하는 것

  200 : 정상적으로 잘 네트워크되어 정보를 가지고 옴

  400과 500번대는 네트워크 오류

  404 : 지정한 주소의 사이트가 없음



### siteinfo.html 문서 만들기

```javascript
{
    "siteinfos" : [
        { "name" : "jQuery", "URL" : "https://jquery.com/" }, 
        { "name" : "w3school", "URL" : "https://www.w3schools.com/" }, 
        { "name" : "야휴", "URL" : "https://www.yahoo.com" }
    ]
}
```

### html, css 설정하기

```javascript
    <script src="jquery-ui-1.12.1/external/jquery/jquery.js"></script>
    <script src="jquery-ui-1.12.1/jquery-ui.js"></script>
    // js파일 불러오기
    <link rel="stylesheet" href="jquery-ui-1.12.1/jquery-ui.css">
    // 관련된 css파일 불러오기
```



### 탭 만들기

```javascript
<head>
$(function () {
            $('#tabs a').click(function() {
                let divid = $(this).attr('href');
                $('#tabs div').hide();
                $('#tabs div'+divid).show();
            });
또는
 			$('#tabs').tabs();
});
</head>

<body>
    <div id="tabs">
        // 탭 UI를 적용할 테그
        <ul>
            // 탭 제목
        <li><a href="#tab1">Naver</a></li>
        <li><a href="#tab2">Daum</a></li>
        </ul>

       // 탭 본문
        <div id="tab1">탭 내용 1</div>
        <div id="tab2">탭 내용 2</div>

    </div>
</body>
```



### 네트워크 불러오기

```javascript
       // 서버로부터 정보가져와서 출력
        $.ajax({
            url: "http://localhost:8080/siteinfo.html",
            type: "get",
            dataType: "json", 
            // url의 타입은 중요하지 않음! 내가 달라고 적어둔 dataType이 중요
            // 다른 것 달라고 하면 오류나서 다운되지 않음

        }).done(data => {
            //done는 정상적인 케이스에 대해서만 처리함
            //오류를 알아보기 위해서는 실패했을 때의 명령어인 fail 복사

            console.log(data);
        }).fail(function(jqXHR, textStatus, errorThrown){
            // 정상적으로 작동하면 위의 done을 실행하고, 실패하면 아래의 fail을 실행
            console.log(errorThrown)
            // 어떤 에러가 났는지 콘솔에서 보여줌
        });
```



### 내용 불러와서 탭 만들기

```javascript
    <script>
    $(function(){

        $.ajax({
            url: "http://localhost:8080/siteinfo.html",
            type: "get",
            dataType: "json", 
        }).done(data => {
            console.log(data);
            data.siteinfos.forEach(site => {
                console.log(site);
                let name = site.name;
                let url = site.URL;
                let id = $('li').length + 1;
                // #tab1 처럼 1부터 하나씩 커지는데, index는 0부터 시작하므로 1을 더해야 첫번째부터 커지는 탭숫자 나타남
                let li =`<li><a href "#tab${id}">${name}</a></li>`;
                let div = `<div id ="tab${id}">탭 내용 ${id}</div>`
                $('#tabs > ul').append(li);
                $('#tabs').append(div);
                console.log(name, url);
            });
            $('#tabs').tabs();
            // 이거 순서 바뀌면 노노해! 꼭 탭 내용 불러들어온 다음에 tab명령어 실행

        }).fail(function(jqXHR, textStatus, errorThrown){
            console.log(errorThrown)
        });
    });
    </script>
```

### 이벤트 만들기

```javascript
$('#tabs').on('click', 'a', function() {
        //탭을 클릭하면 a 실행하고, 함수 실행하라
        let href = $(this).attr('href');
        let url = $(this).attr('url');
        console.log(href)
        $(href).load(url);
            });
```



### 미리보기 만들기 완성

```javascript
<!DOCTYPE html>
<html>

<head>
    <script src="jquery-ui-1.12.1/external/jquery/jquery.js"></script>
    <script src="jquery-ui-1.12.1/jquery-ui.js"></script>
    <link rel="stylesheet" href="jquery-ui-1.12.1/jquery-ui.css">

    <script>
    $(function(){
        $.ajax({
            url: "http://localhost:8080/siteinfo.html",
            //type: "get",
            dataType: "json", 
        }).done(data => {
            console.log(data);
            data.siteinfos.forEach(site => {
                console.log(site);
                let name = site.name;
                let url = site.URL;

                let id = $('li').length + 1;
                let li =`<li><a href="#tab${id}" url="${url}">${name}</a></li>`;
                let div = `<div id ="tab${id}">탭 내용 ${id}</div>`;

                $('#tabs > ul').append(li);
                $('#tabs').append(div);
                console.log(name, url);
            });

            $('#tabs').tabs();

            $('#tabs').on('click', 'a', function() {
                //탭을 클릭해 a(앵커)로 지정되면 함수 실행하라 (a는 앵커로 마우스를 대었을 때 파랗게 변하는 부분)
                //onclick은 tab안에서 함수를 실행하라는 것
                let href = $(this).attr('href');
                let url = $(this).attr('url');
                console.log(href)
                $(href).load(url);
            });
        }).fail(function(jqXHR, textStatus, errorThrown){
            console.log(errorThrown)
        });
    });
    </script>
</head>

<body>
    <div id="tabs">
        <ul>
        </ul>
    </div>
</body>
</html>
```

