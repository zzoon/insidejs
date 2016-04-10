---
layout: post
title: '[번역] 자바스크립트에서 변수와 프로퍼티의 차이'
date: '2011-04-01 10:08:05'
---

<h1>Variables vs. Properties in JavaScript</h1>
원본글 : <a href="http://javascriptweblog.wordpress.com/2010/08/09/variables-vs-properties-in-javascript/">http://javascriptweblog.wordpress.com/2010/08/09/variables-vs-properties-in-javascript/</a>

프로퍼티는 무엇인가? 변수란 무엇인가? 둘 간의 차이가 있다면 어떤 것인가?

기본적인 질문이다. 이것은 언어를 이해하기 위한 기본적인 내용이지만, 대개 자바스크립트에서는 그냥 대충 넘어간다.
<h2>The VariableObject</h2>
자바스크립트 변수가 무엇인지를 이해하기 위해서는 VariableObject에 대해 살펴봐야 한다. 자바스크립트에서 코드는 전역이나 함수 컨텐스트 내에서 실행될 수 있다. 전역 컨텐스트는 오직 하나만 존재한다. 함수 컨텐스트는 함수 호출 시 마다 존재한다. 각 실행 컨테스트는 자신과 연관된 VariableObject를 가진다. 주어진 컨텍스트에서 생성된 변수(과 함수)들은 컨텍스트의 VariableObject의 프로퍼티로 존재하낟.

global 컨텐스트의 VariableObject는 global 객체다. 브라우저에서 global 객체는 window다.

[sourcecode language="javascript"]
var a = &quot;hello&quot;;
//window is the global VariableObject
window.a; //hello
[/sourcecode]

함수 컨텐스트 경우는 좀더 복잡하다. 각 함수는 자신의 VariableObject (보통은 ActivationObject로 알려져 있음)를 가지고 있지만, Rhino를 사용하지 않는다면 보통은 접근할 수 없다. 여러분은 단지 각각의 함수가 이 객체를 가지고 있다는 사실만 알면 된다. 그러므로 여러분이 함수 컨텍스트 안에서 변수를 생성한다면, 그것을 프로퍼티로서 참조할 수 없다.

[sourcecode language="javascript"]
function foo() {
    var bar = &quot;sausage&quot;;
    window.bar; //undefined (VariableObject is not window)
}
[/sourcecode]

그렇다면 우리는 다음과 같은 의문을 가질 수 있다.
<h2>프로퍼티란 무엇인가?</h2>
ECMA5 : 객체의 일부로 이름과 값 사이 연결을 의미 [4.3.26]

다시 말하면, 프로퍼티는 객체를 구성하는 블록들이다.

[sourcecode language="javascript"]
//Examples of properties
foo.bar = &quot;baz&quot;;
window.alert;
a.b = function(c,d,e) {return (c * d) + e};
Math.PI;
myArray[5];
[/sourcecode]
<h2>변수란 무엇인가?</h2>
불행하게 ECMA5는 다음과 같은 정의를 강요하지는 않는다.
<blockquote>이렇게 정의 해보자 : 변수는 실행 컨텍스트 내에 존재하는 이름과 값 사이의 연결을 의미한다.</blockquote>
우리는 이미 변수와 프로퍼티의 본질적인 차이를 알 수 있다. 프로퍼티는 객체에 포함되어 있고, 변수는 컨텍스트에 포함되어 있다. (해당 컨텍스텍의  VariableObject의 프로퍼티에 변수들이 포함되어 있다.)

[sourcecode language="javascript"]
//Examples of variables
var bar = 2; //global context
function foo = function() {
    var a; //function context
    f = 4; //global context (probably unintentionally)
}
[/sourcecode]
<h2>그러나 변수와 프로퍼티는 바로 서로 교체가 가능한가??</h2>
원래는 불가하지만, 다음과 같은 방식에서 확인할 수 있다.

[sourcecode language="javascript"]
//define as a property, access as a variable
window.foo = &quot;a&quot;;
foo; //a

//define as a variable, access as a property
var bar = 54;
window.bar; //54
[/sourcecode]

이것은 global 객체(프로퍼티의 부모)와 전역 VariableObject(변수의 부모)가 동일하기 때문에 일어난다.
당연히 함수 컨텐스트에서 프로퍼티와 변수의 상호 교환은 오동작 할 것이다.
<h2>Ok, so why should I care?</h2>
변수와 프로퍼티 간에는 객체 구성과 프로그램 흐름에 영향을 주는 몇 가지 차이가 있다.
<h3>hoisting</h3>
<span style="color: #ff0000;">지난 포스팅(링크)에서 hoisting에 대해 상세하게 설명했다. 그것은 다음과 같이 요약된다. (이전 블로그 글의 링크 필요) </span>변수나 함수 선언으로 정의된 객체는 실제 코드의 위치와 상관없이 자신의 실행 Scope의 시작 부분에서 생성된다. 반면에 프로퍼티 정의는 프로그램 제어가 statement에 도달할 때 생성된다. (즉, hoisting 되지 않는다.)

