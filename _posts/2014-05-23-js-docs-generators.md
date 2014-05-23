---
layout: post
title: Нелегкий выбор инструмента для генерации документации
keywords: ["js", "jsdoc", "docco", "yuidoc"]
---

Я не буду вдаваться в «философский»  вопрос о необходимости документирования своего кода и наличии к нему документации. Представим идеальную ситуацию &mdash; вы разрабатываете open-source проект и остановились на выборе генератора документации.

Как оказалось я отстал от жизни и на данный момент расплодилось больше десятка генераторов документации. Вот неполный список:

* [JSDoc](http://usejsdoc.org)
* [YUIDoc](http://developer.yahoo.com/yui/yuidoc/)
* [Docco](http://jashkenas.github.com/docco/)
* [Doxx](https://github.com/FGRibreau/doxx)
* [JSDuck](https://github.com/senchalabs/jsduck)
* [SmartComments](http://smartcomments.github.io)
* [NaturalDocs](http://www.naturaldocs.org/)
* [Dojo DocTools](http://dojotoolkit.org/reference-guide/1.8/util/doctools/generate.html)

Мои личные требования к генератору документации. 

1. Поддержка синтаксиса JSDoc. Будем честными, JSDoc стандарт де-факто.

2. Генератор должен быть в виде npm-модуля. Плюс один к карме если существует плагин для Grunt.

3. И последнее, пожалуй самое главное. Сгенерированная документация должна быть удобной и функциональной.


> Из [Википедии](http://ru.wikipedia.org/wiki/Javadoc): JSDoc — генератор документации в HTML-формате из комментариев исходного кода на JavaScript. Синтаксис JSDoc похож на синтаксис [Javadoc](http://ru.wikipedia.org/wiki/Javadoc), который используется для документирования Java кода, но предназначен для работы с языком JavaScript, который является более динамичным, и поэтому JSDoc не совместим с Javadoc. Как и Javadoc, JSDoc позволяет программисту создавать доклеты и теги, которые могут быть выведены в файл, например HTML или RTF.

<small>Рекомендую прочитать [интересный обзор](http://blog.fusioncharts.com/2013/12/jsdoc-vs-yuidoc-vs-doxx-vs-docco-choosing-a-javascript-documentation-generator/), который провел Kaustav Das Modak. С примерами, скриншотами и таблицами.</small>

Мой личный выбор остановился на YUIDoc, как наиболее компромиссном решении из тех что я попробовал. 

Пример задокументированного кода:

{% highlight javascript %}
/**
 * A Graph object with the basic properties
 *
 * @class Graph
 * @constructor
 */
var Graph = function () {

    /** 
     * Object for holding vertices of the Graph
     *
     * @property vertices
     * @type object
     */
    this.vertices = {};
    
    /**
     * Array of edges of the Graph
     * @property edges
     * @type array
     */
    this.edges = [];
    
    /**
     * Length of the Graph, initially set to 0
     * @property length
     * @type number
     */
    this.length = 0;
};

/**
 * Constructor for a vertex of Graph
 *
 * @namespace Graph
 * @class Vertex
 * @constructor
 * @param {string} name Name of the vertex
 * @param {number} value Value of the vertex
 *
 * @property {string} name The name given to the vertex
 * @property {array} edges Edges of the vertex
 * @property {number} value The value of the value argument
 */
Graph.Vertex = function (name, value) {

    /** Store the name argument as a property **/
    this.name = name;
    
    /** Create an empty array for edges */
    this.edges = [];
    
    /** Store the value argument as a property */
    this.value = value;
};

/**
 * Prototype function which returns the number of edges of a {{#crossLink "Graph.Vertex"}}{{/crossLink}}
 *
 * @method degree
 * @return {number} The length of the edges
 */
Graph.Vertex.prototype.degree = function () {
    return this.edges.length;
};

/**
 * Constructor for edge of a {{#crossLink "Graph"}}{{/crossLink}}
 *
 * @namespace Graph
 * @class Edge
 * @constructor
 * @param {Graph.Vertex} tail The tail vertex
 * @param {Graph.Vertex} head The head vertex
 */
Graph.Edge = function (tail, head) {
    this.tail = tail;
    this.head = head;
    tail.edges.push(this);
    head.edges.push(this);
};
{% endhighlight %}

Пример сгенерированной документации: [http://yui.github.io/yuidoc/api/classes/Utils.html](http://yui.github.io/yuidoc/api/classes/Utils.html). 

Удобный поиск, фильтрация, шустрая генерация из под Grunt. YUIDoc идеально подходит для объемного проекта, с большим количеством классов и модулей. 

Но, скажем для jQuery-плагина или небольшой компактной библиотеки, я бы выбрал Docco. [Выглядит вполне симпатично](http://static.fusioncharts.com/samples/articles/js-documentation-generators/docco/docs/source-docco.html). 

Единственно, я никак не могу привыкнуть к inline-комментариям. К примеру из известных библиотек, такого стиля комментировани придерживается [Backbone](https://github.com/jashkenas/backbone/blob/master/backbone.js).

***

Резюмируя. На данный момент существует немалое количество инструментов для генерации документации. И не может не радовать, что есть из чего выбирать.