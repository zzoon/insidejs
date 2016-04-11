---
layout: post
title: NPM을 통한 모듈 의존성 관리
date: '2012-01-03 23:35:19'
---

원문 링크 - http://howtonode.org/managing-module-dependencies

<h1>모듈 의존성 관리하기</h1>

node.js 메일링 리스트에서 모듈 의존성 관리에 대한 논쟁 후에, 나는 이곳에 몇가지 사항을 공유하는 것이 의미있을 것이라 생각했다.

<h2>NPM을 통한 모듈 의존성 기술하기</h2>
당신이 여러개의 NPM 모듈들에 의존적인 애플리케이션을 개발중이라면, 다음과 같은 방식으로 package.json 파일을 작성해서 의존성을 기술할 수 있다.

{% highlight js %}
"dependencies": {
  "express": "2.3.12",
  "jade": ">= 0.0.1",
  "redis":   "0.6.0"
}
{% endhighlight %}

이를 통해 당신이 프로젝트를 갱신할 때마다, 다음과 같은 npm 명령을 통해 의존성을 해결할 수 있다.

{% highlight shell %}
$ npm install
{% endhighlight %}

당신의 프로그램을 구동하기 위해,  특정 버전의 모듈이 필요로 하거나 버전 번호 앞에 >= 를 붙여 앱 실행을 위한 최소 버전을 요구할 수 있다.

<h2>개발 버전을 위한 의존성 관리</h2>
만약 실제 제품이 아닌 개발 버전에서만의 의존성 (예. 테스팅 프레임워크)을 제공하기 위해서는 <strong>devDependencies</strong> 속성을 사용하면 된다.

{% highlight js %}
"devDependencies": {
  "vows": ">= 0.4.x"
}
{% endhighlight %}

실제 제품을 릴리즈 할 때는 <strong>npm install --production</strong> 명령을 실행하면, 개발 버전의 의존성을 가진 모듈의 설치는 제외된다.

<h2>private NPM 모듈 관리</h2>
당신이 private 모듈 상태로 개발한다면, package.json 파일에 <strong>"private": true</strong>를 추가해서 당신이 작성한 모듈이잘못해서 NPM 레지스트리에 등록되는 것을 막을 수 있다.

<h2>git 저장소의 모듈 의존성 기술하기</h2>
마지막으로 여러분이 개발한 모듈을 private git 저장소에서 제공하면서, 이 모듈에 의존하는 어떤 프로젝트를 개발한다면 모듈 의존성을 다음과 같이 작성할 수 있다.

{% highlight js %}
"dependencies": {
    "secret-module": "git+ssh://git@github.com:username/secret-repo.git#v2.3"
}
{% endhighlight %}

URL의 마지막 부분의 v2.3은 어떤 태그가 사용될지 기술한다. 커밋 해시나 브랜치명을 기술할 수도 있다.
