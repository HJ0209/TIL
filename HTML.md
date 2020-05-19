# ctrl+O 통해 html 화면 켤 수 있음

ctrl 누르고 동시에 여러 줄 선택 가능



# HTML 태그

> https://www.w3.org/
>
> 오른쪽 클릭 > 페이지 소스보기



## 문서구조

```html
<!doctype html> : 이 웹페이지가 html로 만들어졌음
<html>
    <head>
        본문 아닌 것
    </head>
    
    <body>
       본문 설명
    </body>
</html>
```



## 제목 설정 : 탭에 나타나는 제목

```html
<title> WEB 제목 </title>
```



## 메타 설정

```html
<meta charset="utf-8">
```

* 한글로 작성한 파일이 깨지지 않도록 메타 설정



## 강조

```html
<strong> 강조</strong>
```



## 밑줄

```html
<u>밑줄</u>
```



## 제목 : h1~h6

```html
<h1>
    <h2>
        제목
    </h2>
</h1>
```



## 줄바꿈

```html
<br>
```

* `<br>`은 닫는 태그 없음 (단순히 시각적으로 줄만 바꾸어 줌)
* 장점 : 원하는 간격을 줄 수 있는 장점
* `<img>`, `<input>`, `<br>`, `<hr>`, `<meta>` 닫지 않는 태그의 사례



## 단락지정

```html
<p>
    하나의 단락 그룹핑
</p>
```

* `<br>` 보다는 `<p></p>`태그가 더 좋은 선택이다

* 단점 : 단락과 단락의 간격이 고정되어 시각적으로 자유도 떨어짐

  그러나 `CSS` 태그를 통해 `<p></p>`태그 한계 극복 가능



## 블록지정

* `<div>`

```html
<div>
    블록 줄바꿈
</div>
```



* `<span>`

```html
<span> 블록 줄 안바뀜</span>
```

```html
<span style="background-color:red">span1</span>
<span style="background-color:blue">span2</span>
```

![image-20200519161146907](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200519161146907.png)

​	주로 CSS 스타일과 함께 사용



## 이미지

```html
<img src="주소속성">
```

* [unsplash.com](http://unsplash.com/) : 저작권 무료 사이트

```html
<img src="주소속성" width="50%">
```

* 이런 방식으로 숫자, 비율 통해 크기 조정 가능



## 목차

* unorder 목차

```html
<ul>
    <li>
        목차
    </li>
</ul>
```



* order 목차 (숫자 앞에 나옴)

```html
<ol>
    <li>
        목차
    </li>
</ol>
```



## 하이퍼텍스트 (링크)

```html
<a href="https://www.w3.org/TR/html5/" target="_blank" title="html5 specification">Hypertext Markup Language (HTML)</a>
```

* anchor 의 `a`
* hypertext reference 의 `href`

* `target="_blank"`는 새창에서 페이지 열림
* `title`은 링크내용을 마우스를 글자에 올렸을 때 툴팁으로 보여줌





# CSS 태그



## 여백 : margin

```css
<p style ="margin-top:45px;">
```











