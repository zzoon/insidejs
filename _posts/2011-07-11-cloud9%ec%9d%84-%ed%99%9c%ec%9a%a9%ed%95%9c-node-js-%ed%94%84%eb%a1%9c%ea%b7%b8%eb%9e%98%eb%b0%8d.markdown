---
layout: post
title: cloud9을 활용한 node.js 프로그래밍
date: '2011-07-11 22:14:06'
---

<p> </p>
<h1>Cloud9을 통한 Node.js 프로그래밍</h1>
<h3>선행지식</h3>
<p>- Node.js - npm - Node.js Package Manager - github</p>
<p> </p>
<p>최근들어 여러 분야에서 클라우드가 다양하게 활용되고 있다.</p>
<p>웹 하드를 활용해 언제 어디서든 클라우드에 접속해 원하는 파일에 접근할 수 있는 기초적인 클라우드 서비스를 시작으로  애플의 iCloud를 비롯해서 구글 독스(http://docs.google.com) 서비스나 구글의 클라우드 기반 랩탑인 크롬북 등이 발표를 앞두고 있는 상황이라 앞으로 각 벤더별 클라우드 싸움은 점점도 치열해질 예정이다. 이러한 클라우드 서비스는 일반적으로 사용자를 중심으로 발전하고 있는 상황이다.</p>
<h4>개발자를 위한 클라우드 서비스 - cloud9</h4>
![]({{ site.url }}/assets/screenshot_1.png)
<p> </p>
<p>이번에 소개할 Cloud9은 개발자들을 위한 클라우드 서비스라고 해도 될 것 같다. Cloud9은  웹 하드처럼 언제 어디서든 소스 코드를 편집하고 테스트 해볼 수 있으며 브라우저만 있으면 굳이 별도의 개발 환경 설치를 하지 않고서도 개발할 수 있게 끔 해준다. 또한 개발자들간 협업할 수 있는 환경을 제공해주고 있기 때문에 오픈 소스 개발에도 활용할 수 있을 것이다. Cloud9은 현재 JavaScript 뿐만 아니라 Python, PHP, Ruby 등의 개발을 지원한다. 특히 Cloud9은 최근 JavaScript를 서버 사이드 스크립트로 사용할 수 있게 끔 해주는 <strong>Node.js 개발을 지원해 주고 있는 유일한 에디터</strong>다. Cloud9은 Node.js 디버깅 기능까지 제공해주고 있기 때문에 Node.js를 공부하고 싶은 개발자라면 한번쯤 사용해 볼만한 서비스다. 특히 Node.js 패키지 관리 시스템인<strong> npm을 지원</strong>해주고 있기 때문에, Node.js 개발을 더욱 편하게 할 수 있다.</p>
<p>이번 글에서는 Cloud9 을 이용해서 Node.js의 웹 프레임워크인 Express의 기본 예제를 어떻게 구동시킬 수 있는 지를 알아보도록 하자.</p>
<h4>Cloud9을 이용해 Node.js 프로그래밍을 해보자.</h4>
<p>우선은 Cloud9 메인 페이지에 접속해서 계정을 만들자. 그리고 만든 계정으로 로그인 후 다음 그림처럼 왼쪽 메뉴에서 MY PROJECTS 옆의 + 버튼을 눌러서 '<strong>Create a new project'</strong>를 선택해 새로운 프로젝트을 생성해서 직접 프로그래밍을 할 수 있지만, 여기서는 Github 사이트와 연동을 통해 프로젝트를 import하기 위해  <strong>'Clone from url' </strong>을 선택하자. 이렇게 하는 이유는 Node.js의 express 모듈을 사용한 예제 프로그램을 개발하기 위해서다. Cloud9은 아직까지 완벽하게 npm을 지원해주고 있지 않기 때문에, npm install express 명령으로 express를 설치하더라도 'express &lt;project&gt;' 명령어을 통해서 express 기반의 프로젝트 템플릿을 생성할 수 없다.</p>
<p> </p>

![]({{ site.url }}/assets/screenshot_2.png)

<h4>잠깐, github 사이트에 예제 프로젝트를 만들어보자</h4>
<p>필자와 같이 github에 대해 익숙하지 않은 분들은  이 글에서 다루기는 내용이 많은 관계로 http://binggrec.tistory.com/116와 같은 github 내용을 다룬 글들을 인터넷에서 찾아서 살펴보자.</p>
필자는 github에 first_project라는 Repository를 생성했다.  그리고 로컬 컴퓨터에서 다음 그림과 같은 작업을 수행했다. 

1. first_project 디렉토리 생성 
2. npm을 통해 express 모듈 추가 (npm install -g express) 
3. express  커맨드를 통한, 템플릿 화일 생성

![]({{ site.url }}/assets/screenshot_3.png)
<p>이제 로컬 머신에 만들어진 프로젝트를 github repository에 저장해보자. 다음은 필자가 github에 만든 first_project repository 화면이다.</p>
<p>repository 주소는 아래 그림과 같이 git@github.com:iamhjoo/first_project.git 임을 알 수 있다.</p>
![]({{ site.url }}/assets/screenshot_4.png)

<h4>다시 Cloud9으로 돌아가자.</h4>
<p>자. 이제 Cloud9에서 github에 생성된 프로젝트를 import 해보자. 위에서 설명했듯 'Clone from url'을 통해 Cloud9의 새 프로젝트를 생성해보자. 이 버튼을 누르면 다음과 같은 팝업 창이 나타난다. 이 팝업창의 Source URL에 import 하려는 프로젝트의 repository 주소를 입력하고, 아래 CHECKOUT 버튼을 클릭하면 된다.</p>
![]({{ site.url }}/assets/screenshot_5.png)

<p>프로젝트를 성공적으로 불러왔다면 다음과 같이 Cloud9 IDE 상에서 first_project에 대한 화면을 볼 수 있을 것이다. first_project는 바로 실행가능한 express 템플릿 프로젝트이기 때문에, 바로 툴바의 run 버튼을 눌러 프로젝트를 실행해보자. 그러나 예상과는 다르게 콘솔창에 'Error: Cannot find module 'express' 에러가 발생하면서 프로젝트가 실행이 안된다. 이유는 Cloud9 상에에는 express 모듈이 없기 때문이다. (물론 로컬 머신에는 들어있다.)</p>
<p>따라서 express 모듈을 설치해야 한다.</p>
<h4>Cloud9에서 npm을 통해 Node.js 모듈을 설치해보자.</h4>
<p>다행히 Cloud9은 Node.js의 패키지 매니저인 npm 명령을 지원해주고 있다. 아래 그림과 같이 Cloud9 IDE 맨 밑줄의 텍스트 박스 부분에<strong> npm install express</strong>라고 입력해보자. 그러면 콘솔창에 express 설치에 대한 메시지가 출력되면서 프로젝트의 node_modules 디렉토리에 express 모듈이 설치될 것이다.</p>
![]({{ site.url }}/assets/screenshot_6.png)

<p>참고로 express 모듈은 자바스크립트 템플릿 엔진인 jade 모듈을 HTML 렌더링시 사용한다. 따라서 express 설치후에 npm install jade 명령으로 jade 모듈을 설치해야한다. 아래 그림은 express 모듈을 설치한 다음, jade 모듈을 이어서 설치가 성공한 것을 나타내고 있다.</p>
![]({{ site.url }}/assets/screenshot_7.png)

<p>자. 이제 express, jade 모듈이 설치됐으니 다시한번 run 버튼을 눌러보자. 이번에도 아래와 같은 에러 메시지가 출력되면서 제대로 실행되지 않을 것이다. 여기서 맨 윗줄의 Tip 정보를 읽어보면 두가지 사실을 확인할 수 있다.</p>
<p>- http://fisrt_project.iamhjoo.c9.i0 라는 주소를 통해 여기서 작성한 Node.js 애플리케이션에 접근할 수 있다. 당연히 이 주소는 프로젝트와 Cloud9의 계정에 따라 달라진다. - 서버 주소는 '0.0.0.0', 포트는 process.env.C9_PORT를 사용하라고 안내하고 있다.</p>
![]({{ site.url }}/assets/screenshot_8.png)

<p>express의 템플릿 프로젝트는 기본적으로 동작하는 서버가 3000번 포트를 사용하게 코드가 생성된다. 프로젝트의 app.js 코드의 맨 마지막 줄을 살펴보면 다음과 같은 코드를 확인할 수 있다.</p>

{% highlight js %}
app.listen(3000);
console.log("Express server listening on port %d in %s mode", app.address().port, app.settings.env);
{% endhighlight %}

<p>따라서, 포트로 사용된 3000 대신 안내대로 process.env.C9_PORT로 수정한 뒤, 다시 run 버튼을 눌러 코드를 실행해보자. 다음과 같은 화면이 나왔다면 실행에 성공한 것이다. express 템플릿 예제가 20206 포트에서 실행되고 있는 것을 알 수 있다. 물론 이 포트 넘버는 상황에 따라 바뀔 것이다.</p>
![]({{ site.url }}/assets/screenshot_9.png)

<h4>이제 작성한 예제의 동작을 확인해보자.</h4>
<p>위 tip에서 안내하고 있는대로 현재 실행중인 예제 프로그램은 http://first_project.iamhjoo.c9.io 주소에서 실행되고 있다. 따라서 브라우저를 실행하고 위 주소를 입력하면 지금까지 작업했던 express 템플릿 예제를 시작할 수 있다. 실행 화면은 단순한 웹 페이지다.</p>
![]({{ site.url }}/assets/screenshot_10.png)
<p>물론 시작은 단순하지만, 지금부터 여러분들이 원하는 Node.js 프로그래밍을 한다면 훨씬 더 멋진 프로그래밍을 할 수 있을 것이다.</p>
<h4>마지막으로</h4>
<p>Cloud9으로 Node.js 개발을 진행하면서 작업중에 네트웍이 원할하지 않을 경우 데이터 저장이 제대로 되지 않는 다거나  npm 명령이 제대로 실행하지 않는 등 아직 안정화에서는 많은 부분의 추가 작업이 필요할 것으로 보였다. 그러나 Cloud9은 분명 매력적인 온라인 기반의 툴임에 틀림없다.  벌써부터 Cloud9이 얼마만큼 발전할 수 있을지 너무 기대가 된다.</p>
<p> </p>
<p>이 글을 읽는 분들이라면 한번쯤 Cloud9을 사용해보기 바란다.</p>
