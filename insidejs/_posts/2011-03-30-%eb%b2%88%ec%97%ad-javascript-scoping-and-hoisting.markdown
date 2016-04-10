---
layout: post
title: '[번역] JavaScript Scoping and Hoisting'
date: '2011-03-30 08:31:31'
---

원본글 : <a href="http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting">http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting</a>
<h1>JavaScript Scoping and Hoisting</h1>
다음 프로그램에서 실행 결과는 무엇일까?
<blockquote>
<pre>var foo = 1; 
function bar() { 
    if (!foo) { 
        var foo = 10; 
    } 
    alert(foo); 
} 
bar();</pre>
</blockquote>
만약 결과가 10이라는 것이 의외라고 생각된다면,  다음 예제도 여러분을 깜짝 놀라게 할 것이다.
<pre>var a = 1; 
function b() { 
    a = 10; 
    return; 
    function a() {} 
} 
b(); 
alert(a);</pre>
물론 여기서는 브라우저는 1을 출력할 것이다. 그렇다면 여기서는 무슨 일이 일어난 건인가? 이상하고 위험하고 혼란스러울지도 모르겠지만, 이것은 실제로는 JavaScript의 강력하고 표현적인 특성이다. 나는 이러한 특징에 대한 표준이 존재하는지는 잘 모르지만, "hoisting"이라는 단어를 사용하는 것이 맘에 든다. 이 기사는 이러한 메카니즘에 대해 몇가지 조명을 해볼것이다. 이를 위해 우선은 JavaScript의 Scoping의 개념을 이해하는 것이 필요하다.
<h3>Scoping in JavaScript</h3>
JavaScript 입문자에게 가장 혼동스러운 개념이 scoping이다. 실제로 그것은 초보자가 쉽게 이해할 수 있는 컨셉이 아니다. 나는 scoping을 제대로 이해하지 못한 경험있는 JavaScript 프로그래머를 여러번 만난적이 있다. JavaScript에서 scoping이 혼동스러운 이유는 그것의 외형이 C 계열의 언어처럼 보인다는 것이다. 다음 C 프로그램을 살펴보자.
<pre>#include &lt;stdio.h&gt; 
int main() { 
    int x = 1; 
    printf("%d, ", x); // 1 
    if (1) { 
        int x = 2; 
        printf("%d, ", x); // 2 
    } 
    printf("%d\n", x); // 1 
}</pre>
이 프로그램의 실행 결과는 1, 2, 1이다. 이것은 C나 C 계열의 언어는 블록 레벨의 Scope을 사용하기 때문이다.
프로그램 제어가 if문 같은 블록으로 들어가면 , Scope 외부에 영향을 미치치 않고 새 변수들을 Scope 안에 선언 할 수 있다. (예제의 x 변수를 보라) 이것은 JavaScript의 경우는 적용되지 않는다. Firebug에서 다음을 실행해보자.
<pre>var x = 1; 
console.log(x); // 1 
if (true) { 
    var x = 2; 
    console.log(x); // 2 
} 
console.log(x); // 2</pre>
이 경우 Firebug는 1, 2, 2를 출력할 것이다. 이것은 JavaScript가 함수 레벨의 Scope를 사용하기 때문이다.  이것은 근본적으로 C 계열의 언어와 다르다. 가령 if 문과 같은 블록은 JavaScirpt에서는 새로운 Scope을 생성하지 않는다. 오직 함수만이 새로운 Scope을 만들 수 있다.

