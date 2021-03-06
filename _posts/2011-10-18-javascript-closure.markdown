---
layout: post
title: Javascript Closure
date: '2011-10-18 02:18:27'
---

<div>
<p style="font-size: 14px">Closure 는 자바스크립트에서 상당히 중요한 개념이다. 하지만 이를 정확하게 이해하는 것은 쉽지 않다.</p>
<p style="font-size: 14px">이를 이해하고자, closure에 대해 가장 자세하게 설명되었다고 판단되는 문서를 찾아서 번역해 보았다.</p>
<p style="font-size: 14px">해당 URL은 다음과 같다.</p>
<p style="font-size: 14px">http://jibbering.com/faq/notes/closures/#clExCon</p>
<p style="font-size: 18px"><strong>Identifier Resolution, Execution Context ans scope chain</strong>
<strong> 식별자 인식, 실행 컨텍스트, 스코프 체인</strong></p>
<p style="font-size: 14px"><strong>실행 컨텍스트</strong></p>
<p style="font-size: 14px">실행컨텍스트는 ECMAScript에서 명시된 추상화된 개념이다.</p>
<p style="font-size: 14px">모든 자바스크립트 코드는 이 실행 컨텍스트 안에서 실행된다. 전역 코드(인라인으로 실행되는 코드, 일반 JS파일, HTML 페이지 등)는 전역 실행 컨텍스트에서 실행된다. 각 함수의 실행 역시 각자의 실행 컨텍스트를 가진다. eval함수로 실행되는 코드 역시 또다른 실행 컨텍스트를 가지지만, 프로그래머들은 일반적으로는 이 함수를 사용하지 않는다. 실행 컨텍스트는 ECMA 262의 섹션 10.2에서 명시되어 있다.</p>
<p style="font-size: 14px">자바스크립트 함수가 호출되면 실행 컨텍스트로 진입한다. 또다른 함수가 호출되었을 때(혹은 같은 함수가 recursive로 호출되도) 새로운 실행 컨텍스트가 만들어지고, 함수 호출되는 동안 이 컨텍스트 안에서 실행된다. 호출된 함수가 반환되면, 원래의 실행컨텍스트로 돌아간다.
이처럼 자바스크립트가 실행되면 실행 컨텍스트가 스택의 형태로 구성된다.</p>
<p style="font-size: 14px">실행 컨텍스트가 생성되면, 수많은 일들이 정해진 순서대로 발생한다. 먼저, 함수의 실행 컨텍스트 안에서 하나의 객체가 만들어지고 활성화된다.(역자: 이를 저자는 "Activation object"로 표현했다. 이후에 나오는 "Variable Object"와 같은 것이다.)
이 "활성화 객체"(Activation object)는 일반적인 것과는 다른 메커니즘을 가진다. 이 객체는 하나의 객체로 인식될 수 있는데, 이는 접근 가능한 프로퍼티를 가지게 되기 때문이다. 하지만 프로토타입을 가지지 않으며, 코드에 의해 직접적으로 접근할 수 없으므로, 일반적인 객체는 아니다.</p>
<p style="font-size: 14px">함수 호출시 실행컨텍스트를 생성하는 그 다음 단계는 arguments 객체를 생성하는 것이다. 이것은 배열과 비슷한 객체인데, 함수호출시에 넘겨받는 인자들에 순서대로 대응되는 멤버를 가지고 정수 인덱스로 접근할 수 있다. 이 객체는 length와 callee 프로퍼티를 가진다. "활성화 객체"는 arguments라는 프로퍼티를 가지고, 이 프로퍼티는 arguments객체를 참조한다.</p>
<p style="font-size: 14px">다음 단계에서 실행 컨텍스트에 스코프(scope)가 할당된다. 스코프는 리스트(혹은 체인)로 연결된 객체들을 포함한다. 각 함수 객체는 내부적으로 [[scope]] 프로퍼티를 가지는데, 이것 역시 리스트(혹은 체인)로 연결된 객체들을 가진다. 이 스코프는 [[scope]] 프로퍼티를 통해 참조되는 리스트로 연결된 객체들을 포함한다. 여기서 이 객체들은 각각의 함수 객체에 대응되는데, 이 체인(혹은 리스트)의 처음 객체로서 "활성화 객체"가 추가된다.</p>
<p style="font-size: 14px">그리고 나서 변수의 생성(instantiation)이 이루어지는데, 이는 ECMA 262에 나오는 "변수 객체(Variable object)"를 통해 이루어진다. 그러나, 앞에서 언급한 "활성화 객체"가 "변수 객체"로서 사용된다.(이 둘은 똑같다. 매우중요) "변수 객체"안에서 이름이 지어진 프로퍼티(named property)들이 함수의 공식적인 인자들 각각에 대해 생성되고 값이 넘겨졌다면 그 값이 각 프로퍼티에 할당된다. (없으면 undefined가 할당됨) 내부함수가 정의되었다면, 함수객체를 생성하고, 그 이름에 대응되는 "변수 객체"의 프로퍼티에 할당된다. 변수 생성의 마지막 단계는 함수안에 선언된 지역 변수에 대한 "변수 객체"의 프로퍼티를 생성하는 것이다.</p>
<p style="font-size: 14px">"변수 객체"에서 생성된 지역 변수에 대한 프로퍼티는 생성될 당시에는 undefined가 할당되고, 실제 초기화(initialization)는 함수가 실행되면서 해당 표현식이 실행되기 전까지는 이루어지지 않는다.</p>
<p style="font-size: 14px">여기서 arguments 프로퍼티를 가지는 "활성화 객체"와 지역 변수에 대한 프로퍼티를 가지는 "변수 객체"는 모두 같은 객체이고, arguments를 함수의 지역 변수처럼 사용할수 있도록 한다.</p>
<p style="font-size: 14px">마지막 단계에서 this라는 키워드를 사용하기 위해 값이 할당된다. 이 값이 어떤 객체에 대한 참조라면 이 this키워드는 해당 객체의 참조 프로퍼티를 가진다. 만약 값이 null이면 this 는 전역 객체를 참조할 것이다.</p>
<p style="font-size: 14px">전역 실행 컨텍스트는 일반적인 실행 컨텍스트와는 약간 다른데, "활성화 객체"를 만들어 참조할 필요가 없으므로 argument를 가지지 않는다. 전역 실행 컨텍스트는 스코프와 이 스코프의 체인(전역객체 하나만을 포함하는)이 필요하다. 전역 객체의 실행 컨텍스트는 변수 초기화를 하고 이것의 내부함수들은 일반적인 탑레벨의 함수로서 선언된다. 전역 객체는 "변수 객체"로서 사용되는데, 이 때문에 전역적으로 선언된 함수와 변수가 전역 객체의 프로퍼티가 된다.</p>
<p style="font-size: 14px">전역 실행 컨텍스트 역시, 전역 객체에 대한 참조로서 this를 사용한다.</p>
<p style="font-size: 18px"><strong>스코프 체인과 [[scope]]</strong></p>
<p style="font-size: 14px">함수 호출시, 실행 컨텍스트의 스코프 체인은 실행 컨텍스트의 활성화/변수 객체를 스코프체인의 제일 앞에 추가함으로써 생성된다.
그리고 [[scope]] 프로퍼티는 이렇게 생성된 스코프 체인을 참조한다. 따라서, [[scope]]가 어떻게 정의되는지를 이해하는 것은 매우 중요하다.</p>
<p style="font-size: 14px">ECMAScript에서는 함수는 객체이고, 함수 선언을 통한 변수 생성 시, 함수 표현식의 실행 시, Function 생성자로 실행 시에 함수 객체는 생성된다.</p>
<p style="font-size: 14px">Function 생성자에 의해 생성된 함수 객체는 항상 [[scope]] 프로퍼티를 가진다. 그리고 이 프로퍼티는 오직 전역객체만을 가지는 스코프 체인을 참조한다.</p>
<p style="font-size: 14px">함수 선언이나 함수 표현식에 의해 생성되는 함수 객체는 이 함수 객체의 실행 컨텍스트 안에서 스코프 체인을 가지고 [[scope]] 프로퍼티에 할당된다.</p>
<p style="font-size: 14px">전역 함수 선언의 간단한 예</p>

