---
layout: post
title: '[번역]Object Graph를 통해서 JavaScript 배우기 (Part I)'
date: '2011-03-22 06:09:56'
---

원본글 :<a href="http://howtonode.org/object-graphs"> http://howtonode.org/object-graphs</a>

최고의 JavaScript 개발자가 되는 비밀중에 한가지는 언어의 semantic을 이해하는 것이다. 이번 article은 많은 그림들을 활용해서 JavaScript의 기본적인 부분들을 설명하려고 한다.
<h2 style="border-bottom-width: 2px; border-bottom-style: dotted; border-bottom-color: #AAA;">References Everywhere</h2>
JavaScript에서 변수는 메모리 어딘가의 값을 가리키는 단순한 라벨일 뿐이다. 이 값들은 프리미티브 형태(strings, numbers, booleans)를 가질 수 있다. 물론 오브젝트 나 function이 될 수 있다.
<h3>Local Variables</h3>
아래의 예제에서처럼, top-level scope에서 4개의 로컬 변수를 만들 것이고 그것들은 어떤 프리미티브 값들을 가르킬 것이다.

[sourcecode language="javascript"]
// Let's create some local variables in the top scope
var name = &quot;Tim Caswell&quot;;
var age = 28;
var isProgrammer = true;
var likesJavaScript = true;
// Test to see if the two variables reference the same value
isProgrammer === likesJavaScript;

// output ==&gt; true
[/sourcecode]

<img class="alignleft" src="http://howtonode.org/object-graphs/variables.dot" alt="" width="323" height="203" />

두개의 Boolean 변수들은 메모리에서 값은 값을 가르키고 있다. 프리미티브 값들은 변경되지 않고 VM에서 최적화를 해서 모든 레퍼런스들이 특정 값을 참조하게해  하나의 인스턴스를 공유하게 한다.

두개가 같은 값을 참조하는지 체크하기 위해서 === 를 사용해서 테스트 하였고 결과는 true 였다.

박스 밖은 가장 바깥쪽 closure 영역이다. 이 변수는 탑레벨의 local 변수이고 Global/Window 오브젝트 속성과 혼돈하면 안된다.
<h3>Objects and Prototype Chains</h3>
Object는 새로운 Object와 prototype을 참조하는 모음이다. 특별한 것은 추가된 기능은 Prototype chain이라는 것이고 이것은  property에 접근하려고할때 로컬 Object에 없다면 Object에 있는 property를 접근가능하게 해준다.

[sourcecode language="javascript"]
// Create a parent object
var tim = {
   name: &quot;Tim Caswell&quot;,
   age: 28,
   isProgrammer: true,
   likesJavaScript: true
}
// Create a child object
var jack = Object.create(tim);
// Override some properties locally
jack.name = &quot;Jack Caswell&quot;;
jack.age = 4;
// Look up stuff through the prototype chain
jack.likesJavaScript;

// Output ==&gt; true
[/sourcecode]

<img class="aligncenter" src="http://howtonode.org/object-graphs/objects.dot" alt="" width="611" height="337" />

<code>tim</code> 변수에 의해서 참조되며 4개의 property를 가지고있는 Object가 있다. 또한 새로운 Object를 생성하는데 그것은 첫 Object를 상속하고 있으며 <code>jack</code>에 참조된다. 그 다음에 우리는 두 property를 로컬 Object에서 overrode 한다.
이제 <code>jack.likesJavaScript</code>를 보면, 우리는 <code>jack</code>을 참조하고 있는 object를 찾는다. 그러면 우리는 <code>likesJavaScript</code>의 property를 보게 된다. 이것이 없기 때문에 우리는 부모 object를 보게되고 그곳에서 찾게 된다. 그리고 우리는 이것이 참조하고 있는 <code>true</code>를 발견한다.
<h3>The Global Object</h3>
<a href="http://jslint.com/">jslint</a> 같은 툴들은 변수 앞에 var를 사용하라고 이야기한다. var를 사용하지 않을 경우는 아래와 같다.

[sourcecode language="javascript"]
var name = &quot;Tim Caswell&quot;;
var age = 28;
var isProgrammer = true;
// Oops we forgot a var
likesJavaScript = true;
[/sourcecode]

<img class="alignleft" src="http://howtonode.org/object-graphs/globals.dot" alt="" width="440" height="246" />

<code>likesJavaScript</code> 는 밖의 closure에 있는 자유 변수가 아니라 global Object의 property이다. 이것은 실제로 몇몇의 script를 섞어서 사용할 때 문제가 된다. 그렇지만 실제 프로그램에서는 하게 되는 일일 것이다.

저곳에 저런 <code>var</code> 문구를 사용할때는 자신의 변수의 영역을 지금의 closure와 children으로 부터 지켜야한다는 것을 기억해야한다. 이 간단한 규칙을 따르는 것으로 매우 행복해질 것이다.

