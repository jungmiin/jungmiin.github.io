---
title: "브라우저 동작 원리 - 렌더링을 중점으로"
author: jungmiin
category: 프론트엔드
tags: [프론트엔드]
layout: post
toc: true
---

https://d2.naver.com/helloworld/59361

https://developer.chrome.com/blog/inside-browser-part3

# 들어가기 전에…

- 브라우저는 사용자가 선택한 **자원**을 서버에 요청하고, 브라우저에 표시하는 기능을 가지고 있다.
- 여기서 말하는 자원은 보통 HTML문서이지만, PDF, 이미지, 혹은 또다른 형태일 수도 있다.
- 브라우저는 HTML과 CSS **명세**에 따라 HTML 파일을 해석해서 표시한다.
- 이 명세는 웹 표준화 기구인 W3C(World Wide Web Consortium)에서 정한다.

# 브라우저의 구성 요소

- **사용자 인터페이스**
    
    주소 표시줄, 이전/다음 버튼, 북마크 등 페이지를 보여주는 창을 제외한 나머지 모든 부분
    
- **브라우저 엔진**
    
    사용자 인터페이스와 렌더링 엔진 사이의 동작을 제어
    
- **렌더링 엔진**
    
    요청한 콘텐츠를 표시, HTML을 요청한다면, HTML과 CSS를 파싱하여 화면에 표시
    
- **통신**
    
    HTTP 요청과 같은 네트워크 호출에 사용.
    
- **UI 백엔드**
    
    플랫폼에서 명시하지 않은 일반적인 인터페이스 장치(UI)를 그림. OS 사용자 인터페이스 체계를 사용.
    
- **자바스크립트 해석기**
    
    자바스크립트 코드를 해석하고 실행
    
- **자료 저장소**
    
    자료를 저장하는 계층, 쿠키, 세션 스토리지, 로컬 스토리지와 같이 자원을 하드 디스크에 저장
    

# 렌더링 엔진(렌더러 프로세스)

이제부터 크롬을 기준으로 설명

- 렌더링 엔진의 역할은 요청 받은 내용을 브라우저 화면에 표시하는 일이다.
- 렌더링 엔진은 HTML 및 XML 문서, 이미지, PDF 등 다양한 유형을 표시할 수 있다.
- HTML과 CSS, JavaScript를 사용자와 상호작용 할 수 있는 웹 페이지로 변환하는 역할

각각의 브라우저들이 사용하는 렌더링 엔진이 다른데, 사파리는 Webkit, 크롬은 Blink 엔진을 사용한다. 

렌더링 엔진의 동작 과정은 아래와 같다.

![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/00494787-9805-4a37-a900-b1b36717376f)

## (1) 파싱

파싱이란 “브라우저가 코드를 이해하고 사용할 수 있는 구조로 변환하는 것”을 말한다. 결과는 보통 문서 구조를 나타내는 노드 트리가 되며, 이를 파싱 트리, 혹은 문법 트리라 불린다.

이는 어휘 분석과 구문 분석으로 구분할 수 있는데, 어휘 분석이란 공백이나 줄 바꿈 같은 의미 없는 문자를 제거하고, 자료를 유효한 토큰으로 분해하는 과정을 의미하고 구문 분석이란 언어의 구문 규칙에 따라 문서 구조를 분석함으로써 파싱 트리를 생성하는 과정을 의미한다.

위의 과정을 반복하며 만들어진 파서 트리를 컴파일러를 통해 기계 코드 문서로 변환한다. 

### HTML 파싱

HTML 파싱은 HTML 마크업을 파싱 트리로 변환하는 과정이며, 여기서의 파싱 트리는 DOM 요소와 속성 노드의 트리를 의미한다. 즉 문자열(HTML)을 파싱해서 DOM으로 변환하기 시작한다.

**DOM(Document Object Model)**은 브라우저가 내부적으로 웹 페이지를 표현하는 방법일 뿐만 아니라 웹 개발자가 JavaScript를 통해 상호작용을 할 수 있는 데이터 구조이자 API이다. 

직접 HTML과 DOM을 통해 이해해보자. 만약 아래와 같은 HTML이 있다면

```html
<html>
  <body>
   <p>Hello World</p>
   <div><img src="example.png" /></div>
  </body>
</html>
```

