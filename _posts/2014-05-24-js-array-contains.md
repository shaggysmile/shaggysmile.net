---
layout: post
title: Функция Array.contains 
keywords: ["js", "array", "contains"]
---

Недавно QA-щик спросил, если в JS для массивов аналог функции contains. Первое что мне пришло в голову, воспользуйся jQuery  <code>$.inArray(value, array)</code> или underscore <code>_.contains(list, value)</code>.

Но потом вспомнил изящный и нативный метод <code>array.indexOf()</code>:

{% highlight javascript %}
var array = [1, 2, 3];
var index = array.indexOf(2); // 1
index = array.indexOf(10); // -1
{% endhighlight %}

Единственным недостатком является только то, что метод <code>indexOf</code> был добавлен в [5-ой редакции ECMA-262](http://www.ecma-international.org/ecma-262/5.1/#sec-15.4.4.14) и данное решение будет работать не во всех браузерах. 

Ложкой дегтя как обычно оказался InternetExplorer. Метод <code>indexOf</code> поддерживается лишь с 9-й версии.

Кроссбраузерное решение от [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf): 

{% highlight javascript %}
if (!Array.prototype.indexOf) {
    Array.prototype.indexOf = function (searchElement, fromIndex) {
      if ( this === undefined || this === null ) {
        throw new TypeError( '"this" is null or not defined' );
      }

	  // Hack to convert object.length to a UInt32
      var length = this.length >>> 0; 
      fromIndex = +fromIndex || 0;

      if (Math.abs(fromIndex) === Infinity) {
        fromIndex = 0;
      }

      if (fromIndex < 0) {
        fromIndex += length;
        if (fromIndex < 0) {
          fromIndex = 0;
        }
      }

      for (;fromIndex < length; fromIndex++) {
        if (this[fromIndex] === searchElement) {
          return fromIndex;
        }
      }

      return -1;
    };
  }
{% endhighlight %}

Профит! На сладкое советую почитать топик на [StackOverflow](http://stackoverflow.com/questions/237104/array-containsobj-in-javascript).


