---
layout: post
title: '[번역] How this Works (JavaScript에서 this 의미)'
date: '2011-03-24 00:00:20'
---

<div>

원본글 : http://bonsaiden.github.com/JavaScript-Garden/#function.this
<h1>How <span style="color: #0000ff;">this</span> Works</h1>
JavaScript는 다른 프로그래밍 언어에 사용되는 <span style="color: #0000ff;">this</span>와는 다른 개념을 가지고 있다. JavaScript에서는 크게 5가지 방식으로 this가 특정 값에 바인딩 될 수 있다.
<h3>The Global Scope</h3>
[sourcecode language="javascript"]
this;
[/sourcecode]

</div>
this를 global 영역에서 이용할 경우, 그것은 단순히 global 객체를 가리킨다.
<h3>Calling a Function</h3>
[sourcecode language="javascript"]
foo();
[/sourcecode]

함수를 호출하는 경우 this는 역시 global 객체를 가리킨다.
<h3>Calling a Method</h3>
[sourcecode language="javascript"]
test.foo();
[/sourcecode]

이번 예제처럼 특정 객체의 메서드를 호출하는 경우, this는 해당 객체(여기서는 test 객체)를 가리킨다.
<h3>Calling a Constructor</h3>
[sourcecode language="javascript"]
new foo();
[/sourcecode]

new 다음에 이뤄지는 함수 호출은 생성자의 역할을 수행한다.
이때 생성자 함수의 내부에서 this는 생성자에 의해 새로 생성되는 객체를 가리킨다.
<h3>Explicit Setting of this</h3>
[sourcecode language="javascript"]
function foo(a, b, c) {}

var bar = {};
foo.apply(bar, [1, 2, 3]); // array will expand to the below
foo.call(bar, 1, 2, 3); // results in a = 1, b = 2, c = 3
[/sourcecode]

Function.prototype 객체의 call이나 apply 메서드를 사용하는 경우, 호출된 함수(예제에서는 foo 함수)의 내부에서
사용되는 this는 call이나 apply 메서드의 첫번째 인자로 명시적으로 설정된다.

그 결과 위 예제는 메서드 호출하는 경우의 this가 적용되지 않고, foo() 함수의 내부에서 this는 bar 객체를 가리킨다.

이상이 5가지 this가 바인딩되는 방식이다.