---
layout: post
title: socket.io 를 활용한 실시간 survey 예제
date: '2012-03-25 22:15:38'
---

socket.io를 활용한 예제를 한 번더 살펴보도록 하겠다.
이에 앞서 socket.io를 설명한 포스트와 예제 포스트를 한번 보시고 본문을 보시면 이해가 더 빠를 것이다.

[8회 연재 – socket.io (웹소켓 통신)]({{ site.baseurl }}{% post_url 2011-12-18-node-js-%ec%9c%a0%ec%9a%a9%ed%95%9c-%eb%aa%a8%eb%93%88-8-socket-io %})

[Socket.IO를 활용한 예제 분석]({{ site.baseurl }}{% post_url 2012-02-02-socket-io%eb%a5%bc-%ed%99%9c%ec%9a%a9%ed%95%9c-%ec%98%88%ec%a0%9c-%eb%b6%84%ec%84%9d %})

이번 예제는 실시간 인터넷 투표 예제를 만들어보려고 한다.

다음 문서에서 같은 주제를 구현한 예제에 대한 설명이 있다.

<a href="http://www.netmagazine.com/tutorials/code-real-time-survey-html5-websockets">http://www.netmagazine.com/tutorials/code-real-time-survey-html5-websockets</a>

처음에는 위 문서를 바로 변역해보려고 했는데, 해당 문서에서는 서버는 WebSocket, 클라이언트는 ruby 를 이용하여 구현되어 있었다.
그래서, 그냥 결과물은 위 문서와 같게 하되, 서버는 socket.io, 클라이언트는 javascript를 이용하여 구현하기로 하였다.

그리고 이 포스트에서 구현한 코드는 다음 github에 공개되어 있으니, 참고하기 바란다.
<a href="https://github.com/zzoon/realtime-survey">https://github.com/zzoon/realtime-survey</a>

먼저 server code를 보자 - server.js

{% highlight js %}
  var io;

  io = require('socket.io').listen(4000);

  io.sockets.on('connection', function(socket) {
    socket.on('data-changed', function(data) {
	  console.log("data-changed received!");
      socket.broadcast.emit('data-changed', {
	  	index: data.index,
        vote: data.vote + 1
      });
    });
  });
{% endhighlight %}

역시 socket.io를 쓰니 굉장히 간단히 구현이 된다. 사실 예제니까 그렇다.^^

간단하게 설명하자면,

{% highlight js %}
io.sockets.on.('connection', function(socket) { });
{% endhighlight %}


클라이언트로 연결 요청이 오면, 연결하고 두번 째 인자인 anonymous function을 실행시킨다.

{% highlight js %}
socket.on('data-changed', function(data) { ... });
{% endhighlight %}


서버가 리스닝을 하고 있다가, 클라이언트로부터 'data-changed' 이벤트가 오면 두번 째 인자인 anonymous function을 실행시킨다.

{% highlight js %}
socket.boradcast.emit('data-changed', { ... });
{% endhighlight %}


'data-changed' 이벤트를 두번째 인자인 object와 함께 broadcasting 한다. ( 연결되어 있는 모든 클라이언트에게 해당 이벤트를 전달한다. )
broadcasting 되는 object는 index, vote 를 가지는데, index는 클라이언트의 버튼 index이고, vote는 현재 해당 버튼에 대한 vote 값을 1을 더해 전달한다.
이에 대해서는 밑에 client 예제를 설명할 때 이해할 수 있을 것이다.

위에서 보듯이 서버가 하는일은 딱 3가지다.
(물론 제대로 실시간 투표 시스템을 구현하려고 한다면, 이보다 훨씬 더 많은 일을 해야할 것이다.)

client 예제를 보자.
참고로 클라이언트는 다음과 같은 화면이 나오도록 구현할 것이다.

![]({{ site.url }}/assets/Charting1.jpg)

[index.html]
(1) visualize jquery plugin : 그래프를 표현하기 위해, 여러가지 플러그인을 찾아보았는데, visualize 가 꽤 괜찮아 보여서, 채택했다. 더 마음에 드는 것이 있다면, 그것을 사용해보도록 하자.
참고로 visualize에 대한 자세한 사항은 <a href="http://www.filamentgroup.com/lab/update_to_jquery_visualize_accessible_charts_with_html5_from_designing_with">http://www.filamentgroup.com/lab/update_to_jquery_visualize_accessible_charts_with_html5_from_designing_with</a>를 참고하도록 하자.

(2) index.html
- table 태그 안의 구조는 visualize가 해석할 수 있도록 만들어야 한다. 이는 사용하는 플러그인마다 조금씩 다르므로, 주의하도록 하자.
- visualize로 표현된 그래프 밑에 사용자가 입력할 수 있는 버튼을 추가하였다. 각 버튼마다 id를 두었다. ( 이 id가 websocket을 통해 전달되는 index이다.)
- javascript code : 아래 주석을 살펴보자. 조금 길다...

