---
layout: post
title: Node.js를 이용한 웹 기반 메모장 구현하기  - Nodepad 프로젝트 소개
date: '2012-01-14 19:00:54'
---

<p>요즘 Node.js 공부를 어떻게 해야하는지에 대한 질문을 종종 받습니다. <br />사실 저도 배워가는 처지라 정확한 답변 보다는 다음과 같은 원론적인 얘기만을 늘어놓고 있습니다.</p>
<p>"Node.js를 설치하고, 우선 API 공부를 충실히 한 다음 관련 프로그램을 작성해보세요."</p>
<p>그런데 저같은 초보자에 입장에서는 API 공부까지는 스스로 할 수 있다 치더라도 무엇을 만들지에 대한 고민을 해결하기는 쉽지가 않습니다.</p>
<p>이에 대한 하나의 대안으로 Node.js를 활용한 웹 기반의 메모장 'nodepad'라는 프로젝트를 소개하려고 합니다.</p>
<p>제가 즐겨 찾는 자바스크립트 관련 블로그 중에 'DailyJS(<a href="http://dailyjs.com/">http://dailyjs.com/</a>)'라는 곳이 있습니다. 이곳은 각종 자바스크립트 관련 라이브러리나 프레임워크의 리뷰 관련 글, 그리고 node.js 글들이 매주 2~3회씩 포스팅 되는 아주 액티브한 블로그입니다.</p>
<p>이곳의 주인장 Allex Young이라는 친구가 Nodepad 라는 Node.js 기반의 튜터리얼 연재를 약 20회에 걸쳐서 진행했습니다. <br /><a href="http://dailyjs.com/2010/11/01/node-tutorial/">여기를 참조하세요. </a></p>

![]({{ site.url }}/assets/NewImage.png)

이 프로젝트에 대해 간단히 소개하면, Node.js의 웹서비스 프레임워크인 Express와 MongoDB를 핸들링할 수 있는 Mongoose 등과 같은 Node 모듈들을 활용해서 웹 기반의 메모장을 만드는 것입니다. 튜터리얼은 Nodepad의 기능을 하나씩 추가하는 방식으로 연재를 진행하고 있습니다. 그리고 해당 회차의 전체 소스들은 Github 사이트에서 공개하고 있습니다. 하지만 해당 회차의 모든 소스들에 대한 설명을 하지는 않기 때문에 튜터리얼과 소스를 같이 살펴봐야 합니다.

아마 Node.js 개발 방법에 대한 전체 사이클이 궁금하신 분들이라면, 차근차근 수행한다면 Node.js에 대한 활용 방법에 대해서도 많은 도움을 얻을 수 있을 것입니다.
그리고 제가 참여하고 있는 <a href="http://www.facebook.com/octoberskyjs">Octobersky.js 스터디</a>의 일환으로 Nodepad 프로젝트의 번역을 진행하고 있으니, <a href="http://blog.doortts.com/category/node.js%20%EB%94%B0%EB%9D%BC%EB%B0%B0%EC%9A%B0%EA%B8%B0">이곳</a>를 참고하셔도 괜찮습니다.
