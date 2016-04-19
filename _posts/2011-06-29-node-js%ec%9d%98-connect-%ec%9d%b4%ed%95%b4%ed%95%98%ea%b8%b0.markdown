---
layout: post
title: node.js의 connect 이해하기
date: '2011-06-29 00:00:02'
---

express로 프로그래밍하다 보면, connect라는 라이브러리가 범상치 않은 놈임을 눈치챌 수 있다.

이녀석을 잘 설명한 문서를 찾다 아래 문서를 발견했다. rough하게 발번역하고,  요약해보았다.

[참고] <a href="http://howtonode.org/connect-it">http://howtonode.org/connect-it </a>

connect는 node를 가능한 작게 추상화하고 리패키징한 것.

그 결과 API는 상당히 node-specific하고, 견고함.

그리고 connect는 unique한 한가지 요소를 node HTTP 서버에 추가했다는데, 그것이 바로 layer.

<h2>통합(integration) 문제</h2>
다음은 일반적인 node HTTP서버 코드

{% highlight js %}
var http = require('http');

// Start an HTTP server
http.createServer(function (request, response) {

    // Every request gets the same "Hello Connect" response.
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.end("Hello Connect");

}).listen(8081);
{% endhighlight %}

이는 잘 동작하지만, 항상 같은 response를 주기 때문에, 실제로는 별 쓸모 없다는 것.

사용자는 요청에 대한 라우팅이나, 혹은 더 향상된 기능을 제공하고 싶어함. ( 이를 테면, 응답(response body) 압축, 괜찮은 캐싱, 로그, 에러 핸들링 등 )

이런 것들을 프로젝트를 수행할 때마다 매번 구현하는 것은 당연히 엄청난 노가다. 물론 node 커뮤니티에 다양한 모듈들이 이미 있지만.... 문제는 이에 대한 스펙 자체가 없다는 것.

따라서 이 모듈들 통합하는 것은 각자 알아서, 지 맘대루. 이것이 문제!

<h2>구원자 Layer! ( Layers to the Rescue )</h2>
그래서 layer란 개념을 도입!
앱은 마치 양파(-_-)와 같은 구조가 된다.
모든 요청은 양파의 바깥에서부터 들어가서 각 층(layer)별로 돌아다님. 해당 요청을 처리하고, 응답을 만들 때 까지..

connect에서는 이들을 filters 와 providers로 부른다. 일단 특정 layer가 응답을 제공하면, 다시 거꾸로 쭉쭉~

connect 프레임웍은 단순히 최초 요청과 node의 http 콜백으로부토 오는 응답을 취한 후, layer 별로 전달한다. 이 layer들은 이미 앱에서 설정된 미들웨어 모듈들.

위 예제를 connect를 써서 구현한 코드

{% highlight js %}
var Connect = require('connect');

Connect.createServer(function (req, res, next) {
  // Every request gets the same "Hello Connect" response.
  res.simpleBody(200, "Hello Connect");
}).listen(8080);
{% endhighlight %}

<h2>앱과 layer를 구현해보자 ( Walkthrough Writing Layers and an Application )</h2>
서버에서 요청된 파일을 열어서 제공하고, 램에 캐쉬하고, 요청과 응답을 로깅하는 서버를 만들기.

{% highlight js %}
var Connect = require('connect');

module.exports = Connect.createServer(
  require('./log-it')(),
  require('./serve-js')()
);
{% endhighlight %}

보는 바와 같이 Connect.createServer에 2가지 핸들러를 넘겨 호출하고 있다.

모든 connect layer는 setup 함수를 export하고, 핸들러 함수를 리턴한다. setup 함수는 서버 시작시에 호출되고, 설정 정보를 넘길 수 있다.

그리고나서, 요청이 들어왔을 때 다음과 같은 옵션이 있다.

(A) 응답

(B) 다음 layer에 컨트롤 넘기기

<h2>파일 내용 제공하기 ( Serve Some Files )</h2>

{% highlight js %}
var fs = require('fs');

module.exports = function serveJsSetup() {
  return function serveJsHandle(req, res, next) {
    // Serve any file relative to the process.
    // NOTE security was sacrificed in this example to make the code simple.
    fs.readFile(req.url.substr(1), function (err, data) {
      if (err) {
        next(err);
        return;
      }
      res.simpleBody(200, data, "application/javascript");
    });
  };
};
{% endhighlight %}

simpleBody는 connect에서 제공하는 helper 함수.
<h2>로깅하기 (Log It)</h2>
요청들어오면 콘솔에 출력하고, 다음 layer로~

{% highlight js %}
module.exports = function logItSetup() {

  // Initialize the counter
  var counter = 0;

  return function logItHandle(req, res, next) {
    var writeHead = res.writeHead; // Store the original function

    counter++;

    // Log the incoming request
    console.log("Request " + counter + " " + req.method + " " + req.url);

    // Wrap writeHead to hook into the exit path through the layers.
    res.writeHead = function (code, headers) {
      res.writeHead = writeHead; // Put the original back

      // Log the outgoing response
      console.log("Response " + counter + " " + code + " " + JSON.stringify(headers));

      res.writeHead(code, headers); // Call the original
    };

    // Pass through to the next layer
    next();
  };
};
{% endhighlight %}

위에서 눈여겨 볼 것은 node의 API인 writeHead를 hooking 한다는 것.

결국 res.writeHead를 호출하면 위 함수가 먼저 호출되고, original이 호출될 것임.

hooking의 간단하고도 효율적인 방법.

<h2>빌트인 미들웨어 ( Built-in Middleware )</h2>
손쉬운 사용을 위해 connect는 몇가지 빌트인 미들웨어 layer를 제공.

{% highlight js %}
var Connect = require('connect');

module.exports = Connect.createServer(
  Connect.logger(), // Log responses to the terminal using Common Log Format.
  Connect.responseTime(), // Add a special header with timing information.
  Connect.conditionalGet(), // Add HTTP 304 responses to save even more bandwidth.
  Connect.cache(), // Add a short-term ram-cache to improve performance.
  Connect.gzip(), // Gzip the output stream when the browser wants it.
  Connect.staticProvider(__dirname) // Serve all static files in the current dir.
);

{% endhighlight %}

<h2>Future and Goals of Connect</h2>
원문참조바람.