{% highlight js %}
function exampleFunction(formalParameter){
    ...   // function body code
}
{% endhighlight %}

<p style="font-size: 14px">이 예제에서 함수 객체는 전역 실행 컨텍스트 내에서 변수가 생성될 때 만들어진다. 전역 실행 컨텍스트는 오로지 전역객체 만을 가지는 스코프 체인을 가진다. 그리고, "exampleFunction"이라는 이름으로서 전역객체의 프로퍼티로 참조되고, [[scope]] 프로퍼티에 할당된다.</p>
<p style="font-size: 14px">함수 표현식이 전역 컨텍스트에서 실행될 때 비슷한 스코프 체인이 할당되는 예제</p>

{% highlight js %}
var exampleFuncRef = function(){
    ...   // function body code
}
{% endhighlight %}

<p style="font-size: 14px">전역 실행 컨텍스트에서 변수 생성 시, 전역객체의 한 이름있는 프로퍼티(exampleFuncRef)가 생성되고, 이에 대한 참조가 해당 프로퍼티에 할당되는데, 할당 표현식이 실행되기 전까지는 함수 객체가 생성되지 않는다. ( 역자. 이 때는 undefined가 exampleFuncRef에 할당될 것이다.)그러나, 할당 표현식이 실행되면, 함수 객체가 전역 실행 컨텍스트 안에서 생성되고, 생성된 함수객체의 [[scope]]프로퍼티는 전역 객체만을 가지는 스코프 체인이 할당된다.</p>
<p style="font-size: 14px">내부함수 선언과 표현식은 해당 함수(외부함수)의 실행컨텍스트 안에서 생성되고, 보다 정교한 스코프 체인을 가진다.
내부함수를 선언한 후 외부함수를 실행시키는 다음의 코드를 보자.</p>

{% highlight js %}
function exampleOuterFunction(formalParameter){
    function exampleInnerFuncitonDec(){
        ... // inner function body
    }
    ...  // the rest of the outer function body.
}

exampleOuterFunction( 5 );
{% endhighlight %}

