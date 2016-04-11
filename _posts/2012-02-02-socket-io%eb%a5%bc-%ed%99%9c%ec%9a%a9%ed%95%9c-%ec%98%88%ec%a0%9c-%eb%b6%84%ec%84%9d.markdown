---
layout: post
title: Socket.IO를 활용한 예제 분석
date: '2012-02-02 23:15:38'
---

socket.io를 활용한 재미있는 예제를 찾던 중, 다음을 발견하였다.

<a href="http://wesbos.com/html5-canvas-websockets-nodejs/">http://wesbos.com/html5-canvas-websockets-nodejs/</a>

이 예제는 각 클라이언트가 socket.io 서버에 접속을 하고, 한 명의 클라이언트가 자신의 브라우저에 그림을 그리면, 다른 클라이언트의 브라우저에도 실시간으로 똑같이 그림이 그려지도록 하는 예제이다. 원문을 클릭하면, 해당 예제를 돌리는 동영상을 볼 수 있다.

본 글에서는 이 페이지에서 소개하는 예제를 분석해 보도록 하겠다.
( 원문에는 coffee script를 활용하였으나, 역자는 컴파일을 통해 생성되는 javascript 코드를 가지고 분석하도록 하겠다.)

원문의 소스가 있는 github 주소는 다음과 같다.
<a href="https://github.com/wesbos/websocket-canvas-draw">https://github.com/wesbos/websocket-canvas-draw</a>

본문으로 들어가기에 앞서, socket.io에 대한 지식을 먼저 갖추도록 하자.
공식 사이트는 <a href="http://socket.io">http://socket.io</a> 이다.
본 블로그에서는 [8회 연재 – socket.io (웹소켓 통신)]({{ site.baseurl }}{% post_url 2011-12-18-node-js-%ec%9c%a0%ec%9a%a9%ed%95%9c-%eb%aa%a8%eb%93%88-8-socket-io %}) 에서 socket.io에 대하여 간단히 소개한 글을 번역한 글이 있고, 한글 문서 중에서는 Web Socket과 함께 자세한 설명이 있는 문서를 다음 주소에서 찾을 수 있다.

<a href="http://helloworld.naver.com/helloworld/1336">http://helloworld.naver.com/helloworld/1336</a>

자, 이제 본격적으로 서버 코드를 보자.
<h3><span style="color: #000000;"><strong>[server.js]</strong></span></h3>
{% highlight js %}
var io;
io = require('socket.io').listen(4000);
io.sockets.on('connection', function(socket) {
  socket.on('drawClick', function(data) {
    socket.broadcast.emit('draw', {
      x: data.x,
      y: data.y,
      type: data.type
    });
  });
});
{% endhighlight %}

전형적인 socket.io의 예제코드다.

'connection' 이벤트가 발생하면, 즉, 연결이 이루어지면, 해당 소켓에서 'drawClick' 이벤트를 리스닝하게 된다.
그리고 'drawClick' 이벤트가 발생하면, { x, y, type } 객체와 함께 'draw' 이벤트를 연결되어 있는 모든 클라이언트에게 브로드캐스팅 하게 된다. 여기서 x와 y는 브라우저의 캔버스의 좌표이고, type은 이벤트 타입( dragstart, drag, dragend, 아래 설명됨 )이다.

서버는 위가 전부다. 그렇다면, 클라이언트는 어떻게 되어 있을까?
<h3><strong>[ index.html ]</strong></h3>
{% highlight html %}
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
    <script type="text/javascript" src="js/jquery.event.drag-2.0.js"></script>
    <script src="http://localhost:4000/socket.io/socket.io.js"></script>
    <script type="text/javascript" src="scripts.js"></script>
    <link rel="stylesheet" href="style.css" />

    <title>HTML5 Canvas + Node.JS Socket.io</title>
</head>
<body>
    <article><!-- our canvas will be inserted here--></article>
</body>
{% endhighlight %}

여기서 클라이언트는 다음의 javascript를 로딩한다.
- jquery
- jquery.event.drag-2.0.js
- socket.io 서버의 socket.io.js
- scripts.js
<h3><strong>[ jquery.event.drag-2.0.js ]</strong></h3>
이 코드는 다양한 웹브라우저의 여러가지 마우스 이벤트 중에서 drag에 대한 처리를 dragstart, drag, dragend 라는 이벤트로 편하게 통일된 방식으로 처리할 수 있도록 만든 jquery plugin 소스이다. ( 이 플러그인 없이, 브라우저의 이벤트를 통해 드래그에 대한 이벤트 핸들링을 하려한다면, 각 브라우저별로 이벤트 핸들링 코드를 구현해야 할 것이다.) 이 소스코드에 대한 설명은 다음 기회에 하도록 하겠다. ( 사실 웹 관련 지식이 부족한 편이라 ( 특히 browser-specific한 부분) 아직 분석할 능력이 안된다 )
<h3><strong>[socket.io.js]</strong></h3>
클라이언트에서 sokcet.io 를 사용하기 위해 로딩한다.
<h2><strong>[scripts.js]</strong></h2>
이 소스에서 하는 일은 다음과 같다.
<strong>(1) canvas를 만들어 그림을 그릴 준비를 한다.</strong>