C, C++, C# 또는 Java 같은 언어에 익숙한 대다수의 프로그래머들에게 이러한 사실은 예상하지 못하거나 달갑지 않다 . 만약 여러분이 함수 안에 임시 Scope를 만들어야 한다면, 다음과 같이 해야 한다.
<pre>function foo() { 
    var x = 1; 
    if (x) { 
        (function () { 
            var x = 2; 
            // some other code 
        }()); 
    } 
    // x is still 1. 
}</pre>
이러한 방법은 실제로는 꽤 유연해서, 꼭 블록문에서가 아니라 임시 Scope이 필요한 어느 곳에서 사용될 수 있다. 그러나 나는 여러분이 JavaScript Scoping을 이해하고 진가를 아는데 시간을 투자하는 것을 강력히 추천한다. 그것은 굉장히 강력하고 내가 가장 좋아하는 JavaScript 특징 중의 하나다. 만약 여러분이 Scoping을 이해한다면, hosing은 여러분에게 제대로 잘 와닿을 것이다.
<h3>Declarations, Names and  Hoisting</h3>
JavaScript에서는 이름(name)은 네 가지 방식 중의 하나로 Scope에 포함된다.

1. Language-defined : 기본적으로 모든 Scope은 this와 arguments라는 이름이 주어진다.
2. Formal parameters : 함수는 이름을 가진 매개변수를 가질 수 있고, 그것은 함수 바디가 Scope이 된다.
3. Function declarations : 이것은 function foo() {}와 같은 형태다.
4. Variable declarations :  var foo; 와 같은 형태다.

함수 선언과 변수 선언은 자바스크립트 인터프리터에 의해 눈에 보이지 않게 그들을 포함하는 Scope의 맨 윗줄로 옮겨진다 (hoisted). <span style="color: #000000;">함수 매개변수와 언어에서 자체 정의된 이름의 경우는 항상 Scope의 맨 첫 부분에  존재한다. 다음의 코드를 보자.</span>
<pre><span style="color: #000000;">function foo() { 
    bar(); 
    var x = 1; 
}</span></pre>
위 코드는 실제로 다음과 같이 동작한다.
<pre>function foo() { 
    var x; 
    bar(); 
    x = 1; 
}</pre>
선언이 포함된 라인이 실행되는지 안되는지는 의미가 없다는 것을 알 수 있다. 다음 두 함수는 동일하다.
<pre>function foo() { 
    if (false) { 
        var x = 1; 
    } 
    return; 
    var y = 1; 
} 
function foo() { 
    var x, y; 
    if (false) { 
        x = 1; 
    } 
    return; 
    y = 1; 
}</pre>
선언문에서 할당 부분(= 오른쪽 부분)은  상위 이동(hosited)이 안되는 것에 주의해라. 단지 이름만이 상위 이동된다. 이것은 함수 선언의 경우 해당하지 않으며, 이 경우는 함수 바디 전체가 상위 이동될 것이다. 그러나 함수를 선언하는 방식은 두가지가 있다는 것을 기억해라. 다음 자바스크립트 소스를 살펴보자.
<pre>function test() { 
    foo(); // TypeError "foo is not a function" 
    bar(); // "this will run!" 
    var foo = function () { // function expression assigned to local variable 'foo' 
        alert("this won't run!"); 
    } 
    function bar() { // function declaration, given the name 'bar' 
        alert("this will run!"); 
    } 
} 
test();</pre>
이 경우, 함수 선언만이 그것의 바디 부분도 Scope 상위로 이동된다. 'foo'의 경우 이름만 상위 부분으로 이동이 되고, 몸체 부분은 실행 중 할당된 상태로 그대로 존재한다.

이것이 hoisting의 기본이며, 복잡하거나 혼동스럽지는 않다. 물론 몇몇 특정한 경우는 약간 복잡한 케이스가 있다.
<h3>Name Resolution Order</h3>
기억해야할 가장 특별한 경우는 이름 해석 순서(name resolution order)이다. 이름이 주어진 Scope에 들어가는 4가지 경우가 있다. 위에서 나열한 순서가 바로 그것들이 해석되는 순서다. 일반적으로 만약 이름이 이미 정의되어 있는 경우, 같은 이름의 다른 프로퍼티에 의해 덮어씌여지지는 않는다. 이것은 함수 선언이 변수 선언보다 상위 우선 순위를 가지고 있다고 볼 수 있다. 이것은 이름에 값을 할당하는 것이 처리가 되지 않는 것을 의미하는 것이 아니라, 단지 선언의 위치가 무시되는 것을 의미한다. 여기에 몇가지 예외 상황이 있다.
<ul>
	<li>예약어 arguments에 경우 특이하게 동작한다. 그것은 매개 변수들 다음에 선언된 것 처럼 보이지만, 함수 선언 전에 생성된다. 이것은</li>
	<li>this라는 이름을 아무것에서는 식별자로서 살용할 경우 SyntaxError를 발생시킨다.</li>
	<li>만약 여러개의 매개변수들이 동일한 이름을 가지고 있다면, 매개변수중 가장 최근에 생성된 것을 사용한다. 심지어 그것이 정의되지 않은 경우에도 말이다.</li>
