---
layout: post
title: 자바 스크립트 클래스를 정의하는 세가지 방법
date: '2011-11-13 22:29:01'
---

원문 : 3 ways to define a JavaScript class
<a href="http://www.phpied.com/3-ways-to-define-a-javascript-class/">http://www.phpied.com/3-ways-to-define-a-javascript-class/</a>

<strong>소개 </strong>
자바스크립트는 문법적인 측면에서 봤을 때, 매우 유연한 객체지향언어이다. 이번 글에서 여러분은 객체를 정의하고 생성하는 세 가지 방법에 대해서 알 수 있을 것이다. 이미 여러분이 자신만의 방법을 갖고 있다고 할지라도 다른 사람의 코드를 읽기 위해서라도 다른 방법들에 대해 알아두는 것이 좋다.
자바 스크립트에서는 클래스가 없다. 함수가 클래스로서 사용될 수 있지만, 일반적으로 자바스크립트는 클래스가 없는(class-less) 언어이다. 자바스크립트에서는 모든 것이 객체이다. 상속의 측면에서 전통적인 언어와는 달리 클래스가 아니라, 객체는 객체로부터 상속을 받는다. 


<strong>1. 함수 사용 </strong>
이 방법은 가장 일반적인 방법중 하나일 것이다. 일반적인 자바스크립트 함수를 정의하고나서 new 키워드를 사용하여 객체를 생성한다. 프로퍼티와 메소드를 정의하기 위해서 다음 예제에서 보는 것처럼 this를 사용하면 된다.


{% highlight js %}
function Apple (type) {
    this.type = type;
    this.color = "red";
    this.getInfo = getAppleInfo;
}

// anti-pattern! keep reading...
function getAppleInfo() {
    return this.color + ' ' + this.type + ' apple';
}
{% endhighlight %}

다음 예제처럼, Apple 생성자를 사용하여 객체를 생성하고 프로퍼티를 설정하고 메소드를 호출할 수 있다. 

{% highlight js %}
var apple = new Apple('macintosh');
apple.color = "reddish";
alert(apple.getInfo());
{% endhighlight %}

<strong>1.1 내부적으로 정의된 메소드</strong>
위 예제에서는 Apple "클래스"의 getInfo() 메소드는 별개의 getAppleInfo() 함수로 정의되었다. 이는 잘 동작하는 코드이지만 결점을 가지고 있다. 이런 식으로 함수 다수를 정의하게 되면, 그것들은 모두 전역 네임 스페이스에 들어가게 되는데, 이는 이름 충돌이 일어날 수도 있다. 전역 네임스페이스의 오염을 방지하려면, 메소드를 아래와 같이 생성자 내부에 정의해야 한다.

{% highlight js %}
function Apple (type) {
    this.type = type;
    this.color = "red";
    this.getInfo = function() {
        return this.color + ' ' + this.type + ' apple';
    };
}
{% endhighlight %}

이렇게 하면, 문법적으로 아무런 변화 없이 프로퍼티와 메소드를 사용할 수 있다. 

<strong>1.2 프로토타입에 추가된 메소드
</strong>
1.1의 결점은 getInfo() 메소드가 새로운 객체를 생성할 때마다 매번 다시 만들어진다는 것이다. 때로는 이것이 개발자가 원하는 것일 수도 있지만, 이는 드문 일이다. 이보다 더 가벼운 방식은 getInfo()메소드를 생성자의 프로토타입에 추가하는 것이다.

{% highlight js %}
function Apple (type) {
    this.type = type;
    this.color = "red";
}

Apple.prototype.getInfo = function() {
    return this.color + ' ' + this.type + ' apple';
};
{% endhighlight %}

다시 한번 말하지만, 여러분은 1 과 1.1 에서와 똑같이 새 객체를 사용할 수 있다. 

<strong>2. 객체 기본문법(literals) 사용</strong>

기본문법을 사용하면 자바스크립트에서 객체나 배열을 더 간단하게 정의할 수 있다. 빈 객체를 하나 생성하기 위해서는 다음과 같이 하면 된다.

객체를 만드는 "일반적인" 방법인 아래 방법 대신에,
{% highlight js %}
var o = new Object();
{% endhighlight %}

아래와 같이 할 수 있다.
{% highlight js %}
 var o = {};
{% endhighlight %}

빈 배열은 아래와 같은 방법 대신에,
{% highlight js %}
var a = new Array();
{% endhighlight %}

아래와 같이 할 수 있다.
{% highlight js %}
var a = [];
{% endhighlight %}
 
이처럼 클래스 만드는 방식과 비슷한 일을 하지 않고, 객체를 바로 생성할 수 있다. 아래 예제는 위 예제와 같은 기능을 하는데, 단지 객체 기본 문법만을 사용했다. 

{% highlight js %}
var apple = {
    type: "macintosh",
    color: "red",
    getInfo: function () {
        return this.color + ' ' + this.type + ' apple';
    }
}
{% endhighlight %}

이 경우는 클래스의 인스턴스를 만들 필요가 없고, 만들 수도 없다. 이미 존재하기 때문이다. 따라서, 단순이 이 인스턴스를 아래와 같이 사용하기만 하면 된다.
 
{% highlight js %}
apple.color = "reddish";
alert(apple.getInfo());
{% endhighlight %}

이러한 객체를 때로는 싱글톤이라고 불리우기도 한다. 자바와 같은 전통적인 언어에서는 항상 해당 클래스의 인스턴스를 하나만 가질 수 있고 더 이상의 객체를 만들 수 없다. 하지만, 자바스크립트에서 객체는 이미 시작할 때 부터 싱글톤이기 때문에, 이러한 개념은 의미가 없다.


<strong>3. 함수를 이용한 싱글톤</strong>
세 번째 방법은 위 두가지 방법을 합친 것이다. 싱글톤 객체를 정의하기 위해 함수를 사용하는 것이다.
 
{% highlight js %}
var apple = new function() {
    this.type = "macintosh";
    this.color = "red";
    this.getInfo = function () {
        return this.color + ' ' + this.type + ' apple';
    };
}
{% endhighlight %}

위 예제는 1.1과 매우 유사하다. 그러나 객체를 사용하는 방법에 관해서는 2와 같다.
 
{% highlight js %}
apple.color = "reddish";
alert(apple.getInfo());
{% endhighlight %}

new function(){} 은 동시에 두가지 일을 하는데, 하나는 함수를 정의하는 것이고, 다른 하나는 new를 가지고 생성하는 것이다.   이에 익숙하지않다면, 조금 혼란스러울 수도 있다. 하지만 이것은 선택 사항일 뿐이다.