이는 아래와 같은 DOM 트리로 변환된다.

![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/1f76c0ac-7774-4bf3-921c-2cd0f059857e)

- **HTML 파싱 알고리즘**
    
    일반적인 파싱 기술을 사용할 수 없기 때문에 HTML 파싱을 위한 별도의 파서를 생성하는데, 이 알고리즘은 토큰화와 트리 구축 두 단계로 되어있다.
    
    토큰화는 어휘 분석으로서 입력 값을 토큰으로 파싱하는 과정이며, HTML에서의 토큰은 시작 태그, 종료 태그, 속성 이름과 속성 값이다.
    

### CSS 파싱

CSS는 기본적인 파서 유형을 이용하여 파싱이 가능하다.

```css
p, div {
	margin-top: 3px;
}
.error {
	color: red;
}
```

이는 아래와 같은 CSSOM 트리로 변환된다.

![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/47086b38-b951-4745-a01d-c6f139ddc158)

### subresource 로딩

웹 사이트는 일반적으로 이미지, CSS, JavaScript와 같은 외부 리소스를 사용한다. 이러한 파일은 네트워크나 캐시에서 로딩해야 한다. DOM을 구축하기 위해 파싱하는 동안 이런 리소스를 만날 때마다 스레드가 하나하나 요청을 한다면 너무 느리기 때문에, 프리로드(Preload) 스캐너가 동시에 실행된다.

HTML 문서에 `<img>` 또는 `<link>` 와 같은 태그가 있으면 HTML 파서가 생성한 토큰을 확인하고 브라우저 프로세스의 네트워크 스레드로 요청을 보낸다. 

### `<script>`과 파싱이 동시에 진행되지 않는 이유

웹은 파싱과 실행이 동시에 수행될 수 있다. 그러나 파서가 `<script>` 태그를 만나면 스크립트가 실행되는 동안 문서의 파싱이 중단된다. 이 이유는 `JavaScript` 에는 DOM 구조 전체를 바꿀 수 있는 `document.write()` 메서드를 사용해 문서의 모양을 변경할 수 있기 때문이다.

그렇기 때문에 때문에 `JavaScript`의 실행이 끝날 때까지 HTML 파서는 기다리게 된다.

- **이러한 상황에서 개발자가 명시적으로 HTML을 조작해서 브라우저에 힌트를 주는 방법이 있다.**
    
    `JavaScript` 에서 `document.write()` 메서드를 사용하지 않는다면 `<script>` 태그에 `async` 속성이나 `defer` 속성을 추가할 수 있다. 이 속성이 있으면 브라우저가 `JavaScript` 코드를 비동기적으로 로딩하고 실행하면서 HTML 파싱을 막지 않는다. 
    
    또한 `<link rel="preload">` 라는 옵션을 줌으로써 내비게이션을 실행하기 위해 리소스가 반드시 필요하다는 것을 브라우저에게 알려서 리소스를 가능한 한 빨리 다운로드하게 만들 수 있다. 
    

반면 스타일 시트는 이론적으로 DOM 트리를 변경하지 않는다. 그러나 스크립트가 문서를 파싱하는동안 스타일 정보를 요청한다면 문제가 될 수 있기 때문에, 로드되지 않은 스타일 시트 가운데 문제가 될만한 속성이 있다면 스크립트를 중단한다.

