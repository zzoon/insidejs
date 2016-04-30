---
layout: post
title: Node.nodeType
date: '2011-04-05 00:04:44'
---

https://developer.mozilla.org/En/DOM/Node.nodeType

jQuery 소스 분석을 하다보면 nodeType 이라는 프로퍼티를 통해 Node의 타입을 알아내서 처리하는 루틴을 많이 보게 된다.  따라서 이를 위 페이지를 참고하여 정리해 보았다.
<ul>
	<li>Node.ELEMENT_NODE == 1</li>
	<li>Node.ATTRIBUTE_NODE == 2</li>
	<li>Node.TEXT_NODE == 3</li>
	<li>Node.CDATA_SECTION_NODE == 4</li>
	<li>Node.ENTITY_REFERENCE_NODE == 5</li>
	<li>Node.ENTITY_NODE == 6</li>
	<li>Node.PROCESSING_INSTRUCTION_NODE == 7</li>
	<li>Node.COMMENT_NODE == 8</li>
	<li>Node.DOCUMENT_NODE == 9</li>
	<li>Node.DOCUMENT_TYPE_NODE == 10</li>
	<li>Node.DOCUMENT_FRAGMENT_NODE == 11</li>
	<li>Node.NOTATION_NODE == 12</li>
</ul>
&nbsp;
