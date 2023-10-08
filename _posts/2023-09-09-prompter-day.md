---
title: "Prompter day - 회고"
author: jungmiin
category: 회고
tags: [해커톤, 회고]
layout: post
toc: true
---

마감 10일 전, 우연찮은 기회로 해커톤 홍보글을 보게되었다. 호기심에 들어가보니, SK텔레콤과 OpenAI가 함께 주최를 하는 생성형 AI 서비스를 이용한 제품을 만드는 해커톤이었다. 

평소에 ChatGPT를 자주 이용하기도 하고, 언젠가 꼭 OpenAI 서비스를 이용해서 개인 프로젝트를 만들고 싶다 생각했던 나는 꼭 이 곳에 참가하고 싶었고, 바로 팀을 찾기 시작했다.

열심히 팀을 찾아다닌 탓에 다행히도 좋은 팀원 들을 만날 수 있었고, 무사히 과제도 제출할 수 있었다. 그리고 첫 해커톤을 경험하며 내가 느꼈던 점들을 적어보고자 한다. 

# 디자이너와 프론트엔드 개발자

사실 나는 여태까지 프로젝트를 진행하면서, 단 한번도 남의 디자인으로 개발을 진행해본적이 없다. 이는 내가 실제로 디자인한 어플을 런칭 해본 경험이 있기도 했고, 나 역시 디자인 하는 걸 꽤 좋아하기도 했고, 무엇보다 여러 직군이 함께 모여 진행했던 프로젝트가 없었기 때문이다. 

개발자들끼리 모여서 프로젝트를 진행하다보면, 일단 디자인 툴을 다룰 줄 아는 사람이 디자인을 하게 되고, 그러면 자연스럽게 내가 디자인을 맡게 되는 상황이 많았다. 

사실 나는 이 경험 또한 내 장점이라고 여겼고, 그렇기 때문에 자소서나 이력서, 면접에서 늘 디자이너와 개발자로서의 업무를 모두 수행해본 경험이 있기 때문에, 더 원활한 커뮤니케이션이 가능하다는 어필을 했었다.

## 👤 남의 디자인으로 개발해본 적이 있나요? 그때 느꼈던 어려움은 무엇이었나요?

실제 어떤 회사의 면접에서 들었던 질문이다. 근데 나는 그 때까지 정말 남의 디자인으로 개발을 해본 경험이 없었다… 그래도 어떻게든 답변을 해보고자 타 서비스 클론 코딩을 했던 경험을 얘기했고, 겪었던 어려움도 정말 횡설수설 말도 안되는 소리를 늘어 놨었다. ~~결과는 탈락~~

이런 질문을 들었던 경험이 있기에, 더더욱 다른 직군, 특히 디자이너와의 협업 경험을 해보고 싶었고, 운 좋게 우리 팀에 정말 훌륭하신 디자이너분이 계셨다. 

디자이너분이 초안을 전달해주셨을 때 너무 두근거렸다. 처음으로 남의 디자인으로 개발해보는 것이기도 하고, 무엇보다 디자인이 너무 예뻤다!!! 꼭 이 디자인의 100%를… 정말 토씨 하나 안 틀리게… 완벽하게 개발 해야겠다는 의지가 불타올랐다.

### 결국엔 커뮤니케이션

내가 디자인을 하고 내가 개발한다는 것의 가장 큰 장점은 바로 **‘아주 편하고 즉각적인 소통이 가능하다는 것’**이다. 이 버튼이 이 위치에 있어야 하는 이유, 배치가 이렇게 되어야 하는 이유, 이 효과가 있어야 하는 이유, 이 모든 디자인의 의도를 나는 훤히 꿰뚫고 있다. 그렇기 때문에 나는 디자인을 구현하다가 좀 힘들 것 같은 각이 보이면 나 스스로에게 묻는다. 

‘이거 진짜 필요해?’ 

백이면 백 안 필요하다고 정신 승리를 한다. 그렇게 그 디자인 요소는 사라지고 개발 과정에서 휘뚜루마뚜루 다른 디자인으로 수정된다. 