{% highlight js %}
   // visualize 플러그인을 통해  html에 그대로 정의된 data를 바탕으로 그래프를 그린다.
   $("#example").visualize({type: 'bar', width: '420px'}).trigger('visualizeRefresh');

	var $opt_item;
	var $opt_vote;

	// 서버에 접속한다.
	app_socket = io.connect('http://zzoon.just4fun.co.kr:10706');

	// 서버로부터 'data-changed' 이벤트를 받으면 다음 함수를 실행한다.
	// 여기서는 vote값을 받아 해당 항목을 update하고 그래프를 refresh한다.
	app_socket.on('data-changed', function(data) {
		console.log("data changed!");
		console.log("index:" + data.index + " vote : " + data.vote);

		$opt_arr[data.index].vote = data.vote;
		$opt_arr[data.index].tr_item.innerText = data.vote;

		// visualize refresh
		$('.visualize').trigger('visualizeRefresh');
	});

	// $opt_arr 은 각 항목의 데이타를 저장하는 배열이다.
	// 이 배열의 각 element는 index, vote, tr_item 을 가지는 object를 가리킨다.
	// index : click 이벤트가 발생한 버튼의 id를 나타내는 프로퍼티
	// vote : 해당 항목의 현재 vote 값을 나타내는 프로퍼티
	// tr_item : 해당 항목의 DOM object를 가리키는 프로퍼티
	$opt_arr = new Array();

	// table안의 tr 태그에 대한 DOM object을 찾는다.
	$opt = $('body').find('#example tbody tr');

	// each 함수를 통해 각각의 TR item에 해당하는 DOM object를 찾아 여러가지 값을 얻어낸다.
	$opt.each(function(i, item) {
		// i 번째 TR item
		$opt_item = $(item);
		// i 번째 TR object에서 TD object를 찾아낸다.
		$opt_find = $opt_item.find('td')[0];
		// 해당 TH항목의 innerText값 ( 여기서는 각 여자연예인 이름이 된다 )
		$opt_name = $opt_item.find('th')[0].innerText;
		// 해당 TD항목의 innerText값 ( 여기서는 vote 값이 된다. )
		$opt_vote = $opt_item.find('td')[0].innerText;

		console.log("Name : " + $opt_name);
		console.log("votes : " + $opt_vote);

		// $opt_arr 배열의 i번째에 위에서 찾아낸 값을 가지는 object를 가리키도록 한다.
		$opt_arr[i] = {
			index: i,  // index는 click된 버튼의 id와 같고, 동시에 $opt_arr 배열 안에서의 element index이기도 하다.
			tr_item: $opt_find,  // 여기서 tr_item은 DOM tree의 object를 직접 가리키고 있음을 명심하자.
			vote: Number($opt_vote) // Number 함수를 사용하였다.
		};
	});

	// 버튼의 click 이벤트 핸들러
	var click_handler = function(event) {
		// click된 버튼의 id와 해당 항목의 vote값을 얻어낸다.
		var clicked_id = event.currentTarget.id;
		var vote_cnt = $opt_arr[clicked_id].vote;
		console.log(clicked_id + " clicked");
		console.log("vote : " + vote_cnt);

		// 'data-changed' 이벤트를 서버에 전송한다.
		app_socket.emit('data-changed', {
			index: clicked_id,
			vote: vote_cnt
		});

		// 서버에 이벤트를 보낸후 현재 자신의 화면을 refresh 해야한다.
		vote_cnt++;
		$opt_arr[clicked_id].vote = vote_cnt;
		$opt_arr[clicked_id].tr_item.innerText = vote_cnt;

		// visualize refresh
		$('.visualize').trigger('visualizeRefresh');
	};

	// 버튼의 clieck 이벤트에 대한 핸들러 등록
	$('button').click(click_handler);
{% endhighlight %}

여기까지가 이번 예제에 대한 설명이다.
여러가지 브라우저에서 클라이언트를 실행하고, 버튼을 클릭하면, 실시간으로 그래프가 바뀌는 것을 아래와 같이 볼 수 있을 것이다. ( 테스트 결과, firefox에서 제대로 동작하지 않음을 확인하였다. 그외, 크롬, IE9, 사파리에서는 정상동작을 확인하였다. 클라이언트에서 DOM에 관한 핸들링을 할 때, 문제가 있는 것으로 추측된다.)

![]({{ site.url }}/assets/Charting-1.jpg)

결과가 궁금하신 분은 <a href="http://zzoon.just4fun.co.kr/realtime-survey/">http://zzoon.just4fun.co.kr/realtime-survey/</a> 에 여러 브라우저를 통해 접속한 후, 테스트해 보시기 바란다.

언제나 느끼지만, socket.io를 설명할 때, 클라이언트가 훨씬 코드가 길게 나온다. 이는 예제이기 때문에 서버의 코드가 굉장히 짧은 것이고, ( 서버가 해야할 수많은 일들을 생략 ) 클라이언트는 그래도 화면에 무엇인가를 그려줘야 하기때문에, 예제라고는 해도 생략할 수 없는 것들이 있다.

위 예제에서는 단순한 web socket 통신 및 데이타를 받아서 클라이언트가 refresh하는 동작만을 보여주는데, 사실 심각한 문제가 있다.
초기 데이타값이 table 태그안에 그대로 정의되어 있는 것이다. 따라서, 어떤 클라이언트에서 아무리 김태희에 클릭질을 해도, 다른 클라이언트가 접속하면, 초기값이 그대로 나올 것이다.
이를 해결하려면 서버가 데이타베이스(아니면, 뭐든지)를 통해 vote 값을 관리해야 하고, 클라이언트는 초기화면에서 이 값을 서버로부터 내려받아 그려주어야 할 것이다.

이에 대한 코드는 다음 포스트에서 설명하도록 하겠다.
(코딩할 시간이 필요하다.ㅡ.ㅡ;)
