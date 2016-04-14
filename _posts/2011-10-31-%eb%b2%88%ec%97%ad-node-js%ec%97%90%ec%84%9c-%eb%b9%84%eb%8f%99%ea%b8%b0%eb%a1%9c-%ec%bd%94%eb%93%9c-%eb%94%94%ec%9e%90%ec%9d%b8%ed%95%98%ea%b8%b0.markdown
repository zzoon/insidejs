---
layout: post
title: '[번역] node.js에서 비동기로 코드 디자인하기.'
date: '2011-10-31 22:23:59'
---

<a href="http://shinetech.com/thoughts/articles/139-asynchronous-code-design-with-nodejs-">http://shinetech.com/thoughts/articles/139-asynchronous-code-design-with-nodejs-</a>

node.js의 비동기 이벤트 드리븐 I/O는 기존의 전통적인 방식인 멀티스레드를 기반으로 한 동기 I/O 방식의 대체 방식으로서 고성능을 보장하는 방식으로 평가받고 있다. 이러한 비동기 방식이 보급되면서, 기존의 서버개발자들은 새로운 프로그래밍 패턴을 배워야하고, 기존의 것을 잊어야 하게 되었다. 전기 충격 요법을 통해야만 가능한 심각한 두뇌의 재구성을 겪어야 한다. 이 글은 어떻게 기존의 동기 프로그래밍 방식을 새로운 비동기 프로그래밍 방식으로 대체할 지에 대해서 보여주고자 한다.
<p style="font-size: 18px;"><strong>시작</strong></p>
node.js로 작업을 하려면, 비동기 프로그래밍이 어떻게 동작하는 지를 필수적으로 알아야 한다. 비동기 코드 디자인은 간단한 문제가 아니고, 약간의 학습이 필요하다.

이제 전기충격을 가할 시간이 되었다.

아래는 node.js의 파일시스템 모듈(fs)을 가지고 동기 방식의 코드를 보여주고, 이를 어떻게 비동기 방식으로 진화시킬지에 대해 설명하게 될 것이다.
<p style="font-size: 18px;"><strong>종속적인 코드와 독립적인 코드</strong></p>
콜백 함수는 node.js의 비동기 이벤트 드리븐 프로그래밍의 기본블록이라고 할 수 있다. 이 콜백함수는 비동기 I/O 작업의 인자로 넘겨진다. 그리고 해당 작업이 완료되면 한 번 호출된다. 콜백 함수는 node.js의 이벤트에 대한 구현이라고 할 수 있다.

다음 예제는 동기 I/O 방식을 어떻게 비동기 I/O로 바꾸는 지를 보여준다. 동시에, 콜백 함수가 어떻게 사용되는지에 대해서도 보여준다. 예제는 현재 디렉토리의 파일을 읽고, 파일명을 콘솔에 출력하고, 현재 프로세스의 아이디를 읽는다.

- 동기 코드
{% highlight js %}
var fs = require('fs'),
    filenames,
    i,
    processId;

filenames = fs.readdirSync("."); 
for (i = 0; i < filenames.length; i++) {
     console.log(filenames[i]);
}
console.log("Ready.");

processId = process.getuid();
{% endhighlight %}

이 동기 예제에서 CPU는 fs.readdirSync() I/O 작업이 완료될 때까지 기다린다. 비동기로 작성하려면 이 부분이 변경되어야 한다. 아래 비동기 버전에서는 fs.readdir()을 호출했다. 이는 fs.readdirSync()와 같지만 두번째 인자로 콜백함수를 받는다.

- 비동기 코드
{% highlight js %}
var fs = require('fs'),
    processId;

fs.readdir(".", function (err, filenames) {
     var i;
     for (i = 0; i < filenames.length; i++) {
         console.log(filenames[i]);
     }
     console.log("Ready.");
});

processId = process.getuid();
{% endhighlight %}

콜백함수 패턴을 사용하는 룰은 다음과 같다.

동기 함수를 그에 대응되는 비동기함수로 대체하고, 동기 함수 호출 후에 실행되는 원래 코드를 콜백함수에 넣는다. 콜백함수의 코드는 동기예제의 코드와 똑같다. 이 콜백함수는 비동기 I/O작업이 끝나면 실행된다.

출력되는 파일이름은 fs.readdirSync() 의 결과에 종속적이듯이, 출력되는 파일이름의 갯수도 마찬가지다. 하지만 processID는 I/O작업과는 독립적이다. 따라서 이를 다른 곳으로 옮길 수 있다.

