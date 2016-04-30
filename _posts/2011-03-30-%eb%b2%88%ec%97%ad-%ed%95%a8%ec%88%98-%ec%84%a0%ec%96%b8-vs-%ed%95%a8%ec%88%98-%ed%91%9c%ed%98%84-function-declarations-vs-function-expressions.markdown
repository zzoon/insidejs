---
layout: post
title: '[번역] 함수 선언 vs 함수 표현 (Function Declarations vs. Function Expressions)'
date: '2011-03-30 19:17:51'
---

원본글 : <a href="http://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/">http://javascriptweblog.wordpress.com/2010/07/06/function-declarations-vs-function-expressions/</a>

<span style="font-size: 26px; font-weight: bold;">Function Declarations vs. Function Expressions</span>

짧은 퀴즈로 시작하자. 다음 코드의 결과가 어떻게 될까?

{% highlight js %}
//Question 1:
function foo(){
    function bar() {
        return 3;
    }
    return bar();
    function bar() {
        return 8;
    }
}
alert(foo());

//Question 2:
function foo(){
    var bar = function() {
        return 3;
    };
    return bar();
    var bar = function() {
        return 8;
    };
}
alert(foo());

//Question 3
alert(foo());
function foo(){
    var bar = function() {
        return 3;
    };
    return bar();
    var bar = function() {
        return 8;
    };
}

//Question 4:
function foo(){
    return bar();
    var bar = function() {
        return 3;
    };
    var bar = function() {
        return 8;
    };
}
alert(foo());
{% endhighlight %}

8, 3, 3, [Type Error] 이라고 답하지 않았다면, 이 글을 계속 읽기 바란다. (실제로는 어차피 읽겠지만.. ^^)
<h3>What is a Function Declarations? (함수 선언이란 무엇인가?)</h3>
<strong>함수 정의는 변수 할당 없이 이름 있는 함수를 정의한 것이다. </strong>함수 정의는 마치 standalone 구조물처럼 비함수 블록에는 포함될 수 없다. 함수 선언을 변수 선언의 형제 정도로 생각하는 것도 도움이 될 것이다. 변수 선언이 "var" 키워드로 시작하는 것과 같이, 함수 선언도 "function" 키워드로 시작해야 한다.

{% highlight js %}
function bar() {
    return 3;
}
{% endhighlight %}

ECMA5 (13.0)은 함수 선언에 대해서 다음과 같은 문법으로 정의하고 있다.
<blockquote><strong>function</strong> <em>Identifier</em> ( <em>FormalParameterList</em><sub>opt</sub> ) { <em>FunctionBody</em> }</blockquote>
함수명은 자신의 영역(Scope)과 자신의 부모의 영역(Scope)에서 접근 가능하다(visible).

{% highlight js %}
function bar() {
    return 3;
}

bar() //3
bar  //function
{% endhighlight %}

<h3>What is Function Expression? (함수 표현이란 무엇인가?)</h3>
함수 표현은  커다란 문법 표현의(전형적으로는 변수 할당) 일부로 함수를 정의한다. 함수 표현에 의해 정의된 함수는 이름을 가지거나 익명일 수 있다.
함수 표현은 "function"으로  시작하지 않는다. (그러므로 아래 자기 호출 함수 예제의 경우 괄호로 묶여 있는 것이다.)

{% highlight js %}
//anonymous function expression
var a = function() {
    return 3;
}

//named function expression
var a = function bar() {
    return 3;
}

//self invoking function expression
(function sayHello() {
    alert("hello!");
})();
{% endhighlight %}

ECMA5 (13.0)은 함수 표현에 대해 다음과 같은 문법으로 정의하고 있다.
<blockquote><strong>function</strong> <em>Identifier</em><sub>opt</sub> ( <em>FormalParameterList</em><sub>opt</sub> ) { <em>FunctionBody</em> }</blockquote>
("function"으로 시작되지 않아야 한다는 표현이 포함되지 않은 것이 불완전하다고 생각할 수도 있다. )

<span style="text-decoration: underline;">함수 선언과는 반대로, </span> 함수 표현에서의함수명은 그것의 영역 밖에서는 보이지 않는다.
<h3>So what's a Function Statement? (함수 문장이란?)</h3>
그것은 보통 함수 선언으로 여겨진다. 그러나 <a href="http://yura.thinkweb2.com/named-function-expressions/#function-statements" target="_blank">kangax</a>가 지적했던 것 처럼, 함수 문장은 '함수 선언 문법이 허용되는 어느 곳에서도 사용할 수 있게 끔' 함수 선언을 확장한 것이다. 그것은 아직 표준은 아니다.

