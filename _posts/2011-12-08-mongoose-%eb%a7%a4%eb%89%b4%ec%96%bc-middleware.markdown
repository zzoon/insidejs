---
layout: post
title: Mongoose 온라인 매뉴얼 - middleware
date: '2011-12-08 00:15:25'
---

<p>이 문서는 http://mongoosejs.com의 메인 페이지에서 스터디에 필요한 일부 내용을 번역한 것입니다.</p>
<h1>미들웨어</h1>
<p>미들웨어는 스키마 레벨에서 정해지며, document 인스턴스 상에 init, save, remove 메서드들이 호출될 때 적용된다.</p>
<blockquote>[역자주: 미들웨어는 DB의 특정 동작이 처리되기 전에 중간 레벨에서 실행되는 로직이라고 생각하면 쉬울 것 같다. 가령, 데이터를 저장하기 전에 무결성을 검사한다거나 또는 삭제하기전에 관련된 리소스 사용 체크 같은 것을 처리하는데 사용되지 않나 싶다.]</blockquote>
<p>미들웨어는 직렬과 병렬 두가지 타입이 있다.</p>
<p>직렬 미들웨어는 다음과 같이 정의 된다:</p>

{% highlight js %}
schema.pre('save', function (next) {   // ... });
{% endhighlight %}

<p>이러한 형식의 미들웨어는 각각의 미들웨어가 next를 호출할 때 하나씩 순차적으로 실행된다.</p>
<p>병렬 미들웨어는 더 정교한 흐름 제어를 기능을 제공하며, 다음과 같이 정의된다.</p>

{% highlight js %}
schema.pre('remove', true, function (next, done) {   // ... });
{% endhighlight %}

<p>병렬 미들웨어는 즉시 next() 함수를 호출할 수 있다. 그러나 마지막 인자는 모든 병렬 미들웨어가 done()를 호출한 다음에 실행될 것이다.</p>
<h3>사용 예</h3>
<p>미들웨어는 다음과 같은 경우 유용하다.</p>
<ul>
<li>복잡한 검증</li>
<li>특정 Document 삭제시 이에 의존하는 Document들 제거 (예: 유저를 지울 경우, 그가 작성한 모든 블로그 기사 삭제)</li>
<li>Asynchronous defaults (역자주-무슨 기능인지 잘 모르겠음)</li>
<li>특정 동작이 발생시킨 비동기 작업. 예를 들면:
<ul>
<li>커스텀 이벤트 발생</li>
<li>통지 생성</li>
<li>이메일</li>
</ul>
</li>
</ul>
<p>이외에도 많은 것들이 있다. 특히 모델 로직을 원자화하고 비동기 코드의 중첩 블록을 회피하는 것에도 특히 유용하다.</p>
<p> </p>
<h3>에러 처리</h3>
<p>만약 어떤 미들웨어가 Error 인스턴스와 함께 next나 done을 호출한다면, 그 흐름은 중단되고 에러는 콜백으로 전달된다.</p>
<p>예를 들면:</p>

{% highlight js %}
schema.pre('save', function (next) {
  // something goes wrong
  next(new Error('something went wrong'));
});

// later...
myModel.save(function (err) {
  // err can come from a middleware
});

{% endhighlight %}

