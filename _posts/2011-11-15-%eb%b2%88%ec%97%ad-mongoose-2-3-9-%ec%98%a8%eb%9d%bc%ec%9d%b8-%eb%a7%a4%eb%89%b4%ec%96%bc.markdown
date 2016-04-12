---
layout: post
title: Mongoose 2.3.9 온라인 매뉴얼
date: '2011-11-15 22:34:52'
---

<p><strong>이 문서는 http://mongoosejs.com의 메인 페이지에서 스터디에 필요한 일부 내용을 번역한 것입니다.</strong></p>
<h3>Mongoose란?</h3>
<p>Mongoose는 비동기 환경에서 작동하게 설계된 MongoDB 객체 모델링 도구다. 모델을 정의하는 것은 다음과 같이 쉽다.</p>
<p> </p>

{% highlight js %}
var Comments = new Schema({
  title     : String
  , body      : String
  , date      : Date
});

var BlogPost = new Schema({
  author    : ObjectId
  , title     : String
  , body      : String
  , buf       : Buffer
  , date      : Date
  , comments  : [Comments]
  , meta      : {
    votes : Number
    , favs  : Number
  }
});

var Post = mongoose.model('BlogPost', BlogPost);
{% endhighlight %}

<h1>설치</h1>
<p>NPM을 통해 설치하는 방법을 추천한다.</p>
<p> </p>

{% highlight shell %}
$ npm install mongoose
{% endhighlight %}

<p>아니면, 다음과 같이 저장소에서 다운로드한 다음, 아래 코드를 통해서 mongoose가 저장된 곳을 지정한다.</p>

{% highlight shell %}
$ git clone git@github.com:LearnBoost/mongoose.git support/mongoose/
{% endhighlight %}

<p> </p>

{% highlight js %}
// in your code
require.paths.unshift('support/mongoose/lib')
{% endhighlight %}

<h3>MongoDB 연결하기</h3>
<p>우선 연결을 정의하는 것이 필요하다. 만약 여러분의 앱이 하나의 데이터베이스만을 사용한다면, mongoose.connect를 이용해라. 추가적인 연결을 생성해야 한다면, mongoose.createConnection를 이용해라. connect와 createConnection 둘다 mongodb://로 시작하는 URI 값이나 host, database, port, options를 인자로 받는다.</p>
<p> </p>

{% highlight js %}
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/my_database');
{% endhighlight %}

<p>일단 연결되면, open 이벤트가 Connection 인스턴스 상에 발생한다. 만약 여러분이 mongoose.connect를 이용했다면, Connection은 mongoose.connection이 된다. 그렇지 않으면, mongoose.createConnection의 리턴값이 Connection 인스턴스이다. (역자주 - 위 예제의 경우는 localhost에 설치된 MongoDB가 연결되고, my_database 라는 데이터베이스가 생성된다. 이것은 mongo 라는 mongodb 쉘 유틸리티를 통해 확인할 수 있다.)</p>
<p>중요! Mongoose는 데이터베이스에 연결될 때 까지 모든 명령들을 버퍼링 해놓는다. 이것은 여러분이 모델을 정의하거나 쿼리를 실행하는 등의 작업을 위해 mongoose가 MongoDB에 연결될 때까지 기다릴 필요가 없다는 것을 의미한다.</p>
<h3>Model(모델) 정의하기</h3>
<p>Model은 Schema 인터페이스를 통해 정의된다. (역자주 - Mongoose의 모델은 MongoDB의 기본 단위인 Document를 의미한다. Model를 저장하는 상위 개념은 Collection이다.)</p>
<p> </p>

{% highlight js %}
var Schema = mongoose.Schema   , ObjectId = Schema.ObjectId;

var BlogPost = new Schema({
  author    : ObjectId
  , title     : String
  , body      : String
  , date      : Date
});
{% endhighlight %}