</ul>
<h3>Named Function Expressions</h3>
함수 선언과 같은 문법으로, 함수 표현으로 정의된 함수에 이름을 부여할 수 있다. 이것은 그것을 함수 선언으로 만드는 것이 아니다. 그리고 이름이 Scope를 생성하지도 body가 상위 이동하지도 않는다. 이를 설명한 다음 코드를 살펴보자. (아래 spam() 함수가 위 설명에 해당한다.)
<pre>foo(); // TypeError "foo is not a function" 
bar(); // valid 
baz(); // TypeError "baz is not a function" 
spam(); // ReferenceError "spam is not defined" 

var foo = function () {}; // anonymous function expression ('foo' gets hoisted) 
function bar() {}; // function declaration ('bar' and the function body get hoisted) 
var baz = function spam() {}; // named function expression (only 'baz' gets hoisted) 

foo(); // valid 
bar(); // valid 
baz(); // valid 
spam(); // ReferenceError "spam is not defined"</pre>
<h3>How to Code With This Knowledge</h3>
이제 여러분은 scoping과 hoisting에 대해서 이해했다. 이러한 것들이 자바스크립트 코딩에서 무슨 의미를 가지는가? 가장 중요한것은 변수 선언을 할때 var를 앞에 선언해야 한다는 것이다. 나는 Scope당 정확히 한개의 var문만을 가지고, 그것을 Scope의 맨 윗부분에 정의하는 것을 강력히 추천한다. 만약 이러한 방식을 잘 지킨다면, 여러분은 hoisting과 관련한 혼란을 겪지 않을 것이다. 그러나 이 경우 어느 변수들이 현재 Scope에 선언되어 있는지를 추적하는 것이 어렵게 된다. (역자. 무슨 말인지 이해안감). 나는 이를 위해 JSLint의 onevar 옵션(변수 선언을 하나의 var를 이용해서 하는 것)을 사용할 것을 추천한다. 만약 여러분이 내가 제시한 룰을 따른 다면, 여러분의 코드는 다음과 같은 형태일 것이다.
<pre>/*jslint onevar: true [...] */ 
function foo(a, b, c) { 
    var x = 1, 
        bar, 
        baz = "something"; 
}</pre>
<h3>What the Standard Says</h3>
나는 이와 같은 동작을 이해하기 위해 <a href="http://www.mozilla.org/js/language/E262-3.pdf" target="_blank">ECMAScript Standard (pdf)</a>를 직접 살펴보는 것이 유용하다는 것을 알았다. 다음은 변수 선언과 Scope(Section 12.2.2)에 대해 언급하는 부분이다.
<blockquote>10.1.3절에서 설명한 것처럼 변수 선언문이 함수 선언 안에 있다면, 변수들은 그 함수의 로컬 scope로 정의된다. 그렇지 않다면 변수 선언은 global scope에서 정의가 된다(즉, 그것들은 global 객체의 멤버로 생성된다.) 변수는 실행 Scope에 진입할때 생성된다. 블록은 새로운 실행 scope를 정의하지 않는다. 단지 프로그램와 함수 선언만이 새 Scope를 만든다. 변수는 생성시에 undefined로 초기화된다. 초기화 값이 정의된 변수의 경우는 변수가 생성될 때가 아닌 var문이 실행될 때, 할당 값이 저장된다.</blockquote>