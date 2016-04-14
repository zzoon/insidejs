---
layout: post
title: '[번역] javascript 성능향상을 위한 10가지 팁 '
date: '2011-10-30 20:36:51'
---

http://jonraasch.com/blog/10-javascript-performance-boosting-tips-from-nicholas-zakas

<strong>1. 지역변수를 정의하라.</strong>
변수가 참조될 때, 자바스크립트는 스코프 체인을 돌면서 찾는다. 이 스코프 체인은 현재 스코프에서 접근 가능한 변수 객체에 대한 집합이다. 그리고 적어도 두개이상의 요소를 가지는데, 지역 변수의 집합과 전역 변수의 집합이다.

간단히 말해서, 엔진이 스코프체인을 따라 들어가야하면 할수록, 실행시간은 더 걸릴 것이다. this부터 시작해서, 인자, 지역 변수 그리고 나서, 전역변수를 돌 것이다.

체인의 앞에 지역변수들이 있기 때문에, 언제나 전역 변수보단 빠를 수 밖에 없다. 한번 이상 전역변수를 사용해야 한다면 지역변수를 재정의하는 것이 좋다.

예를 들면,
{% highlight js %}
var blah = document.getElementById('myID'),
blah2 = document.getElementById('myID2');
{% endhighlight %}

이 코드를,
{% highlight js %}
var doc = document,
blah = doc.getElementById('myID'),
blah2 = doc.getElementById('myID2');
{% endhighlight %}

이렇게 바꿀 수 있다.

<strong>2. with() 구문을 사용하지 말라.</strong>
다음은 명백한 사실이다 : with() 구문은 자바스크립트의 진짜 악마(pure javascript evil)다.

왜냐하면, with()는 스코프체인의 제일 앞에 새로운 변수들의 집합을 붙이기 때문이다. 이것을 하는 이유는 언제,어디서든지 해당 변수를 호출하기 위함인데, 엔진은 이 변수들을 먼저 돌아본 후, 지역변수, 전역변수 순서로 찾아야 한다.

따라서, with()는 지역변수 역시, 전역변수가 가지는 성능상의 결점을 그대로 가지게 만든다. 이는 자바스크립트 최적화에 걸림돌이다.

<strong>3. 클로저를 적절하게 사용하라</strong>
클로저는 매우 강력하고 유용한 자바스크립트의 특징인데, 성능상의 결점을 가지고 있다.
여러분이 클로저에 대해 알든 모르든, 이것을 종종 사용하곤 할 것이다. 클로저는 기본적으로 자바스크립트에서의 "new"로 인식될 수도 있는데, 우리는 함수를 대충 정의할 때 이것을 사용하게 된다.

예를 들면, 다음과 같다.
{% highlight js %}
document.getElementById('foo').onclick = function(ev) { };
{% endhighlight %}

클로저의 문제는 이렇게 함수를 정의함으로써, 스코프체인에 최소 세개의 객체가 들어가게 만든다. 클로져 변수, 지역변수, 전역변수가 그것이다. 이것 역시 우리가 1, 2 에서 피하려고 했던 성능문제를 야기하게 된다.

하지만, 니콜라스는 우리가 이것을 완전히 버리기를 바라진 않을 것이다. 클로저는 편리하고, 코드를 좀더 읽기 쉽게 해주는 등 여전히 유용한 측면이 있기 때문이다. 단지 너무 과용하지는 말도록 하자.(특히 루프 안에서는 쓰지말자.)

<strong>4. 객체의 프로퍼티와 배열의 요소는 변수보다 느리다.</strong>

자바스크립트 데이타에 접근하기 위한 4가지 방법이 존재하는데, 기본값(literal value), 변수, 객체 프로퍼티, 배열의 요소가 그것이다. 최적화의 관점에서 보면 기본값과 변수는 성능상 동일하고, 객체 프로퍼티와 배열보다는 훨씬 빠르다.

따라서, 객체의 프로퍼티나 배열의 요소를 여러번 참조할 일이 생기면, 변수를 정의하는게 성능향상에 도움이 된다. 이는 읽기 뿐만아니라, 쓰기에도 적용된다.