[sourcecode language="javascript"]
alert(a); //undefined (no error)
alert(b); //ReferenceError: b is not defined
var a = 24;
window.b = 36;
[/sourcecode]

예제에서 두 가지를 주목하자.

1) 변수  a는 hoisted 지만, 그것의 값은 아니다. (FunctionDeclarations의 hositing과는 별개)

2) 우리는 window.b라는 프로퍼티 문법으로 간단히 b에 접근함으로써 ReferenceError를 피할 수 있다. 객체 한정자 없이 b를 접근할 때, 자바스크립트는 우리가 변수를 참조하고 있다고 가정하고 VariableObject(그것은 b라는 프로퍼티를 가지고 있지 않다)를 체크한다. 이때 식별자가 존재하지 않으면 ReferenceError가 발생한다. 반대로 단순한 프로퍼티 접근자는 부모 객체 상에서의 해시 룩업 결과를 반환할 것이다. (이 경우는 undefined)
<h3>attribute initialization</h3>
모든 새로운 프로퍼티는 디폴트로 <strong>프로퍼티 디스크립터</strong>를 가진다. 프로퍼티 디스크립터는 몇몇 프로퍼티 속성을 정의한다 ([[value]] 는 가장 명확하다). ECMA3는 내부 사용을 목적으로 다음과 같은 속성덜을 예약했다 : {DontDelete}, {DontEnum},  {ReadOnly}. ECMA5에서 이러한 속성들의 이름이 [[Writable]],[[Enumerable]], [[Configurable]] 로 변경됐다. ECMA5에 따르면 그것들은 외부 수정가능하다. 좀더 자세한 내용은 <a href="http://dmitrysoshnikov.com/ecmascript/es5-chapter-1-properties-and-property-descriptors/">http://dmitrysoshnikov.com/ecmascript/es5-chapter-1-properties-and-property-descriptors/</a> 을 참조하기 바란다.

좀더 단순하게 나는 이글과 관련있는 하나의 속성(ECMA3에서의 [[DontDelete]]에 집중할 것이다.

당신이 <strong>변수</strong>를 생성할 때 그것은 [[DontDelete]] 속성은 true로 설정된다. 여러분이 명식적으로 <strong>프로퍼티</strong>를 생성하면, 초기에는 그것의 [[DontDelete]] 속성은 false로 설정된다.

[sourcecode language="javascript"]
//variable
var oneTimeInit = function() {
    //do stuff
}
delete oneTimeInit; //false (means it did not happen)
typeof oneTimeInit; &quot;function&quot;;

//explicit property
window.oneTimeInit = function() {
    //do stuff
}
delete oneTimeInit; //true
typeof oneTimeInit; &quot;undefined&quot;;
[/sourcecode]

delete에 변수와 프로퍼티에 어떻게 적용되는지 에 대한 자세한 설명은 <a href="http://perfectionkills.com/understanding-delete/">http://perfectionkills.com/understanding-delete/</a>을 살펴봐라.
<h3>illegal names</h3>
subscript notation(대괄호)을 이용할 경우, 잘못된 식별자를 통해서도 프로퍼티에 접근할 수 있지만, 변수의 경우는 에러가 발생한다.

[sourcecode language="javascript"]
//illegal name
var a = &quot;***&quot;;
window[a] = 123;
window[a]; //123 (Property lookup OK)
*** //ReferenceError (illegal name)

//legal name
var a = &quot;foo&quot;;
window[a] = 123;
window[a]; //123
foo; //123
[/sourcecode]
<h2>What other kinds of variables are there?</h2>
함수의 arguments 객체와 각각 인자들은 ActivationObject(i.e. 함수의 VariableObject)에 추가된다. 함수 선언들은 이 객체의 프로퍼티들이고, <span style="color: #000000;">따사서 변수로 취급된다.
(역자주, 위 예제에서 변수들은 VariableObject에 프로퍼티로 관리된다라고 소개한 적이 있다) </span>
<h2>How many ways can I define a property?</h2>
적어도 5가지 이상이다. 다음 예제를 살펴보자.

[sourcecode language="javascript"]
//dot notation
window.foo = 'hello';

//subscript notation
window['foo'] = 'hello';

//forgetting to use the var keyword
var bar = function() {
    foo = &quot;hello&quot;;
}

//Using ECMA 5 methods (showing limited use of property attributes for clarity)
//runs in chrome, safari and IE8 (IE8 works for DOM objects only)
Object.defineProperty(window,&quot;foo&quot;, {value: &quot;hello&quot;});

//runs in chrome and safari
Object.defineProperties(window, {&quot;foo&quot;: {value: &quot;hello&quot;},&quot;bar&quot;: {value: &quot;goodbye&quot;}});
[/sourcecode]