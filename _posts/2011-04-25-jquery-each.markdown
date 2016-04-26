---
layout: post
title: jQuery.each() 메서드
date: '2011-04-25 00:04:22'
---

이번 글에서는 jQuery 소스에서 자주 사용되는 jQuery.each() 메서드에 대해서 알아보자.

이 메서드의 원형은 다음과 같다.
이 메서드는 1번째 인자로 전달되는 배열이나 length 프로퍼티를 가진 배열형 객체의 0에서 n-1번째 인덱스를 순환하며 2번째 인자로 전달되는 콜백 함수를 실행시키는 역할을 한다. 만약 일반 객체의 경우는 각 프로퍼티들을 순환하며 콜백 함수를 실행한다.
<blockquote><strong>jQuery.each( collection, callback(indexInArray, valueOfElement) )</strong>

collection : 반복할 배열이나 객체

callback(indexInArray, valueOfElement) : 모든 객체에서 호출될 함수</blockquote>
예제 코드 및 jQuery.each() 소스코드를 보면서 좀더 자세히 살펴보자.

{% highlight html %}
<!DOCTYPE html>
<html>
<head>
  <style>
  div { color:blue; }
  div#five { color:red; }
  </style>
  <script src="http://code.jquery.com/jquery-1.5.js"></script>
</head>
<body>

  <div id="one"></div>
  <div id="two"></div>
  <div id="three"></div>
  <div id="four"></div>
  <div id="five"></div>
<script>
    var arr = [ "one", "two", "three", "four", "five" ];

    jQuery.each(arr, function() {
      $("#" + this).text("Mine is " + this + ".");
       return (this != "three"); // will stop running after "three"
   });
</script>

</body>
</html>
{% endhighlight %}

위 예제에서  jQuery.each() 함수를 사용한 부분을 살펴보자.  코드는 다음과 같다.
{% highlight js %}
 var arr = [ "one", "two", "three", "four", "five" ];

 jQuery.each(arr, function() {
    $("#" + this).text("Mine is " + this + ".");
    return (this != "three"); // will stop running after "three"
 });
{% endhighlight %}

앞서 설명한 jQuery.each() 메서드의 설명을 참조하면, 위 코드의 대략적인 역할은 arr 배열에 각 원소를 루프돌면서 콜백 함수를 실행하는 것이다.

여기서 우선 살펴봐야 할 것은 콜백 함수에서 this가 무엇을 가리키는 것인가이다.  일반적으로 함수 내부에서 사용된 this는 이 함수를 호출하는 객체를 가리킨다.  즉 this가 가리키는 내용을 알기 위해서는 콜백 함수를 누가 호출하는지 알아야 한다. 결국 이러한 의문을 제대로 하기 위해서는 jQuery.each() 메서드의 소스 코드를 직접 살펴봐야 한다. 이와 관련해서 자세한 내용은 다음 부분에서 좀더 자세히 설명하겠다. 결론부터 얘기하면  this는 각 배열이나 객체의 원소를 가리킨다. 예를 들어 만약 첫번째 루프를 돌고 있다면 이때 콜백 함수내의 this는 "one"을 가리킨다. 결국 콜백 함수는 $("#one")에 의해 선택된 DOM요소의 text 값을 Mine is one.으로 바꾼다.

또한 루프를 돌면서 콜백 함수를 실행하는 도중 콜백 함수의 리턴값이 false인 경우 이후 원소에 대한 콜백 함수 실행은 이뤄지지 않는다.

결과값

{% highlight html %}
Mine is one.
Mine is two.
Mine is three.
<출력 안됨>
<출력 안됨>
{% endhighlight %}

 실제 jQuery.each() 소스를 살펴보자.  
{% highlight js %}
  // args is for internal usage only
  each: function( object, callback, args ) {
    // 콜백 함수의 인자가 있는지 체크
    if ( args ) {
      // 첫번째 인자로 넘어온 객체가 배열이나 배열형 객체인지를 체크
      if ( object.length == undefined ) {
        // 배열이 아닌 일반형 객체일 경우는 객체의 각 프로퍼티를 루프돌면서 콜백 함수를 실행한다.
        // 콜백 함수 호출 시에 함수 인자가 전해진다.
        // 이때 apply 메서드를 통해 실행을 시켰으므로 콜백 함수 내에서 this는 object[name]이 가리키는
        // 값을 가리킨다. 여기서 apply 메서드를 사용한 이유는 object 인자로 넘어온 객체를 수정하기 위함이다.
        for ( var name in object )
          // 콜백 함수의 리턴값이 false인 경우 루프는 취소된다.
          if ( callback.apply( object[ name ], args ) === false )
            break;
      } else
        // 배열이나 배열형 객체일 경우, 0부터 n-1번째까지의 배열 원소들을 루프를 돌면서
        // apply 메서드를 이용 콜백 함수 실행.
        for ( var i = 0, length = object.length; i < length; i++ )
          if ( callback.apply( object[ i ], args ) === false )
            break;

    // A special, fast, case for the most common use of each
    // args가 정의되지 않은 경우
    } else {
      // 일반형 객체의 경우, call 메서드를 통해 콜백 함수를 호출한다. 이 경우 콜백 함수의 인자로
      // 객체의 프로퍼티(name)와 값(object[name])이 전달된다.
      if ( object.length == undefined ) {
        for ( var name in object )
          if ( callback.call( object[ name ], name, object[ name ] ) === false )
            break;
      } else
      // 배열이나 배열형 객체인 경우, 0부터 n-1번째까지의 배열 원소들을 루프돌면서
      // call 메서드를 이용해 콜백 함수 실행.
        for ( var i = 0, length = object.length, value = object[0];
          i < length && callback.call( value, i, value ) !== false; value = object[++i] ){}
    }

    // 첫번째 인자를 다시 반환한다.
    return object;
  },
{% endhighlight %}