(1)의 소스는 다음과 같다.
{% highlight js %}
App.init = function() {
  App.canvas = document.createElement('canvas');
  App.canvas.height = 400;
  App.canvas.width = 800;
  document.getElementsByTagName('article')[0].appendChild(App.canvas);
  App.ctx = App.canvas.getContext("2d");
  App.ctx.fillStyle = "solid";
  App.ctx.strokeStyle = "#bada55";
  App.ctx.lineWidth = 5;
  App.ctx.lineCap = "round";
  ...........................
};
{% endhighlight %}

- createElement를 통해 DOM element(canvas)를 만들고 특성(width, height)을 정한다.
- article이라는 DOM Element의 자식으로 canvas를 붙인다. ( 여기서 article은 위 index.html에서 기술된 그 article태그를 말한다.)
- getContext("2d") 를 통해 canvas의 context를 얻어온다. 이 context를 통해 그림을 (정확히는 선을) 그릴 것이다.

<strong>(2) dragstart, drag, dragend 이벤트가 발생하면 해당 좌표를 담아서 drawClick 이벤트를 socket.io 서버로 보낸다.</strong> (2)의 소스는 다음과 같다.

{% highlight js %}
$('canvas').live('drag dragstart dragend', function(e) {
  var offset, type, x, y;
  type = e.handleObj.type;
  offset = $(this).offset();
  e.offsetX = e.layerX - offset.left;
  e.offsetY = e.layerY - offset.top;
  x = e.offsetX;
  y = e.offsetY;

  App.draw(x, y, type);

  App.socket.emit('drawClick', {
    x: x,
    y: y,
    type: type
  });
});
{% endhighlight %}

- live 함수는 jquery 함수로서 특정 이벤트에 대한 핸들러를 정의할 때 쓰인다. ( 이 함수는 현재 deprecated 되었다. 현재는 on()을 쓴다. )
- live 함수를 통해 drag, dragstart, dragend 이벤트에 대한 핸들러를 정의하였다. 여기서 drag, dragstart, dragend는 위에서 설명했듯이, jquery.event.drag-2.0.js 에서 발생시킨다.
- 핸들러를 보면, x, y좌표를 계산하고, Draw() 한후, socket.io 서버에 이 좌표와 현재 이벤트의 타입을 담아, 'drawClick'이벤트를 보낸다.
- 위의 서버 코드에서 설명했듯이, 서버는 클라이언트로부터 drawClick 이벤트를 받으면, {x,y, type}을 담아 모든 클라이언트에 draw 이벤트를 보낸다.

<strong>(3) 서버로부터 draw 이벤트를 받으면 해당 좌표에 그림을 그린다.</strong>
(3)의 소스는 다음과 같다.
{% highlight js %}
App.init = function() {
  ............................

  App.socket = io.connect('http://localhost:4000');
  App.socket.on('draw', function(data) {
    return App.draw(data.x, data.y, data.type);
  });

  App.draw = function(x, y, type) {
    if (type === "dragstart") {
      App.ctx.beginPath();
      return App.ctx.moveTo(x, y);
    } else if (type === "drag") {
      App.ctx.lineTo(x, y);
      return App.ctx.stroke();
    } else {
      return App.ctx.closePath();
    }
  };
};
{% endhighlight %}

- socket.on 을 통해 'draw' 이벤트를 리스닝하고 해당 이벤트가 발생하면 그림을 그린다.
- App.draw()는 그림(선)을 그리는 함수이다.
- beginPath, moveTo, lineTo, stroke, closePath 함수에 대한 설명은 생략하겠다. canvas에 대한 자세한 사항은 <a href="https://developer.mozilla.org/en/Drawing_Graphics_with_Canvas">https://developer.mozilla.org/en/Drawing_Graphics_with_Canvas</a> 에 설명되어 있다.

이렇게 해서 간단하게 한 클라이언트가 그림을 그리면 다른 클라이언트의 브라우저에서도 똑같이 그림이 그려지는 재미난 예제 코드에 대해 분석해 보았다.

이 예제를 통해 socket.io를 통해 무엇을 만들 수 있을지 감을 잡을 수 있을 것이라 생각한다.
