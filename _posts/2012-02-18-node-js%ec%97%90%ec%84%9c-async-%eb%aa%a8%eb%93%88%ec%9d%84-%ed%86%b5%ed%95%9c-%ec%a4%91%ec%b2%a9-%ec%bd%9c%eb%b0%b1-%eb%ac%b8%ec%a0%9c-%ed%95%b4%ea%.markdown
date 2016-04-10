---
layout: post
title: Node.js에서 Async 모듈을 통한 중첩 콜백 문제 해결하기
date: '2012-02-18 16:56:04'
---

<h1 id="post-670" style="font-size: 28px; font-weight: bold; color: #5d6e00; letter-spacing: -1px; line-height: 27px; font-family: Arial, sans-serif; font-style: normal; font-variant: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; padding: 0px; margin: 0px;">Node.js에서 Async 모듈을 통한 중첩 콜백 문제 해결하기</h1>
<div class="post-meta" style="margin-top: 10px; margin-right: 0px; margin-bottom: 16px; margin-left: 0px; font-size: 12px; color: #5e4614; letter-spacing: normal; font-family: Arial, sans-serif; font-style: normal; font-variant: normal; font-weight: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; padding: 0px;"><em>Posted on January 18th, 2012 under <a style="color: #5d6e00; text-decoration: none;" title="View all posts in Node.js" rel="category tag" href="http://www.hacksparrow.com/category/node-js-2">Node.js</a></em> <em>Tags: <a style="color: #5d6e00; text-decoration: none;" rel="tag" href="http://www.hacksparrow.com/tag/async-js">Async.js</a>, <a style="color: #5d6e00; text-decoration: none;" rel="tag" href="http://www.hacksparrow.com/tag/node-js">node.js</a></em></div>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">원문 링크 - <a href="http://www.hacksparrow.com/managing-nested-asynchronous-callbacks-in-node-js.html">http://www.hacksparrow.com/managing-nested-asynchronous-callbacks-in-node-js.html</a></p>
관련글 - [node.js에서 비동기로 코드 디자인 하기]({{ site.baseurl }}{% post_url 2011-10-31-%eb%b2%88%ec%97%ad-node-js%ec%97%90%ec%84%9c-%eb%b9%84%eb%8f%99%ea%b8%b0%eb%a1%9c-%ec%bd%94%eb%93%9c-%eb%94%94%ec%9e%90%ec%9d%b8%ed%95%98%ea%b8%b0 %})
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">Node.js를 가지고 작업해본 개발자라면 누구나 여러 단계로 중첩된 콜백 함수 처리에 대한 골치아픈 문제를 겪게 될 것이다.</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">여러 겹의 중첩된 비동기 콜백 함수 문제는 최초 콜백 함수 내부에서 콜백 함수를 호출하고, 그 콜백 함수에서 다시 또다른 콜백 함수를 호출하고.. 이런 식으로 콜백 함수가 계속 중첩되는 상황에서 발생한다. 콜백 함수의 중첩 단계가 늘어날 수록 코드는 지저분해지고 결국엔 관리 불가의 상태가 될 수도 있다. 다음 예제를 살펴보자.</p>

{% highlight js %}
var fs = require('fs');
 
var path = './async.txt';
// check if async.txt exists
fs.stat(path, function(err, stats) {
    if (stats == undefined) {
        // read the contents of this file
        fs.readFile(__filename, function(err, content) {
            var code = content.toString();
            // write the content to async.txt
            fs.writeFile(path, code, function(err) {
                if (err) throw err;
                console.log('async.txt created!');
            });
        });
    }
});
{% endhighlight %}

<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">위 예제는 3단계의 중첩된 비동기 콜백 함수가 사용됐다. 여러분은 이것보다 훨씬 심한 콜백 함수의 중첩이 필요한 상황을 접할 수도 있을 것이다.</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">한 가지 방법은 중첩될 콜백 함수를 외부에 정의하고, 콜백 함수에서 외부에 정의한 함수를 호출하는 것이다. 그러나 이 방법은 네임 스페이스를 복잡하게 하며 코드 또한 지저분하게 보인다.</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">Node.js에서 중첩 콜백 문제를 처리하기 위한 다양한 비동기 제어 모듈들이 개발되고 있다. 내가 볼때 가장 유력한 모듈은 <a style="color: #5d6e00; text-decoration: none;" href="https://github.com/caolan/async">Async</a>와 <a style="color: #5d6e00; text-decoration: none;" href="https://github.com/creationix/step">Step</a>이다. 나는 Async를 선택했는 데, 그 이유는 확장성과 몇가지 특징들 때문이다. Asyn는 Step이 할 수 있는 일 뿐만 아니라, 더 많은 것을 할 수 있다. 우리가 Async를 이용해서 위 예제를 고쳐보자.</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">우선 Async 모듈을 설치하자.</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">그럼 다음, Async 모듈을 이용해서 위 소스 코드를 다시 작성하자.</p>

{% highlight js %}
var fs = require('fs');
var async = require('async');
 
var path = './async.txt';
async.waterfall([
    // check if async.txt exists
    function(cb) {
        fs.stat(path, function(err, stats) {
            if (stats == undefined) cb(null);
            else console.log('async.txt exists');
        });
    },
    // read the contents of this file
    function(cb) {
        fs.readFile(__filename, function(err, content) {
            var code = content.toString();
            cb(null, code);
        });
    },
    // write the content to async.txt
    function(code, cb) {
        fs.writeFile(path, code, function(err) {
            if (err) throw err;
            console.log('async.txt created!');
        });
    }
]);
{% endhighlight %}

<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">훨씬 더 보기 좋지 않은가? 실행 순서를 알아보기 쉬울 뿐 아니라, 콜백 함수가 중첩되지도 않는다. (가령, 10단계까지 중첩된 콜백 함수들의 코드를 상상해보자)</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">Async는 위에서 살펴본 것 이외에 훨씬 많은 기능들을 가지고 있다. Async에 대한 정보를 알고 싶으면 <a style="color: #5d6e00; text-decoration: none;" href="https://github.com/caolan/async">Async GitHub page</a>를 참조하자.</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">[역자주]</p>
<p style="padding-top: 0px; padding-right: 0px; padding-bottom: 15px; padding-left: 0px; color: #000000; font-family: Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: normal; orphans: 2; text-align: -webkit-auto; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; background-color: #ffffff; margin: 0px;">- Async는 Node.js 뿐만 아니라, 기존의 브라우저에서 동작하는 클라이언트 자바스크립트 코드에도 그대로 적용된다.<br />- Async를 잘 사용하는 것도 중요하지만, 이를 어떻게 구현했는지를 코드를 통해 살펴보는 것도 많은 도움이 될 것이다.</p>