<p style="font-size: 14px">외부함수 객체는 전역 실행 컨텍스트안에서 변수 생성 시, 만들어진다. 이 객체의 [[scope]]프로퍼티는 전역객체만을 가지는 스코프 체인을 가진다.</p>
<p style="font-size: 14px">전역 코드가 실행되면서 exampleOuterFunction을 호출하면, 새 실행 컨텍스트가 생성되고, 이에 따라 활성화/변수 객체가 생성된다. 새 실행 컨텍스트의 스코프는 새로운 활성화 객체를 포함하는데, 이 활성화 객체에는 외부 함수의 [[scope]]에 의해 참조되는 체인에 추가된다. 새로운 실행 컨텍스트에서 변수 생성을 하면, 내부 함수 객체가 생성되고, 이 객체의 [[scope]]프로퍼티는 해당 실행 컨텍스트의 스코프가 할당된다. 이 내부의 활성화 객체를 포함하는 스코프 체인은 전역 객체와 연결된다.</p>
<p style="font-size: 14px">지금까지는 모두 소스 코드의 구조와 실행을 통해 자동으로 제어되었다. 실행 컨텍스트의 스코프 체인은 새롭게 생성되는 함수객체의 [[scope]] 프로퍼티를 정의하고, 이 [[scope]] 프로퍼티는 해당 함수의 실행컨텍스트의 스코프를 (활성화 객체와 함께) 정의한다. 그런데, ECMAScript에서는 이러한 스코프 체인을 수정하기 위한 with 구문을 제공한다.</p>
<p style="font-size: 14px">with 구문은 표현식을 실행하는데, 표현식이 객체이면 객체는 현재 실행 컨텍스트의 스코프 체인에 추가된다( 활성화 객체의 바로 앞에). with 구문은 다른 구문(블록 구문일수도 있음)을 실행하고 실행 컨텍스트의 스코프 체인을 전에 있던 곳에 저장한다.</p>
<p style="font-size: 14px">함수 선언은 with 구문의 영향을 받지 않고, 함수 객체가 생성되지만, 함수 표현식은 안에서 with 구문과 함께 실행될 수 있다.</p>

{% highlight js %}
/* create a global variable - y - that refers to an object:- */
var y = {x:5}; // object literal with an - x - property
function exampleFuncWith(){
    var z;
    /* Add the object referred to by the global variable - y - to the
       front of he scope chain:-
    */
    with(y){
        /* evaluate a function expression to create a function object
           and assign a reference to that function object to the local
           variable - z - :-
        */
        z = function(){
            ... // inner function expression body;
            console.log(x);
        }
    }
    ...
}

/* execute the - exampleFuncWith - function:- */
exampleFuncWith();
{% endhighlight %}

<p style="font-size: 14px">exampleFuncWith 함수가 호출되면 실행 컨텍스트는 전역객체를 따라가는 활성화객체를 포함하는 스코프 체인을 가진다. with 구문의 실행은 전역 변수 y에 의해 참조되는 객체를 함수 표현식이 실행되는 동안 스코프 체인의 맨앞에 추가한다. 새롭게 생성되는 함수 객체는 [[scope]]프로퍼티에 추가된다. y객체를 포함하는 스코프체인은 외부함수 호출시의 실행컨텍스트의 활성화 객체를 따라간다.</p>
<p style="font-size: 14px">with 구문안의 블록 구문이 종료되면, 실행 컨텍스트의 스코프는 저장된다.(y객체는 제거된다). 하지만 이때 함수 객체는 만들어졌고, 이것의 [[scope]] 프로퍼티는 y객체를 처음 요소로 가지는 스코프 체인을 참조한다.</p>
<p style="font-size: 18px"><strong>식별자 인식 (identifier resolution)</strong></p>
<p style="font-size: 14px">식별자는 스코프 체인을 통해 인식된다. ECMA 262에서는 this를 식별자가 아닌 키워드로 분류한다. 이는 this 가 항상 스코프 체인에 대한 참조 없이 실행 컨텍스트 안에서 사용되는 값에 따라 다르게 인식되기 때문이다.</p>
<p style="font-size: 14px">식별자 인식은 스코프 체인의 첫번째 객체부터 시작한다. 식별자와 대응되는 이름을 가진 프로퍼티가 있는지를 확인한다. 스코프 체인은 객체의 체인이기 때문에 이러한 확인 작업은 객체의 프로토타입 체인을 아우른다. 스코프 체인의 첫번째 객체에서 대응되는 값이 없다면 검색 작업은 다음 객체로 넘어간다. 이런 식으로 대응되는 이름의 프로퍼티를 찾을 때까지 계속된다.</p>
<p style="font-size: 14px">식별자에 대한 이러한 작업은 위에서 설명된 객체에 대한 프로퍼티 접근자(Accessor)를 사용하는 방법과 같다. 대응되는 프로퍼티를 가진 것으로 확인된 객체는 프로퍼티 접근자의 객체를 대신한다.( 역자주 - 이 번역된 문서 바로 전에 프로퍼티 접근자에 대한 설명이 있었음) 그 식별자는 프로퍼티의 이름으로서 작동한다. 전역 객체는 항상 스코프체인의 마지막에 있다.</p>
<p style="font-size: 14px">함수 호출시의 실행 컨텍스트는 체인의 가장 앞에 활성화/변수 객체를 가질 것이다. 함수 안의 식별자들은 공식적인 인자, 내부함수 선언, 지역 변수에 대응되는 지를 먼저 확인한다. 이것들은 활성화/변수 객체의 프로퍼티로 인식될 것이다.</p>
<p style="font-size: 18px"><strong>클로져(closures)</strong></p>
<p style="font-size: 14px"><strong>자동 가비지 컬렉션</strong></p>
<p style="font-size: 14px">ECMAScript에서는 자동 가비지 컬렉션을 사용하지만, 구체적으로 명시되어 있지는 않고, 구현자에게 정리하도록 한다. 그리고 실제 구현에서는 이 가비지 컬렉션 작업에 상당히 낮은 우선순위가 주어져있다고 알려져 있다. 하지만, 일반적으로 한 객체가 어떤 것에도 참조되고 있지않다면 이것은 가비지 컬렉션의 대상이 된다. 그리고 언젠가 소멸되고 그 자원은 재사용을 위해 시스템에 반환된다.</p>
<p style="font-size: 14px">이것은 보통 실행컨텍스트 안에 있는 경우를 말한다. 스코프 체인이 더이상 접근 가능하지 않을 때 가비지 컬렉션의 대상이 된다.</p>
<p style="font-size: 14px"><strong>클로져 형성</strong></p>
<p style="font-size: 14px">클로져는 함수 호출을 통해 실행 컨텍스트 안에서 만들어진 함수객체가 반환될 때 만들어진다. 이 함수 호출시 내부 객체에 대한 참조가 또다른 객체의 프로퍼티에 할당된다.
혹은 직접적으로 이 함수 객체에 대한 참조를 할당함으로써 만들 수 있는데, 예를 들면, 전역 변수(즉, 전역적으로 접근 가능한 객체의 프로퍼티)나, 외부함수 호출시의 인자에 대한 참조를 넘겨받은 객체를 들 수 있다.</p>

