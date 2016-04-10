---
layout: post
title: Node.js 도움말 번역 - C/C++ Add-on  작성 관련
date: '2012-01-17 23:41:21'
---

<p>Node.js  C/C++ Add-on에 관심이 있어서, 가장 기본적인 Node.js 도움말에 관련 내용을 번역해 봤습니다. 이를 토대로 좀더 구체적인 바이너리 모듈 작성 방법에 대해서 정리되는대로 올리겠습니다.</p>
<ul>관련 자료
<li><a href="http://code.google.com/apis/v8/embed.html" title="V8 Embedder's guide" target="_blank">V8 Embedder's guide</a></li>
</ul>
<h1>Addons</h1>
<p>Add-on은 동적으로 링크되는 공유 오브젝트입니다. (역자주-C언어에서의 .so 라이브러리라고 생각하면 됩니다.) 그것은 기존의 C/C++ 라이브러리를 사용하기 위한 진입점 역할을 할 수 있습니다. Add-on을 만들기 위한 API는 다음과 같은 몇개의 라이브러리를 포함한 지식들을 필요로 합니다.</p>
<ul>
<li>
<p>V8 자바스크립트 엔진 (C++ 라이브러리). 자바스크립트 코드와의 인터페이스를 처리하는데 사용 : 객체 생성, 함수 호출 등. <strong>v8.h</strong>헤더 파일 (Node.js 소스 트리 <strong>deps/v8/include/v8.h</strong>) 에 필요한 내용이 문서화되어 있습니다.</p>
</li>
<li>
<p><a href="https://github.com/joyent/libuv">libuv</a> (C 기반 이벤트 루프 라이브러리). 파일 디스크립터가 읽기 가능할 때나, 타이머가 만료될 때, 또는 무언가 수신됐다는 시그널을 기다리는 경우에 libuv를 사용할 수 있습니다. 만약 여러분이 어떤 I/O(입출력)을 수행하던지 libuv를 이용할 수 있습니다.</p>
</li>
<li>
<p>Node 내부 라이브러리. <strong>node::ObjectWrap</strong>클래스가 가장 중요합니다.</p>
</li>
<li>
<p>기타 라이브러리. <strong>deps/</strong> 디렉토리 내부를 살펴보세요.</p>
</li>
</ul>
<p>Node는 그것의 모든 의존성을 가진 모듈들을 정적으로 컴파일해서 실행 파일을 생성합니다. 따라서 여러분이 모듈을 컴파일 할때 어떤 라이브러리들을 링크해야 하는 지 걱정할 필요가 없습니다.</p>
<p>다음과 같은 자바스크립트 코드와 동일한 기능의 간단한 C++ Add-on을 만드는 것을 시작할 것입니다.</p>
{% highlight js %}
exports.hello = function() { return 'world'; };
{% endhighlight %}

우선 다음과 같이 hello.cc 파일을 생성하세요.<br />
{% highlight cpp %}
#include <node.h>
#include <v8.h>
using namespace v8;

Handle<Value> Method(const Arguments& args) {
  HandleScope scope;
  return scope.Close(String::New("world"));
}

void init(Handle<Object> target) {
  NODE_SET_METHOD(target, "hello", Method);
}

NODE_MODULE(hello, init)
{% endhighlight %}

<p>모든 Node Add-on들은 초기화 함수를 익스포트 해야한다는 것을 기억하세요.</p>

{% highlight cpp %}
void Initialize (Handle&lt;Object&gt; target);
NODE_MODULE(module_name, Initialize)
{% endhighlight %}

<p>NODE_MODULE은 함수가 아니기 때문에 문장 끝에 세미콜론(;)이 없습니다. (node.h 파일을 살펴보세요)</p>
<p>module_name은 최종 바이너리의 파일명과 일치하게 합니다. (.node 확장자명을 제거)</p>
<p>위 소스 코드는 바이너리 Add-on인 hello.node 파일로 빌드되야 합니다. 이를 위해서 다음과 같은 파이썬 기반의 빌드 스크립트 파일인 wscript 파일을 작성해야 합니다.</p>

{% highlight python %}
srcdir = '.'
blddir = 'build'
VERSION = '0.0.1'

def set_options(opt):
  opt.tool_options('compiler_cxx')

def configure(conf):
  conf.check_tool('compiler_cxx')
  conf.check_tool('node_addon')

def build(bld):
  obj = bld.new_task_gen('cxx', 'shlib', 'node_addon')
  obj.target = 'hello'
  obj.source = 'hello.cc'
{% endhighlight %}

<p>node-waf configure build 명령을 통해 위 스크립트를 쉘 상에서 실행하면, 여러분이 작성한 build/default/hello.node Add-on 파일이 생성됩니다.</p>
<p>node-waf는 파이썬 기반의 빌드 시스템인 <a href="http://code.google.com/p/waf">WAF</a>와 동일합니다. node-waf는 쉽게 모듈을 빌드할 수 있게 하기 위해 제공하고 있습니다.</p>
<p>여러분은 아래와 같이 hello.js라는 Node 프로젝트를 만들고, 이 파일을 통해 바이너리 Add-on을 바로 사용할 수 있습니다. 작성된 모듈을 사용하기 위해서는 require문을 통해 사용할 모듈을 지정하면 됩니다.</p>

{% highlight js %}
var addon = require('./build/Release/hello');
console.log(addon.hello()); // 'world'
{% endhighlight %}

<p>좀더 자세한 개발 예제를 보고 싶다면 <a href="https://github.com/pietern/hiredis-node">https://github.com/pietern/hiredis-node</a> 를 참조하세요.</p>
