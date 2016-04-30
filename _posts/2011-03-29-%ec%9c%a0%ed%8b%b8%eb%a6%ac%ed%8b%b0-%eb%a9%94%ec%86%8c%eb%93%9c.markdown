---
layout: post
title: 유틸리티 메소드
date: '2011-03-29 23:08:57'
---

[ 본문은 jquery fundamentals 문서에서 발췌, 번역한 것이다. ( http://jqfundamentals.com/book/index.html ) ]

jQuery는 몇가지 유틸리티 메소드를 $ namespace에서 제공한다. 이 메소드들은 반복적인 프로그래밍 작업을 수행하는 데 도움이 된다. 아래 이 몇가지 메소드들에 대한 예제들이 있다. ( 이 메소드들에 대한 레퍼런스를 보려면 다음 사이트를 방문하도록 하자 <a href="http://api.jquery.com/category/utilities/">http://api.jquery.com/category/utilities/</a>. )

<strong>$.trim</strong>

<strong>공백을 제거한다.</strong>

{% highlight js %}
$.trim('    lots of extra whitespace    ');
// returns 'lots of extra whitespace'
{% endhighlight %}

<strong>$.each</strong>

<strong>배열과 객체를 순환해서 접근한다.</strong>

{% highlight js %}

$.each([ 'foo', 'bar', 'baz' ], function(idx, val) {
    console.log('element ' + idx + 'is ' + val);
});

$.each({ foo : 'bar', baz : 'bim' }, function(k, v) {
    console.log(k + ' : ' + v);
});
{% endhighlight %}

<strong><주의> $.fn.each 메소드도 존재한다. 이 메소드는 selection의 각 요소들을 순환해서 접근한다.</strong>

<strong>$.inArray </strong>

<strong>배열 안에서 해당값의 인덱스를 리턴하고, 없으면 -1을 리턴한다.</strong>

{% highlight js %}
var myArray = [ 1, 2, 3, 5 ];

if ($.inArray(4, myArray) !== -1) {
    console.log('found it!');
}
{% endhighlight %}

<strong>$.extend</strong>
<strong> 첫번째 객체의 프로퍼티를 이후의 오브젝트들의 프로퍼티를 이용하여 바꾼다.</strong>

{% highlight js %}
var firstObject = { foo : 'bar', a : 'b' };
var secondObject = { foo : 'baz' };

var newObject = $.extend(firstObject, secondObject);
console.log(firstObject.foo); // 'baz'
console.log(newObject.foo);   // 'baz'
{% endhighlight %}

만약 당신이 인자로 넘기는 객체가 변하지 않기를 바란다면 첫번째 인자를 빈 객체를 넘기면 된다.

{% highlight js %}
var firstObject = { foo : 'bar', a : 'b' };
var secondObject = { foo : 'baz' };

var newObject = $.extend({}, firstObject, secondObject);
console.log(firstObject.foo); // 'bar'
console.log(newObject.foo);   // 'baz'
{% endhighlight %}

<strong>$.proxy </strong>

<strong>사용자에 의해 제공된 스코프안에서 실행될 함수 객체를 리턴한다. 즉, 두번째 인자로 넘겨지는 함수안의 this를 세팅하는 것과 같다.</strong>

{% highlight js %}
var myFunction = function() { console.log(this); };
var myObject = { foo : 'bar' };

myFunction(); // logs window object

var myProxyFunction = $.proxy(myFunction, myObject);
myProxyFunction(); // logs myObject object
{% endhighlight %}

만약 메소드를 정의한 객체가 있다면, 그 객체와 메소드의 이름을 넘기면 해당 객체의 스코프안에서 실행될 함수 객체가 리턴된다.

{% highlight js %}
var myObject = {
    myFn : function() {
        console.log(this);
    }
};

$('#foo').click(myObject.myFn); // logs DOM element #foo
$('#foo').click($.proxy(myObject, 'myFn')); // logs myObject
{% endhighlight %}