<strong>About that quiz….care to explain?</strong>

<span style="color: #000000;">퀴즈1은 함수명이 Scope의 맨 윗부분으로 이동되는 함수 선언을 이용한 것이다.</span>
<h3>Wait, what's Hoisting?</h3>
<a href="http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting" target="_blank">Ben Cherry’s excellent article</a> 글을 인용하면: <strong>"함수 선언과 함수 변수들은 코드의 위치와 상관없이, 자바스크립트 인터프리터에 의해 항상 자신의 자바스크립트 Scope에서 맨 첫 라인 부분으로 움직인다"</strong>

함수 선언이 Scope 상위 부분으로 이동할 때, 전체 함수 바디도 같이 움직인다. 그래서 인터프리터가 Question1에 코드를 처리한 후, 그것은 다음과 같이 실행한다.

{% highlight js %}
//**Simulated processing sequence for Question 1**
function foo(){
    //define bar once
    function bar() {
        return 3;
    }
    //redefine it
    function bar() {
        return 8;
    }
    //return its invocation
    return bar(); //8
}
alert(foo());
{% endhighlight %}

<strong>Do Function Expressions get Hoisted too?</strong>

그것은 함수 표현에 따라 다르다. Quiz2에 첫번째 함수 표현을 보자.

{% highlight js %}
var bar = function() {
	return 3;
};
{% endhighlight %}

왼쪽 부부은 (var bar)은 변수 선언이다. 변수 선언은 Scope의 상위 부분으로 이동하지만, 할당 부분(= 오른쪽 함수 부분)은 이동하지 않는다. 그래서 bar가 Scope의 맨 윗 부분으로로 이동하면 인터프리터는 초기에 var bar = undefined로 설정한다. 그러나 = 오른쪽 부분의 함수 정의 자체는 Scope의 상위 영역으로 이동되지 않는다.

<span style="color: #000000;">(ECMA5 12.2에 따르면 초기화 부분을 가진 변수에 실제 값이 할당되는 순간은 변수 생성할 때가 아니라 var문을 실행할 때이다. )</span>

그래서 Question2에 코드는 다음과 같은 순서로 실행된다.

{% highlight js %}
//**Simulated processing sequence for Question 2**
function foo(){
	//a declaration for each function expression
    var bar = undefined;
    var bar = undefined;
    //first Function Expression is executed
    bar = function() {
        return 3;
    };
    // Function created by first Function Expression is invoked
    return bar();
	// second Function Expression unreachable
}
alert(foo()); //3
{% endhighlight %}

<strong>Ok I think that makes sense. By the way, you’re wrong about Question 3. I ran it in Firebug and got an error</strong>

Firefox에서 HTML 파일을 작성하고 실행해보라. 또는 IE8, 크롬, 사파리 콘솔에서 실행해봐라. 결국 Firebug 콘솔은 그것이 "global" scope레서 실행할 때, 함수 hoisting을 수행하지 않는다. (그것은 실제로는 global scope이 아니고 특별한 "Firebug" scope이다. Firebug에서 "this == window"를 실행해봐라)

Question 3은 Question1과 비슷한 로직에 기반한 것이다. 이 경우 foo 함수가 global Scope의 상위 부분으로 이동한 것이다.

<strong>Now Question 4 seems easy. No function hoisting here…</strong>

거의 맞다. 만약 아무것도 hosting 되지 않았다면, TypeError은 "bar not a function"이 아니라 "bar not defined" 일 것이다. 함수 hosting은 없지만, 변수 hosting이 이뤄진다. 그래서 bar는 Scope에 윗 부분에 선언되지만, 그것의 값은 undefined이다.

{% highlight js %}
//**Simulated processing sequence for Question 4**
function foo(){
	//a declaration for each function expression
	var bar = undefined;
	var bar = undefined;
    return bar(); //TypeError: "bar not defined"
	//neither Function Expression is reached
}
alert(foo());
{% endhighlight %}

<strong>What else should I watch out for?</strong>