{% highlight js %}
function exampleClosureForm(arg1, arg2){
    var localVar = 8;
    function exampleReturned(innerArg){
        return ((arg1 + arg2)/(innerArg + localVar));
    }
    /* return a reference to the inner function defined as -
       exampleReturned -:-
    */
    return exampleReturned;
}

var globalVar = exampleClosureForm(2, 4);
{% endhighlight %}

<p style="font-size: 14px">위 예제에서는 exampleClosureForm 함수를 호출하면서 생기는 실행 컨텍스안에서 함수 객체가 생성된다. 그리고 이는 가비지 컬렉션의 대상이 될 수 없다. 왜냐하면 전역 변수(globalVar)에 의해 참조되므로 여전히 접근 가능하기 때문이다. 이것은 globalVar(n)의 형태로 실행될 수 있다.</p>
<p style="font-size: 14px">하지만 이 예제에는 좀 더 복잡한 것이 있다. globalVar에 의해 참조되는 함수 객체는 [[scope]]프로퍼티와 함께 만들어졌다. 이 [[scope]]프로퍼티는 앞에서 설명한 대로, 해당 실행 컨텍스트에 속하는 활성화/변수 객체를 가지고 있다. 즉, 이 활성화/변수 객체 역시 가비지 컬렉션의 대상이 되지 않는다. globalVar를 통해 함수를 실행하면, 이 [[scope]] 프로퍼티에 전체 스코프 체인을 추가하게 된다.</p>
<p style="font-size: 14px">클로져는 이렇게 만들어진다. 내부 함수 객체는 변수를 가지고 있는데, 스코프 체인에 있는 활성화/변수 객체는 이 변수들에 바인딩된 환경이라고 할 수 있다.</p>
<p style="font-size: 14px">활성화/변수 객체는 globalVar에 의해 참조되는 함수 객체의 [[scope]] 프로퍼티에 의해 참조되면서 같이 보존된다. 그리고 그 프로퍼티의 값은 여전히 (심지어는 실행 컨텍스트가 끝났음에도) 읽기 및 설정까지 가능하다. (이 활성화 객체를 편의상 "ActOuter1"로 부르겠다)</p>
<p style="font-size: 14px">위 예제에서는 exampleClosureForm 함수가 실행이 끝나 반환되지만, 활성화/변수 객체는 인자와 내부함수의 정의 및 지역 변수를 가지고 있다. arg1은 2, arg2는 4, localVar는 8, 그리고 exampleReturned는 내부 함수 객체에 대한 참조를 가지고 있다.</p>
<p style="font-size: 14px">만약, exampleClosureForm을 아래와 같이 다시 호출한다면,</p>

{% highlight js %}
var secondGlobalVar = exampleClosureForm(12, 3);
{% endhighlight %}

<p style="font-size: 14px">새로운 실행 컨텍스트가 실행되고, 따라서 새로운 활성화 객체(편의상 "ActOuter2") 역시 만들어진다. 새로운 함수 객체가 자신의 [[scope]]와 arg1-12, arg2 -3와 함께 반환될 것이다.</p>
<p style="font-size: 14px">두번째로 또다른 클로져가 만들어졌다.</p>
<p style="font-size: 14px">두개의 함수 객체가 exampleClosureForm을 실행하면서 만들어졌는데, 각각 globalVar와 secondGlobalVar에 할당되었다. 이 두객체의 각 4개의 식별자(((arg1 + arg2)/(innerArg + localVar)))를 어떻게 인식하는지를 이해하는 것은 클로자의 값을 사용하는 데 있어서 매우 중요하다.</p>
<p style="font-size: 14px">globalVar(2)를 실행했다고 생각해보자. 새로운 실행컨텍스트와 활성화 객체(편의상 "ActInner1"로 호칭)가 만들어지고 이는 [[scope]]프로퍼티가 가리키는 스코프체인의 첫번째 요소로 삽입된다. 이 실행 컨텍스트의 스코프체인은 다음과 같다 : ActInner1-> ActOuter1-> 전역객체</p>
<p style="font-size: 14px">식별자 인식은 이 스코프 체인을 통해 이루어진다. ((arg1 + arg2)/(innerArg + localVar)) 의 값들은 스코프 체인의 각 객체를 찾아보고 결정된다.</p>
<p style="font-size: 14px">체인의 첫번째 객체는 ActInner1이고 innerArg-2를 가지고 있다. 나머지 3개의 식별자는 ActOuter가 가지고 있고, 값은 arg1-2, arg2-4, localVar-8 이 된다. 결국 ((2+4)/(2+8)) 이 된다.</p>
<p style="font-size: 14px">secondGlobalVar(5)를 실행한 것과 위를 비교해 보자. 위와 마찬가지 방식으로 새로운 활성화 객체(편의상 "ActInner2")가 만들어지고, 해당 실행 컨텍스트의 스코프체인은 다음과 같다 : ActInner2-> ActOuter2-> 전역 객체</p>
<p style="font-size: 14px">ActInner2는 innerArg-5를 가지고 있고, ActOuter2는 arg1-12, arg2-3, localVar-8을 가진다. 결국 ((12 + 3)/(5 + 8)) 이 된다.</p>
<p style="font-size: 18px"><strong>클로저로 무엇을 할 수 있는가?</strong></p>
<p style="font-size: 14px"><strong>예제 1: 함수 참조를 통한 setTimeout</strong></p>
<p style="font-size: 14px">클로저를 사용하는 가장 일반적인 용도는 함수를 실행하기에 앞서 함수 실행에 필요한 인자를 제공하는 것이다.</p>
<p style="font-size: 14px">예를 들면, 웹브라우저에서 setTimeout함수의 첫번째 인자로 함수를 넘길 때가 그것이다.</p>
<p style="font-size: 14px">setTimeout은 첫번째 인자로 넘겨지는 함수(혹은 자바스크립트 코드를 담은 문자열)의 실행에 대한 스케쥴링을 하는데, 두번째 인자인 밀리초 단위의 숫자만큼의 간격으로 함수를 호출한다. setTimeout을 통해 자신의 코드를 호출하고 싶다면 첫번째 인자로 해당 함수 객체의 참조를 넘겨주면 되지만, 이것으로는 실제 실행될 때, 함수에 인자를 줄 수 없다.</p>
<p style="font-size: 14px">그러나, 내부함수 객체의 참조를 반환받는 또다른 함수를 호출할 수도 있다. 이때, 인자는 내부함수(결국 자신을 반환하는)를 실행할 때 같이 넘겨진다. setTimeout은 내부함수를 인자없이 호출하지만, 이 내부함수는 여전히 외부함수 호출 때 제공되었던 인자에 접근할 수 있다.</p>