종속적인 코드는 콜백 함수로 이동시키고 독립적인 코드는 그대로 두자. 종속적인 코드는 I/O 작업이 끝나는 데로 실행되는 반면, 독립적인 코드는 I/O 작업이 호출된 이후 바로 실행된다.
<p style="font-size: 18px;"><strong>시퀀스 (Sequence)</strong></p>
동기 코드의 표준적인 패턴은 선형의 시퀀스(linear sequence)라고 볼 수 있다. 각각의 라인은 이전 라인의 결과에 영향을 받으므로 하나씩 차례대로 실행된다. 다음에 나오는 예제는 먼저 파일의 접근 모드를 변경하고 파일명을 변경한 후, 그 파일이 심볼릭 링크인지 체크한다. 분명히 이 코드는 순서가 어긋나면 동작할 수 없다. 아니면, 파일명은 접근모드가 변경되기 전에 변경되거나, 파일명이 변경되기 전에 심볼릭 링크인지 체크할 수도 있다. 물론 둘 다 에러다. 순서가 반드시 보장되어야 한다.

- 동기 코드
{% highlight js %}
var fs = require('fs'),
    oldFilename,
    newFilename,
    isSymLink;

oldFilename = "./processId.txt";
newFilename = "./processIdOld.txt";

fs.chmodSync(oldFilename, 777);
fs.renameSync(oldFilename, newFilename);

isSymLink = fs.lstatSync(newFilename).isSymbolicLink();
{% endhighlight %}

- 비동기 코드
{% highlight js %}
var fs = require('fs'),
    oldFilename,
    newFilename;

oldFilename = "./processId.txt";
newFilename = "./processIdOld.txt";

fs.chmod(oldFilename, 777, function (err) {
     fs.rename(oldFilename, newFilename, function (err) {
         fs.lstat(newFilename, function (err, stats) {
             var isSymLink = stats.isSymbolicLink();
         });
     });
});
{% endhighlight %}

비동기 코드에서 이 시퀀스를 중첩 콜백(nested callback)을 통해 구현하였다. 이 예제에서 fs.lstat() 콜백은 fs.rename() 콜백 안에 중첩되고, fs.rename()은 fs.chmod() 콜백안에 중첩되었다.
<p style="font-size: 18px;"><strong>병렬화 ( Parallelisation )</strong></p>
비동기 코드는 특히 I/O 작업의 병렬화에 적합하다. 실행되는 코드는 I/O 호출의 결과를 기다리지 않는다. 여러 I/O 작업을 병렬적으로 시작할 수 있다. 다음에 나오는 예제에서는 디렉토리안의 모든 파일의 사이즈를 더하여 총합을 구하는 예제다. 동기 코드에서는 한 번 루프를 돌 때마다 I/O 호출이 이루어져 각 파일의 사이즈를 얻을 때까지 기다려야 한다.

비동기 코드에서는 결과를 기다릴 필요없이 빠르게 루프를 돌며 I/O 호출이 이루어진다. 각각의 I/O 작업이 완료될 때마다, 콜백함수가 호출되고, 해당 파일의 사이즈가 더해진다.

여기서 필요한 단 한가지는 총합을 구하는 작업이 완료되었는지를 알 수 있는 기준을 가지고 있어야 한다는 것이다.

- 동기 코드
{% highlight js %}
var fs = require('fs');

function calculateByteSize() {
     var totalBytes = 0,
         i,
         filenames,
         stats;

     filenames = fs.readdirSync(".");
     for (i = 0; i < filenames.length; i ++) {
         stats = fs.statSync("./" + filenames[i]);
         totalBytes += stats.size;
     }
  
     console.log(totalBytes);
}

calculateByteSize();
{% endhighlight %}

동기 코드는 간단하다.

- 비동기 코드
{% highlight js %}
var fs = require('fs');

var count = 0,
    totalBytes = 0;

function calculateByteSize() {
     fs.readdir(".", function (err, filenames) {
         var i;
         count = filenames.length;

         for (i = 0; i < filenames.length; i++) {
             fs.stat("./" + filenames[i], function (err, stats) {
                 totalBytes += stats.size;
                 count--;
                 if (count === 0) {
                     console.log(totalBytes);
                 }
             });
         }
     });
}

calculateByteSize();
{% endhighlight %}

