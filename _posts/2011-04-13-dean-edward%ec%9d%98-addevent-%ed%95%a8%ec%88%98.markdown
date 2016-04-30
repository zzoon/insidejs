---
layout: post
title: Dean Edward의 addEvent 함수
date: '2011-04-13 22:01:48'
---

jQuery의 이벤트 핸들러 등록 방식을 이해하기 위하여, Dean Edwards의 addEvent 함수를 이해해보자.

소스 주석에도 설명되어 있지만, jQuery의 이벤트 핸들러 등록 코드는 이 함수에서 많은 아이디어를 얻었다고 한다. 이와 관련된 문서의 주소는 http://dean.edwards.name/weblog/2005/10/add-event/ 이다.

주의할 점은 이 코드는 2005년에 작성된 것이므로, 조금은 오래된 방식이 쓰이고 있다.
그럼 코드를 보자.

<span style="font-family: Consolas, Monaco, 'Courier New', Courier, monospace;font-size: 12px;line-height: 18px"> </span>

{% highlight js %}
function addEvent(element, type, handler) {
    // assign each event handler a unique ID
    if (!handler.$$guid) handler.$$guid = addEvent.guid++;
    // create a hash table of event types for the element
    if (!element.events) element.events = {};
    // create a hash table of event handlers for each element/event pair
    var handlers = element.events[type];
    if (!handlers) {
        handlers = element.events[type] = {};
        // store the existing event handler (if there is one)
        if (element["on" + type]) {
            handlers[0] = element["on" + type];
        }
    }

    // store the event handler in the hash table
    handlers[handler.$$guid] = handler;

    // assign a global event handler to do all the work
    element["on" + type] = handleEvent;
};

// a counter used to create unique IDs
addEvent.guid = 1;

function removeEvent(element, type, handler) {
    // delete the event handler from the hash table
    if (element.events && element.events[type]) {
        delete element.events[type][handler.$$guid];
    }
};
{% endhighlight %}

addEvent 함수의 인자는 (DOM element, 이벤트 타입, 핸들러)이다.

3라인: $$guid라는 프로퍼티에 유니크한 아이디를 할당한다.

5라인: DOM element 객체의 events라는 프로퍼티에 빈 객체를 할당한다.

7라인: handlers라는 변수는 element.events[type]을 가리키도록 한다.

8-15라인: 현재는 element.events[type]이 undefined 이다. 따라서 여기에 빈객체가 할당된다. 이미 등록되어 있는 핸들러가 있다면 handlers[0]가 이 기존의 이벤트 핸들러를 가리키게 된다.

17라인: 3라인에서 할당된 아이디를 인덱스로 하여 사용자가 등록한 핸들러를 handlers에 등록하게 된다.

19라인: 실제적으로 DOM element객체의 "onXXXX"에 등록되는 핸들러는 handleEvent 함수이다. 이 코드를 통해 실제 이벤트가 발생할 때 불리우는 함수는 바로 handleEvent 함수가 되는 것이다.

그렇다면 handleEvent 함수의 코드를 살펴보자.

{% highlight js %}
function handleEvent(event) {
    // grab the event object (IE uses a global event object)
    event = event || window.event;
    // get a reference to the hash table of event handlers
    var handlers = this.events[event.type];
    // execute each event handler
    for (var i in handlers) {
        this.$$handleEvent = handlers[i];
        this.$$handleEvent(event);
    }
};
{% endhighlight %}

여기서 this는 이벤트가 발생한 DOM Element이고, 인자로 넘어오는 event는 현재 발생한 해당 이벤트를 가리킨다.
3라인 : IE를 위한 코드로서 IE에서는 콜백에 event가 넘어오지 않고, window.event를 통해 접근할 수 있다.

5라인 : event.type을 통해 events 객체에서 기존에 등록된 (addEvent를 통해 등록된) 핸들러를 받는다.

7~10라인 : handlers의 각 요소들(핸들러)을 받아서 실행한다.

위에서 설명한 내용을 이해하기 쉽게 그림을 그려보면 다음과 같다.

![]({{ site.url }}/assets/addevent2.png)

요약하면, addEvent를 통해 특정 DOM 요소의 특정 이벤트에 대한 핸들러를 등록하면 이는 그 DOM요소의 events 프로퍼티에 하나씩 등록이 된다. 그래서 해당 이벤트가 발생하면 handleEvent 함수를 통해 Dom요소의 events 프로퍼티에 등록된 핸들러들이 하나씩 호출되는 것이다. 이렇게 하면, 하나의 이벤트에 대하여 여러 핸들러를 차례로 호출할 수 있다. removeEvent에 대한 설명은 생략하겠다.

jQuery는 이 아이디어를 그대로 이어받아서 이벤트 핸들러 등록에 대한 구현을 하였다.