{% highlight js %}
function callLater(paramA, paramB, paramC){
    /* Return a reference to an anonymous inner function created
       with a function expression:-
    */
    return (function(){
        /* This inner function is to be executed with - setTimeout
           - and when it is executed it can read, and act upon, the
           parameters passed to the outer function:-
        */
        paramA[paramB] = paramC;
    });
}

...

/* Call the function that will return a reference to the inner function
   object created in its execution context. Passing the parameters that
   the inner function will use when it is eventually executed as
   arguments to the outer function. The returned reference to the inner
   function object is assigned to a local variable:-
*/
var functRef = callLater(elStyle, "display", "none");
/* Call the setTimeout function, passing the reference to the inner
   function assigned to the - functRef - variable as the first argument:-
*/
hideMenu=setTimeout(functRef, 500);
{% endhighlight %}

<p style="font-size: 14px"><strong>예제 2: 함수와 객체 인스턴스 메소드 연결 ( Associating Functions with Object Instance Methods )</strong></p>
<p style="font-size: 14px">미래의 어떤 시점에서 실행될 함수가 알맞은 인자를 넘겨받기 위해 함수 객체에 대한 참조를 다른 변수에 할당해야 되는 경우가 많이 있다.</p>
<p style="font-size: 14px">예를 들면, 특정 DOM 요소와의 상호작용을 캡슐화하도록 디자인되어 있는 객체가 있다고 하자.
이 객체는 doOnClick, doMouseOver, doMouseOut 메소드를 가지며, 해당 이벤트가 발생하면 이 메소드들이 실행되어야 한다. 하지만, 서로다른 DOM 요소들과 관계된 객체의 인스턴스 숫자는 알 수 없다. 그리고 각 객체 인스턴스는 생성코드에서 어떻게 사용될 지 알 수 없다. 이 인스턴스는 어떤 전역변수에 자신이 할당되는지 모르므로, 자신에 대한 참조를 어떻게 하는지 알 수 없다.</p>
<p style="font-size: 14px">문제는 특정 인스턴스와 관계된 이벤트 핸들링 함수를 어떻게 알고 실행시켜야하느냐 이다.</p>
<p style="font-size: 14px">아래 예제는 객체 인스턴스와 요소 이벤트 핸들러와 관계를 맺는 함수들을 바탕으로한 작고 일반적인 클로저를 사용한다.
객체 인스턴스의 특정 메소드(이벤트 핸들러)가 호출될 때, 이벤트 객체와 관계된 요소에 대한 참조를 넘겨받고, 리턴값을 반환한다.</p>

{% highlight js %}
/* A general function that associates an object instance with an event
   handler. The returned inner function is used as the event handler.
   The object instance is passed as the - obj - parameter and the name
   of the method that is to be called on that object is passed as the -
   methodName - (string) parameter.
*/
function associateObjWithEvent(obj, methodName){
    /* The returned inner function is intended to act as an event
       handler for a DOM element:-
    */
    return (function(e){
        /* The event object that will have been parsed as the - e -
           parameter on DOM standard browsers is normalised to the IE
           event object if it has not been passed as an argument to the
           event handling inner function:-
        */
        e = e||window.event;
        /* The event handler calls a method of the object - obj - with
           the name held in the string - methodName - passing the now
           normalised event object and a reference to the element to
           which the event handler has been assigned using the - this -
           (which works because the inner function is executed as a
           method of that element because it has been assigned as an
           event handler):-
        */
        return obj[methodName](e, this);
    });
}

