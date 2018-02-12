---
layout: post
title: Revise Github Issue Viewer to React
date: 2018-02-12 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: githubLogo.png # Add image post (optional)
tags: [Front-end, javascript, web, github_api, React, Redux, React-router] # add tag
---

## Github Issue Viewer refactoring to React

![Alt text](./../assets/img/githubIssueViewerMainView.png)

![Alt text](./../assets/img/React.png)

이번에는 Github Issue Viewer를 React로 revise 해 보았다.

react에 대한 소개, 기타 개념들은 이미 잘 소개되어있는 자료들이 많으므로 생략하고 같은 작업을 react로 했을 때 들었던 생각을 정리해보려고 한다.

일단 기존의 기능, 즉 pure js로 구현되어있던 기능을 react로 변경하면서 느낀 점은 "생산성이 좋다"이다.

이전에 backbone 버전으로 변경할 때는 약간 생산성이 좋다고 말하기는 어려웠다. 그 이유를 생각해보자면 backbone에대한 학습량도 좀 적었고, 이해도 처음에 약간 잘못하고 있긴 했다. 하지만 이것을 떠나서 생각해 보자면,  backbone 자체가 자유로운 구성이 가능한 프레임워크라 분명 여러 상황에 유연하게 대처할 수 있는 강점이 있겠지만 자유로움에 따른 책임 ( 후처리, 효율적인 데이터 교환 방식 고려)가 조금 따랐다.

그러나 react로 작업할 때는 확실히 생산성에 대한 느낌을 많이 받았다. 일단 react 자체가 요구하는 데이터 교환 방식에 맞춰야 하는 답답함은 있을 수 있겠다. 하지만 그것을 효율적인 데이터 교환 방식이 구현되어있다는 생각으로 그것에 잘 맞춰서 프로그래밍 한다면 오히려 나같은 초급 개발자에게는 좀 더 안정적인 서비스를 만들 수 있겠다는 생각을 했다.

### 접근 방법
최상단에 App 컴포넌트가 존재하고, 그 하위로 MainPage, DetailPage가 존재합니다.

MainPage 내부에는 Subnav, IssueList, Pagination 컴포넌트가 존재하며 각 기능에 필요한 데이터는 그냥 react 버전에서는 MainPage에서 관리하고, redux를 적용했을 때는 redux가 app의 전체 state를 관리합니다.

DetailPage는 stateless 컴포넌트이며, redux를 통해 issueList를 받고, router를 통해 issue number를 받아서 필요한 issue를 찾아 랜더링 하고, comments를 api call을 통해 받아서 랜더링 합니다.

 DetailPage url에 제일 처음 접근 했을 때 만약 issueList를 api call을 통해 받아온 상태가 아니라면 redirecting을 통해 MainPage로 넘어가게 합니다.

하나로 통일되어있던 css 파일을 각 컴포넌트로 분리하여 좀 더 스타일 파악을 용이하게 했습니다.

기존에 있던 기능에 대해서는 이전에 vanilla js버전에서 했던 방식과 거의 동일하게 접근했으므로 기능에 대한 설명은 하지 않겠습니다.

### 이 프로젝트를 하면서 배운점

![Alt text](./../assets/img/aha.jpg)

### 생산성이 높게 느껴졌는가??
1. 각 화면에서 컴포넌트 단위로 역할을 나누고, react에서 많이 사용되는 container pattern을 사용해서 state를 공유 및 변경하는 방식을 활용하니 확실히 각 부분에 대해 직관적으로 다가왔다.
2. 이전의 js, backbone에 경우에는 어떤 처리를 하려면 어떤 변수를 변경하고, 그에 맞춰 뒤치다꺼리를 좀 해줘야 했다.(view hide show, dom 처리 등등) 
	- 그러나 react는 state가 변하면 re-rendering하고, virtual-dom 덕분에 이전 dom 상태에서 바뀐 부분만 rendering 되기 때문에 
	- 진짜 내가 원하는 로직을 작성하고 데이터를 처리 해 주면 나머지 후처리는 신경 쓸 필요가 없었다.
3. template 관점에서도, pure js에서는 mustache, backbone에서는 underscore의 templage 엔진을 사용해서 처리했다. 
	- 분명 그냥 js로 동적으로 element를 만들면서 화면을 그리는 것 보다는 효율적이었다. 
	- 하지만 해당 엔진 문법에 맞춰야했다. 
	- 그러나 react에서는 JSX문법을 통해 바로바로 html 코드 작성하듯 element들을 작성하고, 동적인 처리가 필요한 부분은 중괄호를 통해 js code로 처리할 수 있어 더욱 직관적이었다.

### redux와 react-router를 적용
맨 처음에는 react router만 적용했었다. 그러나 라우터만 사용하려고 하니 생각보다 편하지는 않았다. route로 props 넘기는 방식도 직관적이지 못했다.

그러나 redux를 같이 적용하니 시너지 효과가 났다. state 관련 부분은 redux가 맡아서, router는 단지 routing만 하도록 처리하였더니 훨씬 직관적인 로직 작성이 가능했다.

기본적으로 react는 View를 지원하는 라이브러리이기 때문에 기타 부차적인 툴들을 사용해서 구조를 맞추려면 여러 툴을 고려해서 다 같이 사용하는 편이 좋겠다고 느꼈다. 이것들이 react에서 공식적으로 나온 것들은 아니라서 이런 성격을 가지는 것 같다.

### React가 사랑받은 이유??
- 단방향 데이터 교환 방식이 주는 단순함
- virtual-dom 덕분에 dom 처리에 대한 신경을 덜 써도 된다는 편리함
- 월등한 생산성
- 코드 가독성
이 4가지 이유로 react가 사랑받고 주목받는 것 같다.