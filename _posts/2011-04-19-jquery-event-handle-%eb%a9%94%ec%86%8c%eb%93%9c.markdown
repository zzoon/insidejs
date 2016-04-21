---
layout: post
title: jQuery.event.handle 메소드
date: '2011-04-19 23:58:46'
---

이 글은 jquery-1.2.2.js 기반으로 작성되었다.

jQuery.event.handle 은 jQuery 사용시 실제 이벤트가 발생되었을 때 실행되는 함수이다.

이 블로그의 <a title="jQuery에서 이벤트 핸들러 등록 및 호출 과정" href="http://nodejs-kr.org/wordpress/archives/266">jQuery에서 이벤트 핸들러 등록 및 호출 과정</a>을 보면  jQuery.event.add 함수에서 다음 코드를 확인할 수 있다.

{% highlight js %}
val = jQuery.event.handle.apply(arguments.callee.elem, arguments);
{% endhighlight %}

를 통해 호출된다.  여기서 arguments.callee.elem 는 event가 발생한 해당 DOM 요소이다.

이 글에서 jQuery.event.handle 의 동작 과정을 살펴보자.

{% highlight js %}
var handlers = jQuery.data(this, "events") && jQuery.data(this, "events")[event.type],
    args = Array.prototype.slice.call( arguments, 1 );
    args.unshift( event );

    for ( var j in handlers ) {
        var handler = handlers[j];

       // Pass in a reference to the handler function itself
       // So that we can later remove it
       args[0].handler = handler;
       args[0].data = handler.data;

       // Filter the functions by class
       if ( !parts[1] || handler.type == parts[1] ) {
           var ret = handler.apply( this, args );

       if ( val !== false )
           val = ret;

       if ( ret === false ) {
           event.preventDefault();
           event.stopPropagation();
       }
   }
}
{% endhighlight %}

<strong>1 라인</strong> : handlers는 jQuery.data 메소드를 통해 jQuery cache의 events의 event.type(예를들면 "click") 프로퍼티를 가리킨다.

<strong>5 라인</strong> : handlers에 있는 핸들러를 하나씩 얻어온다.

<strong>10~11 라인 :</strong> handler와 handler.data(bind함수를 부를 때 세번째 인자로 data를 넘기면 handler.data에 들어간다.)를 event.handler와 event.data가 가리키게 한다. (args[0]은 event 이다.)

이는 IE의 경우 생기는 메모리 누수 현상을 방지하기 위함이다. 이 event.handler와 event.data는 jQuery.event.handle 함수의 말미에 클린업된다.

<strong>15 라인 :</strong> handler를 실행시킨다.

<strong>20~23라인 :</strong> return 값이 false이면 event.preventDefault()와 event.stopPropagation() 를 실행시킨다.

이는 실행된 이벤트 핸들러가 false를 리턴하는 경우 디폴트 함수를 실행시키는 것을 막는다.( 이를 이해하기 위해서는 자바스크립트의 이벤트 처리 방식을 알아야 한다. 차후에 포스팅하도록 하겠다.)