/* This constructor function creates objects that associates themselves
   with DOM elements whose IDs are passed to the constructor as a
   string. The object instances want to arrange than when the
   corresponding element triggers onclick, onmouseover and onmouseout
   events corresponding methods are called on their object instance.
*/
function DhtmlObject(elementId){
    /* A function is called that retrieves a reference to the DOM
       element (or null if it cannot be found) with the ID of the
       required element passed as its argument. The returned value
       is assigned to the local variable - el -:-
    */
    var el = getElementWithId(elementId);
    /* The value of - el - is internally type-converted to boolean for
       the - if - statement so that if it refers to an object the
       result will be true, and if it is null the result false. So that
       the following block is only executed if the - el - variable
       refers to a DOM element:-
    */
    if(el){
        /* To assign a function as the element's event handler this
           object calls the - associateObjWithEvent - function
           specifying itself (with the - this - keyword) as the object
           on which a method is to be called and providing the name of
           the method that is to be called. The - associateObjWithEvent
           - function will return a reference to an inner function that
           is assigned to the event handler of the DOM element. That
           inner function will call the required method on the
           javascript object when it is executed in response to
           events:-
        */
        el.onclick = associateObjWithEvent(this, "doOnClick");
        el.onmouseover = associateObjWithEvent(this, "doMouseOver");
        el.onmouseout = associateObjWithEvent(this, "doMouseOut");
        ...
    }
}
DhtmlObject.prototype.doOnClick = function(event, element){
    ... // doOnClick method body.
}
DhtmlObject.prototype.doMouseOver = function(event, element){
    ... // doMouseOver method body.
}
DhtmlObject.prototype.doMouseOut = function(event, element){
    ... // doMouseOut method body.
}
{% endhighlight %}

<p style="font-size: 14px">위 예제에서 DhtmlObject의 어떤 인스턴스도 필요한 DOM요소와 관계를 가질 수 있다. 게다가 이 인스턴스들은 어떻게 다른 코드에 사용되는지 알 필요가 없고, 전역 네임스페이스에 영향을 주거나, 다른 인스턴스와의 충돌도 발생하지 않는다.</p>
<p style="font-size: 14px"><strong>예제3: 관계된 함수의 캡슐화 ( Encapsulating Related Functionality )</strong></p>
<p style="font-size: 14px">클로저는 다른 관계된 혹은 종속적인 그룹들에 사용되는, 추가적인 스코프를 생성하는 데 사용될 수 있다.</p>
<p style="font-size: 14px">이 방법으로 갑작스런 충돌 위험을 최소화 할 수 있다.</p>
<p style="font-size: 14px">문자열을 생성하는 함수를 생각해 보자. 이 함수는 반복적인 문자열 붙이기를 피하고, 수많은 중간단계의 문자열 생성 역시 피하고자 한다. 하나의 배열을 만들어서 문자열의 각부분들을 순서대로 저장하고 Array.prototype.join 메소드를 사용하여 결과를 얻는 것이 목표이다. 이 배열은 결과물이 만들어지는 버퍼의 역할을 할 것이고함수에 지역 변수로 선언된다. 그리고 각각의 함수 실행마다 계속해서 결과값이 이 배열에 재할당될 것이다.</p>
<p style="font-size: 14px">한가지 접근 방법은 이 배열을 전역변수로 만들어서 재생성 없이 재사용할 수 있도록 하는 것이다. 하지만, 이렇게 하면 버퍼 배열을 사용할 함수를 참조하는 전역변수 뿐만 아니라, 또다른 전역 프로퍼티역시 그 배열을 참조하게 된다. 그 결과 코드는 관리하기 어려워지고, 특히, 다른곳에 사용된다고 가정하면, 작성자는 이 배열과 함수의 정의를 반드시 기억해야만한다. 그리고, 이런 코드는 다른 코드와 통합할 때 어려움을 줄 수 있다. 왜냐하면 함수의 이름과 배열의 이름이 전역 네임스페이스 안에서 충돌이 없는지를 항상 확인해야하기 때문이다.</p>
<p style="font-size: 14px">클로저는 버퍼 배열이 이것에 종속적인 함수와 관계를 가지는 것을 허용한다. 동시에 이 버퍼 배열이 할당된 프로퍼티 이름을 그대로 유지할 수 있다. 이는 전역 네임스페이스에서 이름의 충돌이나 상호작용의 충돌을 피할 수 있다.</p>
<p style="font-size: 14px">아래 예제에나오는 트릭은 다음과 같다. 인라인 함수 표현식의 실행을 통해 추가적으로 실행컨텍스트를 하나 만들고 여기서 외부에 사용될 내부함수를 리턴하는 것이다. 버퍼 배열은 인라인으로 실행되는 함수 표현식 안에 지역변수로 선언된다. 그러면 배열은 한번 생성되고 반복적인 사용이 가능한다.</p>
<p style="font-size: 14px">아래 코드에서 HTML 문자열(대부분이 "constant"인)을 반환하는 함수를 만든다. 이 문자열은 함수 호출 시 인자로 제공되는 값들이 중간중간에 배치되면서 최종 문자열을 만들게 된다.</p>
<p style="font-size: 14px">내부 함수에 대한 참조는 함수 표현식에 대한 인라인 실행을 통해 반환되고 전역변수에 할당되어 전역 함수에서 호출될 수 있게 된다. 버퍼 배열은 외부 함수 표현식에 지역변수로 선언된다. 이는 전역 네임스페이스에 노출되지 않음을 의미하고 함수가 호출될 때마다 재생성될 필요가 없다.</p>