그렇지만 내 디자인이 아니라면 다르다. 일단 최우선으로 그 디자인을 구현해내고자 노력한다. 이후 아무리 노력해도 이렇게는 (제한된 시간 내에) 못할 것 같다면… 이야기를 나누어야한다. 그 과정에서 이것이 안되는 이유, 그렇다면 가능한 대안은 무엇인지 정리해서 공유해야 한다. 이 과정에서 많은 시간이 소요된다.

![prompter1](https://github.com/jungmiin/moim/assets/58061756/1bd3d5d2-f1ca-43b2-9101-59789beb2134)

초반에 우리는 이런 류의 레이아웃을 개발하려고 하였다. 여기서 TEXT1과 TEXT2는 늘 가변적인 길이를 갖고있는 텍스트를 렌더링하는데, 디자이너 분은 TEXT1과 TEXT2를 가지고 있는 div 중 높이가 높은 곳에 맞추어 두 div가 동일하게 끝나도록 만들고 싶어하셨다. (`max-content` 처럼 작동하도록)

이것이 두 텍스트 모두 적당한 길이를 가지고 있을 때는 아주 예쁜 레이아웃이 되지만 텍스트의 길이가 조금만 달라지게 되면 하나의 div가 넓은 여백을 갖게 되는 경우가 많았고, 이를 어떻게 해결해야 할지에 대해 오랜 시간동안 고민했었다. 

프론트엔드로 해결할 수 있는 방법은 결국 두 div가 어떻게 끝나든 상관 없이 가로 축을 중심으로 가운데 정렬, 혹은 레이아웃을 아예 고정하고 스크롤 추가 등등의 방법이 존재하였는데 이는 역시 디자이너 분도, 나 역시도 썩 맘에 들지 않는… 찝찝한 해결 방안이었다. 

결국 이것을 해결한건 AI였는데, 아예 AI단에서 텍스트의 길이를 한정시킬 수 있었고, 우리는 위의 문제 사항과 관련 없이, 적절한 글자수 제한을 걸어줌으로써 레이아웃을 지킬 수 있었다. 

### 100% 구현은 불가능하다

디자인을 받자마자 신나서 밤을 꼴딱 새가며 작업을 했다. 동이 터올 쯤 모든 작업을 마치고 배포를 하였다. 신나게 톡을 남겼다. ‘프론트엔드 거의 다 만들어 놓았습니다! API 연결하면 될까요?’ 꿀잠 잤다.

일어나자마자 보게된 것은 방대한 양의 수정사항이었다.

그러고나서 다시 디자인 원본과 개발 완성본을 비교해보았다. ‘나 완전 날림 개발 했잖아…?’ 싶었다. 나름대로 피그마에 적힌 수치와 똑같이 개발하려고 했었는데, 실제 브라우저에서 렌더링하니 꽤 다르게 보이는 것이 많았다. 

수정 사항을 기반으로 차근차근 수정을 진행하였다. 디자이너 경험이 있었던 만큼, 척하면 척 알아 듣는 알잘딱깔센 개발자가 되고 싶었는데… 너무 부끄러웠다. 

그리고 만약 정말 노력해서 한 치의 오차도 없이 디자인과 같은 결과물을 만들어 냈다고 해도 다양한 문제가 존재한다는 것을 알게 되었다. 웹 서비스는 인터랙션이 존재하고 수많은 리사이징, 다양한 데이터에 대응하여 적절하게 렌더링 해야한다. 그렇기 때문에 정적인 디자인을 완벽하게 구현하는 것에서 멈추지 않고, 다양한 기기, 데이터를 테스트 해가면서 이 모든 경우를 충족시키는 디자인을 구현할 수 있도록 끊임없이 수정해야 한다. 이 과정 역시 디자이너 분과 끊임없는 커뮤니케이션을 하며 더 나은 화면으로 개선시켜 나가야했다.

### 그럼에도 내가 어필할 수 있는 강점은 무엇일까

누구나 서비스의 UI를 보면서, ‘안정적이다’, ‘아름답다’라고 느끼는 지점은 동일하다. 더 나아가서, 나는 이 서비스에서 UI가 더 아름답게 보이려면 레이아웃을 어떻게 바꾸고, 폰트를 무엇으로 변경하고, 패딩과 마진을 얼마를 주어야 할지 아는 것이 디자인 감각이라 생각한다. 

물론 나는 전문적인 디자이너분들에 비해 정말 한참 모자라는 실력을 가지고 있지만, 그럼에도 불구하고 아주 쥐똥만큼의 디자인 감각을 가지고 있다고 생각한다. 이러한 감각을 바탕으로 디자이너 분에게 다른 디자인의 대안, 혹은 조금 더 나은 디자인에 대한 이야기, 제안들을 건설적으로 할 수 있는 것이 나의 강점이라고 생각한다. 

# 타 개발자와 프론트엔드 개발자

이상적으로는 AI 서버 → 백엔드 서버 → 프론트엔드 서버 순으로 개발이 진행되는 것이 맞지만, 실제 현업에서는 물론이거니와 해커톤은 당연하게도, 모든 파트가 동시에 개발이 진행되어야 한다. 

개발 시작 당시 월요일까지 AI 완료, 화요일까지 프론트엔드 완료, 수요일까지 백엔드 완료! 이렇게 뭉뚱그린 일정만을 잡아놓고 시작한터라, API 스펙과 관련해 아무런 정리 없이 개발이 진행되고 있었다. 

처음엔 나 역시도 레이아웃 개발에 바빴던 터라 이것에 대해 썩 생각을 하고 있지 않았지만, 레이아웃 개발이 완료되고, 점점 시간이 지나갈 수록 초조해지기 시작했다. 

‘다른 서버 배포는 언제 되는 거지?’, ‘Response, Request 데이터는 다들 어떻게 정의하고 계시는거지?’, ‘이대로 데이터를 넘겨줘도 괜찮겠지?’, ‘이대로 데이터를 렌더링 해도 괜찮겠지?!?!’ 

그렇지만 재촉을 할 수도 없었다. 이 곳에서 아마 시간이 많은 사람은 당연히 백수인 나뿐이었을 테고, 다들 현업, 학업 생활을 병행해서 진행하고 계시기 때문이었다…

## 그렇다면 내가 할 수 있는 것은 무엇일까?

내 목표는 단 하나였다. 

**‘서버가 배포가 되자마자 url만 바꾸면 바로 연결될 수 있을 만큼 99.9% 완성된 코드를 만들어 놓자!’** 

그러기 위해 예전부터 알고 있던 키워드인 Mock API에 대해 학습하였고, 자주 사용되는 것으로 알려진 `msw` 를 이용하여 Mock API를 구성하였다. 또한 프론트엔드가 보내고 받는 Request, Response Dto를 노션으로 정리해 다른 개발자 분들과 공유하여 배포 전 데이터 양식을 맞추었다. 

그 결과 배포 후 10여분 만에 무리 없이 API 연결을 성공시킬 수 있었다!!

이 Mock API는 API 연결 이후에도 제 역할을 톡톡히 했는데, 바로 에러와 관련한 모달 기능을 구현할 때였다. 아무래도 생성형 AI를 이용한 API 요청이다 보니 한번 요청을 하는데 꽤 시간이 걸리게 되고, 계속 해당 API에 에러를 내는 요청을 함으로써 무리를 주는 것이 부담이었다.

이때 `msw` 를 이용해 API 환경과 똑같은 조건에서 에러를 반환하도록 시킴으로써, 훨씬 쾌적하고 빠른 속도로 에러 모달 개발을 진행할 수 있었다. 

# 라이브러리에게 종속되지 않기

개발을 하다보면 우리는 늘 두가지 선택지 중 하나를 골라야하는 상황이 생긴다. 

**라이브러리 써서 편하게(?!) 구현하기 vs 라이브러리 쓰지 않고 직접 구현하기**

나는 아직 취준생이고, 직접 구현하는 것이 내부 구조를 이해하는데 큰 도움이 될 것이라고 생각하여 보통은 거의 후자를 선택한다. 그렇지만 가끔 정말 노가다스러운 로직이거나, 빠른 시간 내에 결과물이 필요할 경우 라이브러리를 선택해서 개발하기도 한다.

이번에는 아무래도 해커톤이라는 촉박함때문에 라이브러리를 선택하였는데, 거기서 느꼈던 바가 정말 많았다… 이를 적기 전에 내가 경험했던 라이브러리의 좋은 예에 대해 말해보겠다.

## 라이브러리의 좋은 예(day.js)

[moim](https://github.com/jungmiin/moim)이라는 약속 잡기 공유 캘린더 서비스를 개발한 적이 있다. 이때 캘린더 컴포넌트를 구현해야했는데, 이 역시 캘린더 기능을 제공하는 라이브러리가 있지만, 날짜와 관련한 여러 변수들을 고려하여 설계를 직접 해보는 것이 좋은 경험이 될거라 생각해 직접 구현하기로 마음을 먹었다. 

그렇지만 캘린더 내부에서 다루는 날짜 데이터는 `Date` 로 다루기 보다는 `dayjs` 라이브러리를 이용해 다루기로 하였는데, 이는 날짜를 비교하거나, 더하거나 빼고, 지역과 관련한 시차 설정을 하는데 `Date` 보다 훨씬 편리하기 때문이었다. 

비록 이 라이브러리 안의 기능을 제대로 이해하고 활용하는 것에는 러닝 커브가 있었지만, 꽤 직관적인 인터페이스를 갖고 있었기 때문에 간단하게 공식 문서를 읽은 후 바로 큰 어려움없이 활용할 수 있었다. 

## 라이브러리의 나쁜 예(react-slick)

동화의 그림과 내용을 슬라이드 형식으로 넘기는 기능을 구현해야했다. 많은 시간이 존재하지 않았기 때문에 나는 슬라이드 기능을 제공하는 라이브러리 중 가장 유명한 `react-slick`을 이용하여 슬라이드를 구현하기로 하였다. 

여기서 발생한 문제는 커스터마이징이었다. 해당 서비스는 어린아이를 위한 서비스이기 때문에 UI를 상당히 신경써야 한다. 그러나 라이브러리를 사용하면서 주어진 디자인 요구사항을 완벽하게 구현하는 것이 생각보다 어려웠다. 

라이브러리 안에 숨겨진 dom과 css요소가 있기 때문에, 내가 원하는대로 적용이 되지 않는 경우가 많았고, 이를 해결하기 위해 직접 라이브러리의 dom과 css를 뜯어보며 구조를 이해하고, 기존의 css를 무효화 시키고 새로운 css를 뒤집어 씌우는 과정을 반복해야했다(이럴거면 내가 만들고 말지!)

추가로 디자인과 상관없이 이러한 기능을 넣으면 어떨까? 하고 실험적으로 개발을 진행해보는데, 해당 기능은 아예 react-slick과 상충되는 방식이어서, 슬라이드 기능을 정상적으로 사용하려면 아예 이러한 기능은 구현이 불가능한 것도 존재했다. 

위의 기능은 단순히 내가 실험적으로 혼자 시도해보고 말았던 것이기 때문에 조용히 넘어갔지만, 만약 이 기능을 실제로 구현해야 했다면 나는 정말 ‘아 이건 개발 못해요, 구조 상 안돼요’ 라고 말을 하는 상황이 왔을 수도 있었다… 이 지점에서 나는 ‘아… 지금 이거 너무 라이브러리에 너무 종속되어 있다…’ 라는 생각이 들었던 것 같다. 

결과적으로는 해당 기능은 찝찝하긴 했지만 그렇다고 현재 문제가 있는 것은 아니었기 때문에 수정 없이 react-slick으로 완성하여 배포하였다. 

확실히 라이브러리를 사용하면 비교적 문제의 해결책을 쉽게 찾을 수 있다는 장점이 있다. 그렇지만 불필요하게 프로젝트의 크기를 키울 수 있고 개발자가 원하는 방식으로 변경하는게 쉽지 않다는 어려움이 존재할 수 있다는걸 이번에 확실하게 깨닫게 되었다.