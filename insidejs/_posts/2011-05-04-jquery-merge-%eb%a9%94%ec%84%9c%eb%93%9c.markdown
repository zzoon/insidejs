---
layout: post
title: jQuery.merge() 메서드
date: '2011-05-04 00:28:52'
---

jQuery.merge() 메서드에 대해서 알아보자. 간단한 코드이기 때문에 별도의 설명없이 바로 코드를 살펴보자.
아래 코드는 jQuery.merge() 메서드 코드이다. (크로스 브라우저를 지원하기 위한 코드는 제외했다.)

[code lang="js" title="jQuery.merge() 메서드 코드"]
merge: function( first, second ) {

	for ( var i = 0; second[ i ]; i++ )
		first.push( second[ i ] );

	// first 객체 리턴
	return first;
},
[/code]

코드의 내용은 간단하다. first, second 인자는 둘다 배열이고, second 배열의 내용을 first 배열의 끝에 붙이는 것이다. (배열 객체에서 사용된 push() 메서드는 배열의 끝의 값을 추가하는 역할을 한다.)

for문에서 second[i] 라는 조건 체크 부분이 좀 특이하다. 결국은 second[i]의 배열의 마지막을 지나면 false가 될 것이기 때문이다. 그런데 여기서 second[i] 값이 0이 되면 어떻게 되는거지??