비동기 코드는 먼저 fs.readdir()을 호출하고 디렉토리의 파일명을 읽어들인다. 그리고 각각의 파일에 대해서 호출되는 콜백함수 안에서 fs.stat()을 호출한다. 여기까지는 예상대로다.

흥미로운 것은 fs.stat()의 콜백함수 안의 총합을 구하는 부분에 있다. 작업을 왼료시킬 기준이 사용되고 있는 것이다. 변수 count는 파일 갯수로 초기화 된 후 콜백이 하나씩 호출될 때마다 감소한다. count가 0이면 모든 I/O의 콜백이 호출되었다는 의미가 된다. 그리고 계산된 총합이 화면에 출력될 것이다.

위 비동기 예제에는 또 하나의 흥미로운 부분이 있다. 바로 클로져다. 클로져는 함수안의 함수인데, 안쪽 함수(inner function)가 바깥쪽 함수(outer function)에 선언된 변수들에 접근이 가능하다. 바깥 함수가 완료된 이후에도 접근할 수 있다. 예제에서도, fs.stat()의 콜백함수는 fs.readdir()의 콜백함수안에 선언되어 있는 count와 totalBytes 변수에 접근하고 있으므로 클로져라고 할 수 있다. 클로져는 자체적으로 컨텍스트를 가진다. 이 컨텍스트 안에 함수에서 접근하는 변수들이 놓이게 된다.

클로져를 사용하지 않는다면, count와 totalBytes 변수를 전역으로 선언해야 한다. 왜냐하면 fs.stat()의 콜백함수는 변수를 위치시킬 컨텍스트를 가지고 있지 않기 때문이다. calculateByteSize()함수고 종료된 이후에는 전역 컨텍스트만이 존재한다. 그래서 클로져를 사용하는 것이다. 변수들을 이 클로져의 컨텍스트안에 위치시키고 함수에서 접근할 수 있는 것이다.
<p style="font-size: 18px;"><strong>코드 재사용 (Code Reuse)</strong></p>
자바스크립트에서는 코드의 조각들을 함수로 감싸면서 재사용할 수 있다. 이 함수는 프로그램의 다른 부분에서도 호출될 수 있다. 이 함수안에서 I/O 작업들이 이루어진다면, 비동기 코드로 변환할 때 몇가지 리팩토링 작업이 필요하다.

다음의 동기 예제에서는 주어진 디렉토리안의 파일 갯수를 리턴하는 countFiles()가 구현되어 있다. countFiles()는 파일의 갯수를 파악하기 위해 fs.readdirSync()라는 I/O 작업을 수행한다. countFiles()는 서로 다른 두가지의 인자를 넘기면서 호출된다.

- 동기 코드
{% highlight js %}
var fs = require('fs');

var path1 = "./",
    path2 = ".././";

function countFiles(path) {
     var filenames = fs.readdirSync(path);
     return filenames.length;
}

console.log(countFiles(path1) + " files in " + path1);
console.log(countFiles(path2) + " files in " + path2);
{% endhighlight %}

- 비동기 코드
{% highlight js %}
var fs = require('fs');

var path1 = "./",
    path2 = ".././",
    logCount;

function countFiles(path, callback) {
     fs.readdir(path, function (err, filenames) {
         callback(err, path, filenames.length);
     });
}

logCount = function (err, path, count) {
     console.log(count + " files in " + path);
};

countFiles(path1, logCount); countFiles(path2, logCount);
{% endhighlight %}

fs.readdirSync()를 비동기 함수인 fs.readdir()로 대체하면 그것을 감싸고 있는 countFiles() 역시 비동기가 된다. countFiles()가 콜백 함수의 결과 값에 영향을 받기 때문에, 결국 결과값은 fs.readdir()이 완료된 이후에 얻을 수 있다. 그래서 countFiles()의 구조를 콜백을 수용할 수 있도록 바꿀 필요가 있다.

console.log를 호출하는 대신 countFiles()를 호출하고 여기서 fs.readdir()을 호출하고 콜백에서 console.log를 호출한다.
<p style="font-size: 18px;"><strong>결론</strong></p>
이 문서는 비동기 프로그래밍의 기초적인 부분에 집중하고 있다. 비동기 프로그래밍에 맞게 당신의 두뇌를 바꾸는 것은 쉽지 않다. 익숙해지는데 시간이 좀 걸릴 것이다. 하지만, 약간의 복잡도가 더해진 대신 병렬 처리에 있어서 놀라운 향상을 그 댓가로 얻을 것이다.
