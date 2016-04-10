---
layout: post
title: Mongoose 온라인 매뉴얼 - Virtual Attributes
date: '2011-12-08 09:02:07'
---

<p><strong>이글은 www.mongoosejs.com에 게재된 mongoose 온라인 매뉴얼에서 <a href="http://www.facebook.com/octoberskyjs" target="_blank">Octobersky.js</a> 스터디와 연관된 부분을 번역한 것입니다.</strong></p>
<h1>Virtual attributes</h1>
<p>Mongoose는 가상(virtual) 속성(attributes)을 지원한다. 가상 속성은 mongodb에 지속적으로 유지되지 않는 편리한 속성을 의미한다.</p>
<p>다음 예제를 보면 쉽게 이해할 수 있다.</p>
<h4>Example</h4>
<p>다음 스키마를 살펴보자.</p>
<p>[code lang="js"] var PersonSchema = new Schema({     name: {         first: String       , last: String     } });</p>
<p>var Person = mongoose.model('Person', PersonSchema);</p>
<p>var theSituation = new Person({ 	name: { first: 'Michael', last: 'Sorrentino' } }); [/code]</p>
<p>여러분이 theSituation의 전체 이름을 쓰고 싶다면, 다음과 같이 할 수 있다.</p>
<p>[code lang="js"] console.log(theSituation.name.first + ' ' + theSituation.name.last); [/code]</p>
<p>여기서 name.full 이라는 가상 속성을 정의하면 다음과 같이 좀더 편리하게 작업할 수 있다:</p>
<p>[code lang="js"] console.log(theSituation.name.full); [/code]</p>
<p>이를 위해서, 여러분은 Schema에 다음과 같이 Person이라는 가상 속성을 선언할 수 있다.</p>
<p>[code lang="js"] PersonSchema .virtual('name.full') .get(function () {   return this.name.first + ' ' + this.name.last; }); [/code]</p>
<p>이제 다음과 같이 name.full에 접근할 수 있다.</p>
<p>[code lang="js"] theSituation.name.full; [/code]</p>
<p>다음과 같은 getter 함수를 호출하는 것으로 구현은 마무리 된다.</p>
<p>[code lang="js"] function () {   return this.name.first + ' ' + this.name.last; } [/code]</p>
<p>그리고 이것을 리턴한다.</p>
<p>this.name.full을 설정함으로써 this.name.first와 this.name.last에 해당 설정이 적용되는 것은 굉장히 유용하다. 예를 들어 우리가 theSituation의 name.first와 name.last를 각각 'The'와 'Situation'으로 수정하고 싶다면, 다음과 같이 호출하면 된다:</p>
<p>[code lang="js"] theSituation.set('name.full', 'The Situation'); [/code]</p>
<p>Mongoose는 이러한 작업을 가상 속성 setter를 통해서도 허용한다. 다음과 같이 가상 속성 setter를 정의할 수 있다:</p>
<p>[code lang="js"] PersonSchema .virtual('name.full') .get(function () {   return this.name.first + ' ' + this.name.last; }) .set(function (setFullNameTo) {   var split = setFullNameTo.split(' ')     , firstName = split[0]     , lastName = split[1];</p>
<p>this.set('name.first', firstName);   this.set('name.last', lastName); }); [/code]</p>
<p>이제 setter를 호출하자:</p>
<p>[code lang="js"] theSituation.set('name.full', 'The Situation'); [/code]</p>
<p>그리고 document를 저장하면 mongodb에서 name.first와 name.last는 변경될 것이다. 하지만, mongodb document는 name.full 키 또는 값을 데이터베이스에 유지하지 않을 것이다.</p>
<p>[code lang="js"] theSituation.save(function (err) {   Person.findById(theSituation._id, function (err, found) {     console.log(found.name.first); // 'The'     console.log(found.name.last);  // 'Situation'   }); }); [/code]</p>
<p>만일 여러분이 mongodb에 저장되지 않을 속성을 사용하고자 한다면, 가상 속성을 이용해라.</p>