- **script의 async defer**
    - **<head>의 내부**
        
        ```html
        <!DOCTYPE html>
        <html lang="en">
          <head>
            <title>Document</title>
            <script src="main.js"></script>
          </head>
          <body>
            ...
          </body>
        </html>
        ```
        
        ![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/bbc1ade1-af3b-4347-a751-3c58d589eb84)
        
        브라우저가 html 파일을 위에서부터 차례로 읽어나가다, `<script>`를 만나면 html 파싱을 멈추고 서버에서 자바스크립트 파일을 다운로드하기 시작한다. 자바스크립트 파일 다운로드가 끝나면 자바스크립트를 실행하고, 그 이후에 멈췄던 html 파싱을 시작한다. 
        
        이런 방식을 사용하면 사용자가 완전한 웹사이트를 보는 데까지 시간이 오래 걸린다.
        
    - **<body> 내부의 마지막 줄**
        
        ```html
        <!DOCTYPE html>
        <html lang="en">
          <head>
            <title>Document</title>
          </head>
          <body>
            ...
            <script src="main.js"></script>
          </body>
        </html>
        ```
        
        ![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/6ca4d53a-9f11-40e4-b7e6-a56efac54a49)
        
        이러한 방식을 사용하면 html 파싱이 완료된 다음 자바스크립트 파일 다운로드를 시작하기 때문에 html 컨텐츠를 빨리 볼 수 있지만, 컨텐츠가 자바스크립트에 의존하고 있다면 자바스크립트의 다운로드와 실행을 기다려야 한다.
        
    - **async**
        
        ```html
        <!DOCTYPE html>
        <html lang="en">
          <head>
            <title>Document</title>
            <script async src="main.js"></script>
          </head>
          <body>
            ...
          </body>
        </html>
        ```
        
        ![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/42e4bd4d-6598-4f57-b860-9a6a197b3196)

        
        위에서부터 html을 파싱하던 브라우저가 `<script>` 를 만나고 파싱과 자바스크립트 다운로드를 동시에 한다. 자바스크립트 다운로드가 완료되면 자바스크립트를 실행하고, 자바스크립트가 실행되는 동안은 html 파싱이 중지된다.
        
        이는 html 파싱과 자바스크립트 다운로드가 동시에 진행되기 때문에 시간이 절약된다. 그렇지만 html 파싱이 완료되기 전에 자바스크립트가 실행되므로 자바스크립트에 아직 생성되지 않은 DOM 요소를 참조하는 코드가 있다면 오류가 날 수 있다.
        
        ```html
        <!DOCTYPE html>
        <html lang="en">
          <head>
            <title>Document</title>
            <script async src="a.js"></script>
            <script async src="b.js"></script>
            <script async src="c.js"></script>
          </head>
          <body>
            ...
          </body>
        </html>
        ```
        
        ![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/19d5d1b5-4170-4ab6-b6a4-1234be439f7a)
        
        만약 위와 같이 복수의 `<script>` 가 있다면, 정의된 스크립트 순서와 상관 없이 다운로드가 완료된 순서대로 실행된다.
        
    - **defer**
        
        ```html
        <!DOCTYPE html>
        <html lang="en">
          <head>
            <title>Document</title>
            <script defer src="main.js"></script>
          </head>
          <body>
            ...
          </body>
        </html>
        ```
        
        ![image](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/e75464a7-e904-437a-8958-f2a22a4f7f26)
        
        파싱을 하다 `<script>` 를 만나면 파싱과 자바스크립트 다운로드를 동시에 실행하고, 파싱이 끝나면 자바스크립트 다운로드가 완료되는대로 자바스크립트를 실행한다. html 파싱을 진행하는 동안 자바스크립트 다운로드를 모두 해놓고 html 파싱이 끝나자마자 자바스크립트를 실행하므로 가장 많이 사용되는 선택지이다. 
        

## (2) 렌더 트리

DOM 트리가 구축되는 동안 브라우저는 렌더 트리를 구축하는데, 이는 표시해야 할 순서와 문서의 시각적인 구성 요소(=렌더러)로써 올바른 순서로 내용을 그려낼 수 있도록 하기 위한 목적이다. 

즉 Render Tree에는 스타일 정보가 설정되어 있으며, 실제 화면에 표현되는 노드들로만 구성된다.

렌더러는 자신과 자식 요소를 어떻게 배치하고 그려내야 하는지 알고 있는 객체이다.

### DOM Tree vs Render Tree

렌더러는 DOM 요소에 부합하지만 1:1로 대응하는 관계는 아닌데, 예를 들어 `head` 와 같은 비시각적인 DOM 요소는 렌더 트리에 추가되지 않는다. 또한 display 속성에 `none` 값이 할당된 요소는 트리에 나타나지 않는다. (visibility 속성에 `hidden` 값이 할당된 요소는 트리에 나타난다.)

또한 어떤 렌더러는 DOM 노드에 대응하지만, 트리의 동일한 위치에 있지 않다. float 처리된 요소 또는 position이 `absolute` 로 처리된 요소는 흐름에서 벗어나 렌더 트리의 다른 곳에 배치된 상태로 그려진다. 

### attachment

