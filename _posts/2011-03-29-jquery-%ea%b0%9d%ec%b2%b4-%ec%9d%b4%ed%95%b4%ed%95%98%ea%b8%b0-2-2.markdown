---
layout: post
title: jQuery 객체 이해하기 (2)
date: '2011-03-29 23:46:51'
---

앞 글 말미에 언급된 jQuery객체의 constructor에 대한 의문을 풀어보도록 하자.

먼저, 다음 코드를 실행시켜보자.

{% highlight js %}
function MyConstructor() {}
var myobject = new MyConstructor();
myobject.constructor == MyConstructor;     // true
{% endhighlight %}

위 코드의 의미를 궁금해 하는 독자는 이제 없다고 믿지만,  그래도 그림으로서 표현을 하면 다음과 같다.

![]({{ site.url }}/assets/constructor.png)

이 상태에서 myobject의 constructor를 출력해 보면, myobject에는 constructor가 없으므로, 링크가 가리키는 MyConstructor.prototype으로 가서 그곳에 정의되어 있는 constructor를 출력하게 된다. MyConstructor.prototype.constructor가 MyConstructor이므로 myobject의 constructor 역시 MyConstructor가 되는 것이다. ( 앞글 "코드와 그림으로 이해하는 자바스크립트 객체와 프로토타입"에서 설명한 내용이지만, MyConstructor.prototype은 자동적으로 생성되며, 이 객체의 constructor는 MyConstructor로 정해진다. )

그렇다면, 다음의 코드는 어떠한가?

{% highlight js %}
function MyConstructor() {}
MyConstructor.prototype = {};
var myobject = new MyConstructor();

myobject.constructor == MyConstructor;  // false
{% endhighlight %}

MyConstructor의 prototype이 새로운 객체 {}를 가리키도록 바꾸었다. 그리고 나서 MyConstructor를 constructor로 하는 새로운 객체를 생성하면 이 객체의 constructor는 MyConstructor가 아님을 알 수 있다. 왜 그럴까? 이 의문을 해소하고 나면,  위 jQuery 객체의 constructor가 왜 jQuery가 아닌지 알 수 있다. 위 코드와 다를 바 없기 때문이다.

먼저 그림을 그려보자.

![]({{ site.url }}/assets/constructor1.png)

위 그림에서 보듯이 MyConstructor의 prototype이 자동적으로 생성된 object가 아닌 사용자가 정의한 빈 객체 {}를 가리키게 바꾸어 놓았다.

그리고 이 빈 객체 {}의 constructor는 당연히 Object이다. 이 상태에서, 새로운 객체 myobject의 constructor를 출력해 보면, myobject에는 constructor 프로퍼티가 없으므로, 링크가 가리키는 MyConstructor.prototype으로 가는데, 여기에도 constructor 프로퍼티는 없으므로, 다시 링크를 타서 Object.prototype으로 가게 된다. 그리고 여기에서 정의된 constructor가 바로 myobject의 constructor가 되는 것이다.

이를 jQuery에 적용해 보면, 다음 그림처럼 그릴 수 있다. ( 사실 위의 그림과 다를 바 없다.)

![]({{ site.url }}/assets/constructor2.png)

<div>jQuery에서도 위 예제와 같이 jQuery.fn = jQuery.prototype = { ... }; 코드를 통해  jQuery.prototype을 새롭게 정의된 객체를 가리키도록 해놓았기 때문에, 사용자가 만든 객체인 $("div")의 constructor는 결국 Object.prototype에 정의되어 있는 constructor가 되는 것이다.  ( 이후 버전업되면서 jQuery.prototype 에 "constructor : jQuery" 를 추가함으로서 $("div")의 constructor는 jQuery가 된다. )</div>
<div><span style="font-family: monospace"><span style="font-size: 17px;line-height: normal">
</span></span></div>