만약에 global Object에 무언가 올리고 싶다면 브라우저에서는 <code>window.woo</code> 이고 node.js에서는 <code>global.goo</code>와 같이 명시적으로 적어야한다.
<h2 style="border-bottom-width: 2px; border-bottom-style: dotted; border-bottom-color: #AAA;">Functions and Closures</h2>
Javascript는 단지 자료구조 연결들의 모음이 아니다. 이것은 executable, callable code(function으로 알려진)와 같은 것들을 포함하고 있다. 이런 function은 chained scope와 clousure를 생성한다.
<h3>Visualizing Closures</h3>
Function은 executable code 뿐만 아니라 property를 가지고있는 특별한 Object로 그려진다. 모든 Function은 이것이 정의될때의 환경을 나타내는 특별한 <code>[scope]</code> property를 가지고있다. 만약에 function이 다른 function으로 부터 반환 된다면 이것은 옛날의 환경은 Closure에 의해서 닫히게 된다.

이번 예제에서 closure를 생성하고 function을 반환하는 간단한 factory 메소드를 만들도록 하겠다.

[sourcecode language="javascript"]
function makeClosure(name) {
   return function () {
      return name;
   };
}

var description1 = makeClosure(&quot;Cloe the Closure&quot;);
var description2 = makeClosure(&quot;Albert the Awesome&quot;);
console.log(description1());
console.log(description2());

// Output
// Cloe the Closure
// Albert the Awesome
[/sourcecode]

<img class="aligncenter" src="http://howtonode.org/object-graphs/closure.dot" alt="" width="768" height="409" />

<code>description1()</code>을 호출할 때, VM은 참고하고 있는 곳이나 실행하고 있는 곳에서 function을 찾는다. funtion이 로컬 변수인 <code>name</code>를 찾고 있기 때문에 closure 영역에서 찾게 된다.이 factory 메소드는 각각 만들어진 function이 자신의 로컬 변수영역을 가지고 있기 때문에 잘 만들어진 것이다.

보다 자세한 내용이 알고싶으면 다음 문서를 보면 <a href="http://howtonode.org/why-use-closure">why use closure</a> 된다.
<h3>Shared Functions and this</h3>
퍼포먼스의 이유 혹은 선호하는 스타일 때문에 JavaScript는 <code>this</code>라는 키워드를 제공하고 function Object를 다른 scope에서 불리는 방법에 따라 재사용하기도 한다. 공통 function을 공유하는 몇가지 Object를 생성한다. 이 function은 <code>this</code>를 참조하고 내부적으로 호출마다 어떻게 바뀌는 지를 보여준다.

[sourcecode language="javascript"]
var Lane = {
   name: &quot;Lane the Lambda&quot;,
   description: function () {
      return this.name;
   }
};

var description = Lane.description;
var Fred = {
   description: Lane.description,
   name: &quot;Fred the Functor&quot;
};
// Call the function from four different scopes
console.log(Lane.description());
console.log(Fred.description());
console.log(description());
console.log(description.call({
   name: &quot;Zed the Zetabyte&quot;
}));

// Output
// Lane the Lambda
// Fred the Functor
// undefined
// Zed the Zetabyte
[/sourcecode]

<img class="aligncenter" src="http://howtonode.org/object-graphs/functions.dot" alt="" width="544" height="393" />

다이어그램에서는 <code>Fred.description</code>이 <code>Lande.description</code>으로 설정되었지만 이것은 단지 function을 참조하는 것 뿐이다. 그래서 3개의 참조는 anonymous 함수에 똑같은 소유권을 가지고 있다. 이런 이유 때문에 나는 컨스트럭터의 프로토 타입 형태의 메소드인 경우 function을 부르려고 하지 않는다. 왜냐면 어떤 종류의 함수의 바인딩과 클래스를 의미 하기 때문이다.(무슨 소리일까 -_-;;)(<a href="http://howtonode.org/what-is-this">what is this</a>에 dynamic nature of <code>this</code>에 대한 자센한 이야기를 보면 된다.)
<h2 style="border-bottom-width: 2px; border-bottom-style: dotted; border-bottom-color: #AAA;">Conclusion</h2>
다이어그램을 사용해서 자료구조를 시각화 하는게 정말 재미있었다. 내 바램은 이 문서가 JavaScript Semantic을 이해하는데 도움이 되었으면 하는 것이다. 나는 과거에 프론트 엔드의 설계와 개발 경력 그리고 서버쪽 구조 설계 경력을 가지고 있다. 나의 독특한 관점이 JavaScript언어를 이해하는데 도움이 되었으면 좋겠다.
(NOTE, all the diagrams are graphviz dot files and can be seen here)