{% highlight js %}
/* A global variable - getImgInPositionedDivHtml - is declared and
   assigned the value of an inner function expression returned from
   a one-time call to an outer function expression.

   That inner function returns a string of HTML that represents an
   absolutely positioned DIV wrapped round an IMG element, such that
   all of the variable attribute values are provided as parameters
   to the function call:-
*/
var getImgInPositionedDivHtml = (function(){
    /* The - buffAr - Array is assigned to a local variable of the
       outer function expression. It is only created once and that one
       instance of the array is available to the inner function so that
       it can be used on each execution of that inner function.

       Empty strings are used as placeholders for the date that is to
       be inserted into the Array by the inner function:-
    */
    var buffAr = [
        '<div id="',
        '',   //index 1, DIV ID attribute
        '" style="position:absolute;top:',
        '',   //index 3, DIV top position
        'px;left:',
        '',   //index 5, DIV left position
        'px;width:',
        '',   //index 7, DIV width
        'px;height:',
        '',   //index 9, DIV height
        'px;overflow:hidden;\"><img src=\"',
        '',   //index 11, IMG URL
        '\" width=\"',
        '',   //index 13, IMG width
        '\" height=\"',
        '',   //index 15, IMG height
        '\" alt=\"',
        '',   //index 17, IMG alt text
        '\"><\/div>'
    ];
    /* Return the inner function object that is the result of the
       evaluation of a function expression. It is this inner function
       object that will be executed on each call to -
       getImgInPositionedDivHtml( ... ) -:-
    */
    return (function(url, id, width, height, top, left, altText){
        /* Assign the various parameters to the corresponding
           locations in the buffer array:-
        */
        buffAr[1] = id;
        buffAr[3] = top;
        buffAr[5] = left;
        buffAr[13] = (buffAr[7] = width);
        buffAr[15] = (buffAr[9] = height);
        buffAr[11] = url;
        buffAr[17] = altText;
        /* Return the string created by joining each element in the
           array using an empty string (which is the same as just
           joining the elements together):-
        */
        return buffAr.join('');
    }); //:End of inner function expression.
})();
/*^^- :The inline execution of the outer function expression. */
{% endhighlight %}

<p style="font-size: 14px">하나의 함수가 다른 함수들에 종속적일 때, 다른 코드에 직접적으로 사용되길 원하지 않는다면 위와 같은 방법을 이용할 수 있다.
복잡하게 많은 함수를 만들지 말고, 간단하게 이식가능하고 캡슐화된 코딩을 하도록 하자.</p>
<p style="font-size: 14px">잘못된 클로저 사용
내부함수를 외부에서 접근가능하게 만들면, 클로저가 생성된다. 이처럼 클로저를 생성하는 것은 매우 쉽다. 하지만, 이 때문에, 자바스크립트 작성자(언어의 한 특성으로서 클로저에 감사하지 않는)는 정확한 결과를 모른채, 클로저가 만들어지는지도 모른채, 그리고 그것이 어떠한 영향을 미치는 지도 모른채, 다양한 일을 수행하는 내부함수를 사용하는 것을 볼 수 있다.</p>
<p style="font-size: 14px">잘못 만들어진 클로저는 좋지 않은 부작용을 낳을 수 있다. 아래에 설명할 IE의 메모리 누수가 좋은 예이다. 게다가 코드의 효율성에 좋지 않은 영향을 미친다. 클로저를 주의깊게 사용하면 효율적인 코드를 만드는데 중요한 영향을 미친다. 클로저 자체의 문제가 아니라, 내부함수의 사용을 어떻게 하느냐가 문제다.</p>
<p style="font-size: 14px">일반적인 상황중 하나가 내부함수를 DOM요소의 이벤트 핸들러로 사용하는 것이다.
아래 예제는 link 요소에 대한 onclick 이벤트의 핸들러를 추가하는 코드이다.</p>

{% highlight js %}
/* Define the global variable that is to have its value added to the
   - href - of a link as a query string by the following function:-
*/
var quantaty = 5;
/* When a link passed to this function (as the argument to the function
   call - linkRef -) an onclick event handler is added to the link that
   will add the value of a global variable - quantaty - to the - href -
   of that link as a query string, then return true so that the link
   will navigate to the resource specified by the - href - which will
   by then include the assigned query string:-
*/
function addGlobalQueryOnClick(linkRef){
    /* If the - linkRef - parameter can be type converted to true
       (which it will if it refers to an object):-
    */
    if(linkRef){
        /* Evaluate a function expression and assign a reference to the
           function object that is created by the evaluation of the
           function expression to the onclick handler of the link
           element:-
        */
        linkRef.onclick = function(){
            /* This inner function expression adds the query string to
               the - href - of the element to which it is attached as
               an event handler:-
            */
            this.href += ('?quantaty='+escape(quantaty));
            return true;
        };
    }
}
{% endhighlight %}

<p style="font-size: 14px">addGlobalQueryOnClick 함수를 호출할 때마다 새로운 내부 함수 및 클로저가 생성된다. 효율성의 관점에서 보았을 때 이 함수가 한두번 호출된다면 큰 문제가 되지 않지만, 다른 많은 함수에 의해 사용되게 되면 문제가 될 것이다.</p>
<p style="font-size: 14px">위 코드는 내부함수가 함수 외부에서 접근 가능하다는 사실의 잇점을 활용한 코드가 아니다. 이벤트 핸들러로서 사용될 함수를 개별적으로 정의하고 그 참조를 이벤트 핸들링 프로퍼티에 할당하면 위 코드와 정확히 같은 결과를 얻을 것이다. 게다가, 단 하나의 함수 객체가 만들어질 것이며, 이 이벤트 핸드러를 사용하는 모든 요소들은 하나의 함수 객체에 대한 참조를 공유할 것이다.</p>