함수 선언은 공식적으로 비 함수 블록 (가령 if문)에 쓰이는 것을 금지한다. 그러나 모든 브라우저는 그것을 다른 방식으로 지원한다.

예를 들어, 다음 코드는 Firefox 3.6에서는 에러를 발생한다. 그 이유는 '함수 선언'을 '함수 문장(Function Statement)'으로 여기기 때문에, x는 정의되지 않는다.  그러나 IE8, 크롬5, 사파리5의 경우 함수 x는 반환된다. (표준 함수 선언으로 취급되기 때문에)

<strong>I can see how using Function Declarations can cause confusion but are there any benefits?</strong>

이제 여러분은 함수 선언이 관대하다고 주장할 수 있다. -- 함수가 선언되기 전에 그 함수를 이용하려고 한다면 hoisting은 위 호출 순서를 고치고 함수는 에러없이 호출된다. 그러나 이런 종류의 관대함은 엄격한 코딩을 막지만, 장기적으로는 그것들을 막기보다는 더 활발한 사용을 장려할 것이다.  결국, 프로그래머들은 몇몇 이유로 특정한 순서로 코드문을 정렬하고 있다 .

<strong>And there are other reasons to favour Function Expressions?</strong>

당신은 어떻게 생각하냐?

a) 함수 선언은  자바 스타일의 메서드 선언을 흉내낸것 같지만, 자바 메서드는 매우 다른 놈이다. 자바스크립트 함수는 값들을 가진 살아있는(?) 객체다. 자바 메서드는 단지 메타데이터 저장소이다. 아래 코드의 두 부분 모두 함수를 정의하고 있지만, 단지 함수 표현만이 객체를 생성중임을 나타낸다.

{% highlight js %}
//Function Declaration
function add(a,b) {return a + b};
//Function Expression
var add = function(a,b) {return a + b};
{% endhighlight %}

b) 함수 표현은 굉장히 다재다능하다. 함수 선언은 단지 "문장"으로 따로 분리되어서만 존재할 수 있다. 그것이 할 수 있는건 그것의 현재 Scope을 부모로 하는 객체 변수를 생성하는 것 뿐이다. 반면에 '함수 표현'은 좀더 큰 구조물의 일부이다. 당신이 익명 함수나 함수의 prototype 할당이나 다른 객체의 프로퍼티로 사용하려면 '함수 표현'을 이용해야 한다. <a href="http://javascriptweblog.wordpress.com/2010/04/05/curry-cooking-up-tastier-functions/" target="_blank">curry</a> 나 <a href="http://javascriptweblog.wordpress.com/2010/04/14/compose-functions-as-building-blocks/" target="_blank">compose</a>와 같은 상위 애플리케이션을 사용하는 새 함수를 생성할때마다, 함수 표현을 사용한다. 함수 표현과 함수 단위 프로그래밍은 불가분 관계이다.

{% highlight js %}
//Function Expression
var sayHello = alert.curry("hello!");
{% endhighlight %}

<strong>Do Function Expressions have any drawbacks?</strong>

보통 함수 표현으로 생성되는 함수는 익명이다. 예를 들어 다음 함수는 익명이다. today는 단지 익명 함수의 레퍼런스이다.

{% highlight js %}
var today = function today() {return new Date()}
{% endhighlight %}

이것이 정말 문제가 되나? 대부분 아니지만, <a href="http://fitzgeraldnick.com/weblog/" target="_blank">Nick Fitzgerald</a>가 지적한 것 처럼 익명 함수의 디버깅은 좌절감을 줄 수 있다. 그는 이러한 문제를 해결하기 위해 Named Function Expressions(NFEs)를 제안했다.

그러나 Asen Bozhilov가 지적한 것 처럼 NFEs는 IE9 이전의 버전에서는 정확히 동작하지 않는다.

<strong>Conclusions?</strong>

잘못 위치한 함수 선언은 혼동을 준다. 그리고 변수에 함수 표현을 할당할 수 없는 몇몇 상황이 있다. 그러나 만약 여러분이 함수 선언을 이용해야만 한다면 그 함수가 속하는 Scope의 맨 윗부분에 함수를 위치시키는 것이 혼란을 방지할 수 있다. 난 결코 if문 안에 함수 선언을 하지 않을 것이다.

이제 여러분은 함수 선언을 이용할 상황을 잘 알고 있다. 그거면 된거다.
