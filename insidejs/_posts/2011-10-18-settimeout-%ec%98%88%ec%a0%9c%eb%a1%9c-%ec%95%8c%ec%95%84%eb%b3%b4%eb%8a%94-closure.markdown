---
layout: post
title: setTimeout 예제로 알아보는 closure
date: '2011-10-18 23:53:41'
---

아래 closure에 대한 설명은 이해하기가 쉽지는 않다.
( 그래서 조만간 그림을 통해 좀더 이해하기 쉬운 글을 포스팅하려고 계획중이다.)

일단, 쉬운 예제들을 통해 closure에 대해서 감을 잡아보도록 하자.

다음 코드를 보자.

[sourcecode language="javascript"]

function countSeconds(howMany) {
  for (var i = 1; i &lt;= howMany; i++) {
    setTimeout(function () {
      console.log(i);
    }, i * 1000);
  }
};
countSeconds(3);

[/sourcecode]


[sourcecode language="javascript"]

function countSeconds(howMany) {  
  setTimeout(function () {  console.log(1);  }, 1000);
  setTimeout(function () {  console.log(2);  }, 2000);
  setTimeout(function () {  console.log(3);  }, 3000);
};
countSeconds(3);

[/sourcecode]


과연 위 두개의 예제는 같은 결과를 출력할까? closure를 정확히 이해했다면, 위 두개의 예제의 결과는 다를 것이라는 것을 알 것이다.

두 번째 예제는 두말할 필요없이 다음과 같은 결과를 출력할 것이다. (각 라인당 1초의 딜레이를 두고)

zzoon@~/dev/JavaScript/closure $ node ./settime.js 
1
2
3
zzoon@~/dev/JavaScript/closure $

하지만 첫번째 예제는 조금 다르다.
(0) 외부 함수는 countSeconds 가 되고, 내부 함수는 setTimeout의 첫번째 인자로 들어가는 익명의 함수가 된다.
(1) 외부함수가 호출이 되면, i가 1씩 증가하면서 setTimeout을 호출한다. 3번을 돌고, i가 4가 되면 for-loop을 빠져나온다.
(2) 외부함수가 반환된다.
(3) 외부함수의 실행이 끝나고 반환이 되었지만, 내부함수는 setTimeout에 의해 여전히 참조되고 있기 때문에, closure가 형성된다.
(4) 주어진 시간이 지난 후, 내부 함수가 호출되고, 내부함수의 i는 closure 영역의 i를 참조하는데, 이 i는 이미 4가 되어 있다.
(5) 이렇게 내부 함수가 1초의 간격으로 세번 호출된다.

따라서 결과는 다음과 같다.

zzoon@~/dev/JavaScript/closure $ node ./settime.js 
4
4
4
zzoon@~/dev/JavaScript/closure $


사실 위 예제는 이런 결과를 바라고 코딩한 것은 아닐 것이다. 그렇다면 원하는 결과를 얻기 위해서는 어떻게 코드를 수정하는 것이 좋을까? 다음 코드를 보자.

[sourcecode language="javascript"]

function countSeconds(howMany) {
  for (var i = 1; i &lt;= howMany; i++) {
    (function () {
      var currentI = i;
      setTimeout(function () {
        console.log(currentI);
      }, currentI * 1000);
    }());
  }
};
countSeconds(3);

[/sourcecode]

setTimeout을 즉시 실행 함수(immediate executed function)으로 덮어씌웠다. 
이 함수 안에서 가장 먼저 하는 일은 지역 변수 currentI를 선언하고 i의 현재값으로 초기화하는 것이다.
그리고 이 지역변수를 내부함수가 사용하면 사용자가 원하는 결과가 출력된다.

보통 즉시 실행 함수는 주로 name conflict를 방지하기 위해 많이 사용되는데, 위 코드는 여러가지 비슷하지만 다른 형태의 코드로 바꿀 수 있다.

다음 코드는 어떠한가?

[sourcecode language="javascript"]

function countSeconds(howMany) {
  for (var i = 1; i &lt;= howMany; i++) {
    // The IEF remembers the current value of i using a function parameter
    (function (currentI) {
      setTimeout(function () {
        console.log(currentI);
      }, currentI * 1000);
    }(i));
  }
};
countSeconds(3);

[/sourcecode]

이번에는 즉시실행함수에 인자 i를 넘겨주었다. 내부함수는 넘겨받은 인자 currentI를 참조하게 되고,  원하는 결과가 출력될 것이다.

그런데, 위 예제에서 인자의 이름을 똑같이 i로 한다면?

[sourcecode language="javascript"]

function countSeconds(howMany) {
  for (var i = 1; i &lt;= howMany; i++) {
    // The IEF remembers the current value of i using a function parameter
    (function (i) {
      setTimeout(function () {
        console.log(i);
      }, i * 1000);
    }(i));
  }
};
countSeconds(3);

[/sourcecode]

결론부터 얘기하면, 위 예제는 아무런 문제가 없다.
스코프 체인을 생각해보자.

(0) 외부함수 실행 ( countSeconds(3); )
 -&gt; 외부함수의 실행컨텍스트가 생성된다.  이 실행컨텍스트에서 생성되는 활성화/변수 객체를 OBJ_1 이라 하자. 
     스코프 체인은 [ OBJ_1 -&gt; 전역 ]
 
(1) 즉시 실행 함수 실행 
 -&gt; 즉시 실행 함수의 새로운 실행 컨텍스트가 생성된다. 이 실행 컨텍스트에서 생성되는 활성화/변수 객체를 OBJ_2 이라 하자.
      여기서 외부 함수객체의 i가 인자로 넘겨지는데, 이 i의 값은 IEF_1의 변수 i에 할당된다.
      스코프 체인은 [ OBJ_2 -&gt; OBJ_1 -&gt; 전역 ]
 
(2) setTimeout 실행

(3) for-loop을 마치고, 외부함수가 리턴된다.

(4) setTimeout을 통해 내부 함수가 호출되면서 새로운 실행 컨텍스트가 형성된다. 여기서의 활성화/변수 객체를 OBJ_3 이라고 하자. 그러면 이 내부함수 객체가 가지는 스코프 체인은 다음과 같다.

OBJ_3 -&gt; OBJ_2 -&gt; OBJ_1 -&gt; 전역

내부함수가 실행되면서 i를 참조하려고 할 때, OBJ_3에는 없기 때문에 결국 OBJ_2의 i에서 값을 읽어올 수 있다.
그 결과, 이전의 예제와 같은 결과를 출력하게 된다.





