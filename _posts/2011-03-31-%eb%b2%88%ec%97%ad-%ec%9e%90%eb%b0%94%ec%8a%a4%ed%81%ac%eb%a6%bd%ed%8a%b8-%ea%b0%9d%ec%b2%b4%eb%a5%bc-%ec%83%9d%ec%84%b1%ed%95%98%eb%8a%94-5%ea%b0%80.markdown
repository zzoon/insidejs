---
layout: post
title: '[번역] 자바스크립트 객체를 생성하는 5가지 방법'
date: '2011-03-31 10:02:29'
---

원본글 : <a href="http://javascriptweblog.wordpress.com/2010/03/16/five-ways-to-create-objects/#more-21">http://javascriptweblog.wordpress.com/2010/03/16/five-ways-to-create-objects/#more-21</a>
<h1>Five ways to create objects…</h1>
<h3>1. Simple Object Literal</h3>
{}를 통해 빈 객체를 생성한 다음, 프로퍼티를 추가해 나가는 방법

[sourcecode language="javascript"]
myApp.notepad  = {};
myApp.notepad.writeable = true;
myApp.notepad.font  = 'helvetica';
myApp.notepad.setFont = function(theFont) {
    myApp.notepad.font = theFont;
}
[/sourcecode]
<h3>2. Nested Object Literal</h3>
{} 내부에 프로퍼티를 미리 정의한 다음 객체를 생성하는 방법

[sourcecode language="javascript"]
myApp.notepad  = {
    writeable: true,
    font: 'helvetica',
    setFont: function(theFont) {
        this.font = theFont;
    }
}
[/sourcecode]
<h3>3. Constructor using Object Literal</h3>
{}를 통해 객체를 만드는 함수를 만들고, 이를 호출하는 방식으로 객체를 생성하는 방법

[sourcecode language="javascript"]
myApp.Notepad = function(defaultFont) {
    var  that = {};
    that.writeable = true;
    that.font = defaultFont;
    that.setFont = function(theFont) {
        that.font = theFont;
    }
    return that;
}

myApp.notepad1 =  myApp.Notepad('helvetica');
[/sourcecode]
<h3>4. Simple Constructor for new</h3>
생성자 함수를 만든 다음, new를 통해 객체를 생성하는 방법

[sourcecode language="javascript"]
myApp.Notepad = function(defaultFont) {
    this.writeable = true;
    this.font = defaultFont;
    this.setFont = function(theFont) {
        this.font = theFont;
    }
}

myApp.notepad1  = new myApp.Notepad('helvetica');
[/sourcecode]
<h3>5. Prototype with Constructor for new</h3>
Prototype을 가진 생성자 함수와 new를 이용해서 객체를 생성하는 방법.

[sourcecode language="javascript"]

myApp.Notepad = function(defaultFont) {
    this.font = defaultFont;
}
myApp.Notepad.prototype.writeable  = true;
myApp.Notepad.prototype.setFont  = function(theFont) {
    this.font = theFont;
}

myApp.notepad1  = new myApp.Notepad('helvetica');
[/sourcecode]