각 노드에 대한 스타일을 결정하는 과정을 “**attachment**” 라고 부른다. 모든 DOM 노드에는 `attach` 메서드가 있고, DOM 트리에 노드를 추가하면 새 노드의 `attach` 메서드를 호출한다.

### 스타일 계산

렌더 트리를 구축하려면 각 렌더러의 시각적 속성에 대한 계산이 필요한데, 이것은 각 요소의 스타일 속성을 계산함으로써 처리된다. 

## (3) Layout

렌더러가 생성되어 트리에 추가될 때 크기와 위치 정보는 없다. 각 노드들의 뷰포트 내에서의 정확한 위치와 크기를 계산하는 것을 Layout 또는 Reflow라고 부른다.

이 과정에서 `vh`, `vw`와 같이 상대적인 위치, 크기 속성이 실제 화면에서 그려지는 `pixel` 단위로 변환된다.

HTML은 흐름 기반의 배치 모델을 사용하는데, 이것은 보통 단일 경로를 통해 크기와 위치 정보를 계산할 수 있다는 것을 의미한다. 일반적으로 흐름 속에서 나중에 등장하는 요소는 앞서 등장한 요소의 위치와 크기에 영향을 미치지 않기 때문에 배치는 왼쪽에서 오른쪽으로, 또는 위에서 아래로 흐른다. (단 표는 예외)

배치는 반복되며, HTML 문서의 `<html>` 요소에 해당하는 최상위 렌더러에서 시작하며, 각 렌더러에 필요한 크기와 위치 정보를 계산한다.

모든 렌더러는 `layout` 또는 `reflow` 메서드를 갖고, 각 렌더러는 배치해야 할 자식의 `layout` 메서드를 불러온다.

- **Layout은 보통 다음과 같은 형태로 진행된다.**
    1. 부모 렌더러가 자신의 너비를 결정
    2. 부모가 자식을 검토
        1. 자식 렌더러를 배치 (자식의 x와 y를 결정)
        2. (부모와 자식이 더티하거나 전역 배치 상태이거나 또 다른 이유로) 필요하다면 자식 배치를 호출하여 자식의 높이를 계산
    3. 부모는 자식의 누적된 높이와 여백, 패딩을 사용하여 자신의 높이를 설정. 이 값은 부모 렌더러의 부모가 사용
    4. 더티 비트 플래그를 제거

### 더티 비트 체제

작은 변경 때문에 전체를 다시 배치하지 않기 위해 브라우저는 더티 비트 체제를 사용한다. 렌더러는 다시 배치할 필요가 있는 변경 요소 또는 추가된 것과 그 자식을 더티하다고 표시한다.

“dirty”와 “children are dirty” 두 가지 플래그가 있는데, 자식이 더티하다는 것은 본인은 괜찮지만 자식 가운데 적어도 하나를 다시 배치할 필요가 있다는 의미이다.

### Global Layout과 Incremental Layout

Layout은 렌더러 트리 전체에서 일어날 수 잇는데, 이것을 Global Layout이라 하고 다음과 같은 경우에 발생한다.

- 글꼴 크기 변경과 같이 모든 렌더러에 영향을 주는 전역 스타일 변경
- 화면 크기 변경에 의한 결과

Incremental Layout은 렌더러가 더티일 때 비동기적으로 일어난다. 예를 들면 네트워크로부터 추가 내용을 받아서 DOM 트리에 더해진 다음 새로운 렌더러가 렌더 트리에 붙을 때다.

### Asynchronous Layout과 Synchronous Layout

Incremental Layout는 비동기로 실행되는데. Webkit은 점증 배치를 실행하는 타이머가 있는데 그때마다 트리를 탐색하여 “더티” 렌더러를 Layout 시킨다. 

Global Layout은 보통 동기적으로 실행된다.

### 최적화

Layout이 “크기 변경” 또는 렌더러의 위치 변화 때문에 실행되는 경우 렌더러의 크기는 다시 계산하지 않고 캐시로부터 가져온다. 

어떤 경우는 하위 트리만 수정이 되고 최상위로부터 배치가 시작되지 않는 경우가 있는데, 이런 경우는 입력 필드에 텍스트를 입력하는 경우와 같이 변화 범위가 한정적이어서 주변에 영향을 미치지 않을 때 발생한다.

