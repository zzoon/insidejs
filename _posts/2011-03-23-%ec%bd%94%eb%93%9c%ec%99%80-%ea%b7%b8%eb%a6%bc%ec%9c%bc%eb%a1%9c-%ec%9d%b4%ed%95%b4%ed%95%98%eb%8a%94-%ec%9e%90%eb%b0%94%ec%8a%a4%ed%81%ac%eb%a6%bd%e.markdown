---
layout: post
title: 코드와 그림으로 이해하는 자바스크립트 객체와 프로토타입
date: '2011-03-23 23:12:05'
---

![]({{ site.url }}/assets/prototype12.png)

아래 글에 이어서 프로토타입에 대해서 예제 코드와 그림을 통해 이해해 보도록 하자.

아래 ECMAScript 262 에서 설명한 것중에 반드시 알아야 하는 것은 다음과 같다.

<strong>"prototype object와 implicit prototype link는 다르다."</strong>

많은 문서들이 prototype object와 prototype link 에 대해 특별히 구분해서 설명하고 있지 않다. 심지어는 ECMAScript에서 조차도 둘을 구분해서 이해하기가 그렇게 쉽지 않다. 단지 prototype object와 [[Prototype]] 이라고 표현을 달리한다는 것을 바탕으로 구분해서 이해해야만 한다.  이는 자바스크립트 입문자(본인 포함)에게 엄청난 혼동을 안겨다 준다. 심지어는 Crockford 의 문서( http://javascript.crockford.com/survey.html )에서도 prototype에 대해 implicit prototype link 의 개념을 가지고 설명한다.( 사실 prototype에 대한 정의로서 맞는 설명이긴 하지만, )  대부분의 입문자들은 객체의 prototype object가 실제 link를 가리키는 것으로 오해하게 된다.

이 문서에서도 prototype object와 [[Prototype]] 으로 구분해서 표현하도록 하겠다. [[Prototype]]의 개념을 다시한번 상기하고 넘어가도록 하자.

<strong>[[Prototype]] : 생성자에 의해 만들어진 모든 객체는 생성자의 “prototype” 프로퍼티의 값을 참조하는 implicit reference (객체의 prototype 이라 불림)를 가진다. </strong>

다음 코드를 보자.

{% highlight js %}
function A() {}
{% endhighlight %}

﻿

이 한 줄의 코드는 A라는 이름의 function object를 생성하는 코드이다. 이를 그림으로 그려보면 다음과 같다.

![]({{ site.url }}/assets/prototype13.png)

위 그림에서 보듯이 A라는 function object가 만들어지면서 A.prototype이라는 prototye object가 만들어진다.  Function object가 생성될 때, prototype object는 자동적으로 만들어지며, empty object이다. 그리고 이 prototype object의 constructor는 function A가 된다.  여기서 function A의 [[Prototype]]이 A.prototype이 아닌, Function.prototype임을 주의해야 한다. A의 constructor가 Function이므로 A의 [[Prototype]]은 constructor.prototype인 Function.prototype을 가리키게 되는 것이다.

여기서 다음 코드를 추가한다.

{% highlight js %}
function A() {}
B = new A();
{% endhighlight %}

위와 같이 A를 constructor로 하여 B 객체를 생성한다면,  아래 그림과 같을 것이다.

![]({{ site.url }}/assets/prototype2.png)

B의 [[Prototype]]은 constructor인 A의 prototype을 가리키게 된다.

이그림을 통해 보면, A를 생성자로 해서 만들어진 새로운 객체들은 모두 A.prototype에 접근할 수 있다는 것을 알 수 있다.

이 특성으로 인해 자바스크립트에서 상속의 개념을 구현해 낼 수 있는 것이다.

주의할 점 또 하나는 function object인 A는 Function.prototype에 정의되어 있는 property(bind, call, apply등이 정의되어 있다)와 Object.property에 정의되어 있는 property(isPrototypeOf, toString, valueOf 등)에 접근할 수 있는 반면, new를 통해 생성된 B 객체(function object가 아니다.) 는 Object.prototype의 property에만 접근할 수 있다.