{% highlight js %}
/* Define the global variable that is to have its value added to the
   - href - of a link as a query string by the following function:-
*/
var quantaty = 5;

/* When a link passed to this function (as the argument to the function
   call - linkRef -) an onclick event handler is added to the link that
   will add the value of a global variable - quantaty - to the - href -
   of that link as a query string, then return true so that the link
   will navigate to the resource specified by the - href - which will
   by then include the assigned query string:-
*/
function addGlobalQueryOnClick(linkRef){
    /* If the - linkRef - parameter can be type converted to true
       (which it will if it refers to an object):-
    */
    if(linkRef){
        /* Assign a reference to a global function to the event
           handling property of the link so that it becomes the
           element's event handler:-
        */
        linkRef.onclick = forAddQueryOnClick;
    }
}
/* A global function declaration for a function that is intended to act
   as an event handler for a link element, adding the value of a global
   variable to the - href - of an element as an event handler:-
*/
function forAddQueryOnClick(){
    this.href += ('?quantaty='+escape(quantaty));
    return true;
}
{% endhighlight %}

<p style="font-size: 14px">첫번째 코드의 내부함수는 클로저를 활용하기 위해 사용되지 않는다. 차라리 내부함수를 사용하지 않는 것이 더 효율적일 것이고, 함수 객체를 반복적으로 생성하지 않을 것이다.</p>
<p style="font-size: 14px">비슷한 예로 생성자 함수를 생각해 보자. 아래와 같은 스켈레톤 생성자 코드는 어렵지 않게 볼 수 있는 코드다.</p>

{% highlight js %}
function ExampleConst(param){
    /* Create methods of the object by evaluating function expressions
       and assigning references to the resulting function objects
       to the properties of the object being created:-
    */
    this.method1 = function(){
        ... // method body.
    };
    this.method2 = function(){
        ... // method body.
    };
    this.method3 = function(){
        ... // method body.
    };
    /* Assign the constructor's parameter to a property of the object:-
    */
    this.publicProp = param;
}
{% endhighlight %}

<p style="font-size: 14px">new ExampleConst(n) 을 통해 객체를 생성할 때마다 새로운 함수 객체의 set이 만들어 진다. 객체 인스턴스를 생성하면 할 수록 이와 함께 함수 객체도 많이 만들어질 것이다.</p>
<p style="font-size: 14px">이러한 경우 함수 객체를 한 번 만들고 이에 대한 참조를 생성자의 prototype의 프로퍼티에 할당하면 보다 효율적인 코드가 될 수 있다. 생성자에 의해 생성되는 모든 객체에 의해 이 함수들이 공유될 것이기 때문이다.</p>

{% highlight js %}
function ExampleConst(param){
    /* Assign the constructor's parameter to a property of the object:-
    */
    this.publicProp = param;
}
/* Create methods for the objects by evaluating function expressions
   and assigning references to the resulting function objects to the
   properties of the constructor's prototype:-
*/
ExampleConst.prototype.method1 = function(){
    ... // method body.
};
ExampleConst.prototype.method2 = function(){
    ... // method body.
};
ExampleConst.prototype.method3 = function(){
    ... // method body.
};
{% endhighlight %}

<p style="font-size: 18px"><strong>인터넷 익스플로러 메모리 누수 문제</strong></p>
<p style="font-size: 14px">인터넷 익스플로러 웹브라우저 (버전 4에서 6)는 가비지 컬렉션 시스템에서 결함을 가지고 있다. 호스트 객체들이 원형적으로 참조를 가지게 되면 가비지 컬렉션이 되지 않는다. 문제의 호스트 객체는 DOM 노드와 ActiveX 객체들이다. 원형 참조가 형성되면 관계된 모든 객체는 브라우저가 종료될 때까지 자원이 해제되지 않는다.</p>
<p style="font-size: 14px">원형 참조는 두개 이상의 객체가 서로를 참조하면 발생하게 된다. 객체 1이 객체 2를 참조하고 객체 2가 객체3을 참조하고, 객체 3이 다시 객체 1을 참조하게 되는 경우를 말한다. ECMAScript에서는 이것들도 가비지 컬렉션의 대상이 된다. 하지만 인터넷 익스플로러의 가비지 컬렉션 시스템은 DOM 노드나 activeX객체들에 이런 원형 참조가 형성되면 이를 알아차리지 못하고, 계속 메모리에 상주하게 된다.</p>
<p style="font-size: 14px">클로저는 원형참조가 쉽게 형성이 된다. 클로저를 만드는 함수 객체가 할당이 되면, 이를테면 DOM노드의 이벤트 핸들러로서 할당이 되면, 그 노드의 참조는 해당 스코프 체인의 활성화/변수 객체에 할당된다. 이렇게 되면 원형 참조가 형성되는 것이다. ( DOM_node.onevent -> function_obj.[[scope]] -> scope_chain -> Activation_obj.nodeRef -> DOM_node )
이는 매우 쉬운 일이며 대부분의 웹사이트에서 이러한 참조 형태가 나타나고, 결국 평범한 코드가 시스템 메모리의 대부분을 차지하게 되는 일이 발생한다.</p>
<p style="font-size: 14px">원형 참조가 형성되지 않도록 주의를 기울여야 하고, 피할 수 없는 경우 개선할 수 있는 방법이 취해져야 한다. 이를 테면, IE의 onunload 이벤트에 대한 이벤트 핸들링 프로퍼티를 null로 설정하는 것이 그것이다. 문제를 정확히 인식하고, 클로저가 무언인지를 정확히 이해하면 IE에서의 문제를 피할 수 있다.</p>

</div>