## (4) Paint

Paint 단계에서는 화면에 내용을 표시하기 위한 렌더 트리가 탐색되고, 렌더러의 `paint` 메서드가 호출된다. 

Paint는 Layout과 마찬가지로 전역 또는 점증적 방식으로 수행된다. Incremental Paint에서 일부 렌더러가 전체 트리에 영향을 주지 않는 방식으로 변경 되면, 이는 화면 위의 사각형을 무효화 하는데 OS는 이것을 “더티 영역”으로 보고 “paint” 이벤트를 발생시킨다. 

실제로 요소가 stacking contexts에 쌓이는 순서가 Paint에 영향을 미치는데, 블록 렌더러가 쌓이는 순서는 다음과 같다. 

1. 배경 색
2. 배경 이미지
3. 테두리
4. 자식
5. 아웃라인

### 사각형 저장소

WebKit은 리페인팅 전에 기존의 사각형을 비트맵으로 저장하고, 새로운 사각형과 비교, 차이가 있는 부분만 다시 그린다.

## Reflow, Repaint 최적화

### Reflow(Layout)

어떠한 액션이나 이벤트에 따라 html 요소의 크기나 위치등을 수정하면 그에 영향을 받는 자식 노드나 부모 노드들도 Layout 과정을 다시 수행하게 된다. 이렇게 되면 Render Tree 각 요소들의 크기와 위치를 다시 계산해야하며, 이 과정을 Reflow라고 한다. 

Reflow가 일어나는 대표적인 경우는 아래와 같다.

- 페이지 초기 렌더링 시
- 윈도우 리사이징 시
- 노드 추가 또는 제거
- 요소의 위치, 크기 변경
- 폰트 변경, 이미지 크기 변경

### Repaint(Paint)

Reflow가 수행된 후 반영된 Render Tree를 다시 화면에 그려주는 과정이 필요한데, 이때 Paint가 다시 수행되며 이를 Repaint라고 한다.

그렇지만 무조건 Reflow가 일어나야 Repaint가 일어나는 것은 아니고, `background-color` `visility` 와 같이 레이아웃에는 영향을 주지 않는 속성이 변경되었을 때는 Reflow를 수행할 필요가 업기 때문에 Repaint만 수행하게 된다.

### Reflow, Repaint 줄이기

- 사용하지 않는 노드들에는 `visibility: invisible` 보다는 `display: none` 사용
    
    `display: none` 은 Render Tree에 아예 제외되기 때문에 Reflow가 되지 않는다.
    
- Reflow, Repaint가 발생하는 속성 사용 피하기
    
    Reflow가 일어나면 Repaint는 필연적으로 일어나기 때문에 가능하다면 Repaint만 발생하는 속성을 사용하는 것이 좋다.
    
    **Reflow가 일어나는 대표적인 속성**
    
    | position | width | height |
    | left | top | right |
    | bottom | margin | padding |
    | border | border-width | clear |
    | display | float | font-family |
    | font-size | font-weight | line-height |
    | min-height | overflow | text-align |
    | vertical-align | white-space |  |
    
    **Repaint가 일어나는 대표적인 속성**
    
    | background | background-image | background-position |
    | background-repeat | background-size | border-radius |
    | border-style | box-shadow | color |
    | line-style | outline | outline-color |
    | outline-style | outline-width | text-decoration |
    | visibility |  |  |
    
    또한 Reflow, Repaint가 일어나지 않는 `transform`, `opacity`와 같은 속성도 있다.
    
    따라서 `left`, `right`, `width`, `height`보다 `transform`을, `visibility` / `display`보다 `opacity`를 사용하는 것이 성능 개선에 도움이 된다.
    

## React의 Virtual DOM

일반적으로 DOM에 접근하여 여러번의 속성 변화, 여러번의 스타일 변화를 수행하면 그에 따라 여러번의 Reflow, Repaint가 발생하게 된다.

하지만 Virtual DOM은 Reflow, Repaint를 한번에 묶어서 DOM에 전달하기 때문에 처리되는 Reflow, Repaint의 규모가 커질 수는 있지만 한번만 연산을 수행하게 된다.

이를 통해 여러번 Reflow, Repaint를 수행하며 연산이 반복적으로 일어나는 부분이 줄어들어 성능이 개선된다.