<p>문서(documents)의 구조와 저장될 데이터의 타입을 정하는 것 이외에도, Schema는 다음에서 나열하는 것들에 대한 정의도 다룬다.</p>
<ul>
<li><a href="http://mongoosejs.com/docs/validation.html">Validators</a> (async and sync)</li>
<li><a href="http://mongoosejs.com/docs/schematypes.html">Defaults</a></li>
<li><a href="http://mongoosejs.com/docs/getters-setters.html">Getters</a></li>
<li><a href="http://mongoosejs.com/docs/getters-setters.html">Setters</a></li>
<li><a href="http://mongoosejs.com/docs/indexes.html">Indexes</a></li>
<li><a href="http://mongoosejs.com/docs/middleware.html">Middleware</a></li>
<li><a href="http://mongoosejs.com/docs/methods-statics.html">Methods</a> definition</li>
<li><a href="http://mongoosejs.com/docs/methods-statics.html">Statics</a> definition</li>
<li><a href="http://mongoosejs.com/docs/plugins.html">Plugins</a></li>
<li><a href="http://mongoosejs.com/docs/populate.html">DBRef-like populate</a></li>
</ul>
<p>다음 예제는 이러한 Schema의 특징을 보여준다.</p>
<p> </p>

{% highlight js %}
var Comment = new Schema({
  name  :  { type: String, default: 'hahaha' }
  , age   :  { type: Number, min: 18, index: true }
  , bio   :  { type: String, match: /[a-z]/ }
  , date  :  { type: Date, default: Date.now }
  , buff  :  Buffer
});

// a setter
Comment.path('name').set(function (v) {
  return capitalize(v);
});

// middleware
Comment.pre('save', function (next) {
  notify(this.get('email'));
  next();
});
{% endhighlight %}

<p>Mongoose 소스 코드에서 examples/schema.js를 살펴봐라. 이 코드는 일반적인 셋업 과정을 설명하고 있다.</p>
<h3>모델에 접근하기</h3>
<p>일단 mongoose.model('ModelName', mySchema) 메서드를 이용해 모델을 정의하면, 우리는 다음과 같은 함수를 이용해서 모델에 접근할 수 있다.</p>
<p> </p>

{% highlight js %}
var myModel = mongoose.model('ModelName');
{% endhighlight %}

<p>또는 다음과 같이 모델에 대한 정의와 접근을 한번에 할 수 있다.</p>

{% highlight js %}
var MyModel = mongoose.model('ModelName', mySchema);
{% endhighlight %}

<p><strong>우리는 모델의 인스턴스를 만들수도 있고, 그것을 저장할 수도 있다. (여기서 모델을 save 하는 방법을 잘 알아두자) </strong></p>

{% highlight js %}
var instance = new MyModel();
instance.my.key = 'hello';
instance.save(function (err) {   // });
{% endhighlight %}

<p>또는 우리는 동일 컬렉션으로부터 document들을 검색할 수 있다.</p>

{% highlight js %}
MyModel.find({}, function (err, docs) {   // docs.forEach });
{% endhighlight %}

<p>이를 위해 findOne, findById, update 등의 메서드를 사용할 수 있다.</p>
<h3>쿼리(Querying)</h3>
<p>문서들은 find, findOne, findById 메서드를 통해 찾을 수 있다. 이런 메서드들은 Model 인스턴스에서 실행된다.</p>
<h4>Model.find 사용하기</h4>

{% highlight js %}
Model.find(query, fields, options, callback) // fields and options can be omitted
{% endhighlight %}

<p> </p>
<p><strong>단순 쿼리</strong> 다음 예제는 some.value=5인 Document를 검색한다.</p>

{% highlight js %}
Model.find({ 'some.value': 5 }, function (err, docs) {
// docs is an array
});
{% endhighlight %}

<p><strong>특정 필드 값 얻기</strong> 다음 예제는 검색한 모든 Document에서 그것들이 생성될 때 디폴트로 만들어진 필드 값(ObjectID)을 출력한다.</p>

{% highlight js %}
Model.find({}, ['first', 'last'], function (err, docs) {
// docs is an array of partially-`init`d documents
// defaults are still applied and will be "populated"
})
{% endhighlight %}

<h4>Model.findOne 메서드 사용하기</h4>
<p>Model.find와 동일하지만, 오직 하나의 Document만이 두번째 인자로 넘긴 콜백함수의 doc 인자로 전달된다. 다음 예제는 age가 5인 Document를 하나만 검색한다.</p>

{% highlight js %}
Model.findOne({ age: 5}, function (err, doc){   // doc is a Document });
{% endhighlight %}

<p> </p>
<h4>Model.findById 메서드 사용하기</h4>
<p>findOne 메서드와 동일하지만, _id 키값을 이용해서 이것과 일치한 Document를 찾아낸다.</p>

{% highlight js %}
Model.findById(obj._id, function (err, doc){   // doc is a Document });
{% endhighlight %}

<p> </p>