이 룰은 사실이라고 할 수 있지만, 파이어폭스에서 배열의 인덱스를 최적화하는 재미있는 것들을 해놓았다. (<a href="http://www.webreference.com/programming/javascript/ncz/column4/" target="_blank">http://www.webreference.com/programming/javascript/ncz/column4/</a>) 실제로 배열로 하는 것이 변수보다 성능이 좋다. 하지만 이외의 브라우저에서는 배열이 성능상의 결점을 가지고 있기 때문에, 배열에서 무언가를 찾는 것은 지양해야 한다. 당신이 정말 파이어폭스의 성능만을 목표로 잡지 않는다면 말이다.

<strong>5. 배열속으로 너무 깊이 들어가지 말라</strong>
또한 배열속으로 깊이 들어가서 작업을 하는 것을 피해야 한다. 깊이 들어가면 들어갈 수록 속도는 느려진다.

간단히 말해서, 깊이 들어간다는 것은 배열의 요소를 찾는 것이 더 느려지는 것을 의미하기 때문이다.
생각해보자. 배열의 세번째 요소를 찾는 것은 한번이 아닌 세번의 찾기가 이루어지는 것이다.

따라서 foo.bar를 매번 참조하고 있다면, var = foo.bar; 로 변수를 정의하는 것이 성능향상에 도움이 된다.

<strong>6. for-in 루프를 피하자 ( 그리고 함수호출에 기반한 반복도..)</strong>

for-in 루프를 사용한 로직은 매우 간단하다. for나 do-while을 가지고 인덱스를 통해 루프를 도는 것 대신, for-in을 사용하면 추가적인 배열의 요소들을 돌아야하고, 더 많은 노력을 요구한다.

배열의 요소들을 돌기 위해서 자바스크립트는 각각에 대해 함수 객체를 생성해야 한다. 이러한 함수호출에 기반한 반복은 수많은 성능 이슈를 야기한다. 각 함수에 대응되는 실행 컨텍스트가 만들어졌다 사라지고, 스코프 체인에는 추가적인 객체가 제일 앞에 들어가게 된다.

<strong>7. 루프를 사용할 때 제어 조건과 제어 변수 변화를 결합하라</strong>
성능에 관해 논할때, 루프안에서의 작업을 줄이는 것은 언제나 뜨거운 주제이다. 왜냐하면, 매우 간단한 얘기지만, 루프는 반복해서 계속 실행되기 때문이다. 그렇기 때문에 루프에서 약간의 성능 향상이라도 얻는다면, 그것은 전체적으로 커다란 성능의 향상을 볼 수 있을 것이다.

이러한 방법중에 하나는 루프를 정의할 때, 제어 조건과 제어 변수를 결합하는 것이다

다음 코드는 결합이 이루어지지 않은 코드다.
{% highlight js %}
for ( var x = 0; x < 10; x++ ) {
};
{% endhighlight %}

여기에 어떤 코드를 추가하기 전에도, 매 반복마다 몇가지 작업이 일어난다. 자바 스크립트 엔진은 (1) x가 있는지 확인하고, (2) x < 10인지 확인하고, (3) x++를 실행한다.

그러나, 단순히 배열의 요소들을 반복하고 있는 것이라면, 이 작업들 중 하나는 다음과 같이 자를 수 있다.

{% highlight js %}
var x = 9;
do { } while( x-- );
{% endhighlight %}

<strong>8. HTML 공통 객체(collection objects)를 사용하기 위한 배열을 만들어라.</strong>
자바스크립트는 document.forms, document.images 같은 수많은 HTML 공통 객체들을 사용한다. 게다가 이들은 getElementsByTagName나 getElementsByClassName 같은 메소드를 통해 호출된다.

어떤 DOM을 선택하든 HTML 공통 객체는 대단히 느리다. 게다가 또다른 문제가 있다. DOM 레벨1 스펙에서는 "HTML DOM의 공통 객체들은 live해야 한다. 즉, 문서가 변경되었을 경우, 자동으로 업데이트되어야 한다."

이건 정말 끔찍하다.

공통 객체들은 배열처럼 생겼지만, 특정 쿼리의 결과라는 점에서 상당히 다르다. 이 객체가 읽기 혹은 쓰기를 위해 접근될 때, 이 쿼리는 매번 수행되어야 한다. 이때, length와 같은 해당 객체의 주변 요소들에 대해서도 업데이트가 되어야만 한다.

HTML 공통 객체는 정말 너무너무 느리다. 니콜라스는 예상치가 작은 동작에서도 60배는 느리다고 언급한 바 있다. 게다가 이 공통 객체들은 의도치않게 무한루프가 발생하게 할 수도 있다.

{% highlight js %}
var divs = document.getElementsByTagName('div');

for (var i=0l i < divs.length; i++ ) {
    var div = document.createElement("div");
    document.appendChild(div);
}
{% endhighlight %}

위 코드는 무한 루프를 유발시킨다. 왜냐하면, divs가 당신이 예상한 배열이 아닌, HTML 공통객체를 나타내기 때문이다. 이 살아있는(live) 공통 객체는 새로운 div가 DOM에 추가될 때마다 업데이트 된다. 그래서 i < divs.length는 결코 끝나지 않는다.

이를 피하기 위해서는 배열을 선언해야 한다. 물론 var divs = document.getElementsByTagName('div'); 보다 조금 더 복잡하긴 하다.

{% highlight js %}
var divs = document.getElementsByTagName('div');

function array(items) {
    try {
        return Array.prototype.concat.call(items);
    } catch (ex) {

        var i       = 0,
            len     = items.length,
            result  = Array(len);

        while (i < len) {
            result[i] = items[i];
            i++;
        }

        return result;
    }
}

var divs = array( document.getElementsByTagName('div') );

for (var i=0l i < divs.length; i++ ) {
    var div = document.createElement("div");
    document.appendChild(div);
}
{% endhighlight %}

<strong>9. 웬만하면 DOM을 건드리지 마라!</strong>
DOM을 그대로 놔두는 것은 자바스크립트 최적화에 있어서 커다란 주제이다. 배열의 각 아이템을 추가한다고 가정하자. 만약 아이템 하나하나를 개별적으로 DOM에 추가한다면 그것은 한번에 추가하는 것보다 분명히 느릴 것이다. DOM 관련 작업은 기본적으로 비싸기 때문이다.

DOM 작업이 자원이 많이드는 큰 작업인 이유는 reflow 때문이다. reflow란 브라우저가 스크린에 DOM요소들을 다시 렌더링하는 기본적인 과정을 말한다. 예를 들어 당신이 자바스크립트로 div의 width를 변경하면 브라우저는 이 변경을 반영하여 페이지를 새롭게 렌더링해야 한다.

DOM 요소가 추가되거나 제거될 때도 reflow는 발생할 것이다. 이 부분에 대한 유용한 솔루션은 documentFragment라는 객체를 사용하는 것이다.

documentFragment는 기본적으로 브라우저에 의해 화면상으로 표시되지 않는 document-like한 fragment다. 화면상으로 표시되지 않는 것은 많은 잇점을 가져다 준다. 브라우저가 reflow하지 않게 documentFragment에 노드를 추가할 수 있다.

<strong>10. CSS 클래스를 변경하라. 스타일 말고!</strong>
CSS 스타일을 변경하는 것보다는 CSS 클래스를 변경하는 것이 더 낫다는 말을 어디선가 들었을 것이다. 이렇게 하면, reflow 이슈가 줄어든다. style이 변경될 때마다, reflow가 발생한다.

레이아웃 스타일은 레이아웃에 영향을 주므로, reflow를 유발시킨다. 예를 들면, width, height, font-size, float 같은 것들 말이다.

하지만 오해는 하지말자. CSS 클래스도 reflow를 피할 수는 없다. 다만, 그것을 최소화하는 것이다. 스타일을 바꿀때마다 reflow를 발생시키지 말고, 클래스를 바꿈으로써 단 한번의 reflow만을 발생시키도록 하자.

따라서, 하나의 스타일에 여러 개의 CSS 클래스 이름을 사용하는 것이 좋다. 또한, 많은 클래스를 정의할 때, <a href="http://jonraasch.com/blog/javascript-style-node" target="_blank">style node를 DOM에 추가</a>하는 것이 좋다.
