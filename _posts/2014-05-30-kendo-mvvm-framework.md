---
layout: post
title: Kendo UI как MVVM-фреймворк
keywords: ["js", "kendo ui", "mvvm"]
---

Волей судеб, мне пришлось поработать с [Kendo UI](http://www.telerik.com/kendo-ui), отличным фреймворком разработанной компанией Telerik. В состав входит более 70 виджетов (Charts, Diagram, Editor, Grid, Scheduler, TreeView, Upload и т.д.), в основе которых лежит jQuery. 

Плюс к этому в Kendo UI есть поддержка мобильных устройств, неплохая кастомизация (из коробки 11 предустановленных тем) и MVVM-фреймворк. О MVVM-фреймворке мы и поговорим в этой статье.

Ко всем этим плюсам, есть один большой жирный минус в виде цены. Пакет [Kendo UI Professional](http://www.telerik.com/purchase/kendo-ui) стоит $699.

<small>Но не будем опускать руки, существует и open-source версия &mdash; [Kendo UI Core](http://www.telerik.com/download/kendo-ui-core), выпущенная под лицензией Apache 2.0, включающая в себя 40 виджетов и весь необходимый нам core-функционал.</small> 

### MVVM
#### Краткая история
Итак, [MVVM](http://ru.wikipedia.org/wiki/Model-View-ViewModel) (Model-View-ViewModel) — архитектурный шаблон программирования, основанный на MVC. Ключевое различие между ними состоит в том, что MVVM добавляет абстракцию View — ViewModel, которая занимается отслеживанием изменений в данных модели и их отображением в представлении (data-binding).

Концепт MVVM был [представлен Джоном Гроссманом](http://blogs.msdn.com/b/johngossman/), в 2005-м году и использовался в Windows Presentation Foundation ([WPF](http://ru.wikipedia.org/wiki/Windows_Presentation_Foundation)) и [Silverlight](http://www.microsoft.com/silverlight/). 

Немалую популярность обрел в сообществе Adobe Flex, в качестве альтернативы MVC. Вот как раз-таки тогда я впервые познакомился с MVVM. 

А второй практический опыт был уже [Knockout](http://knockoutjs.com). Не ошибусь, если это первый JavaScript MVVM-фреймворк. 

На данный момент их уже целых три:

* [Knockout](http://knockoutjs.com)
* [Kendo UI](http://docs.telerik.com/kendo-ui/getting-started/framework/mvvm/overview)
* [Knockback](https://github.com/kmalakoff/knockback)


### Kendo MVVM
#### Model

Модели, абстрактная основа любого MV*-приложения, отвечающая за большую часть соответствующей логики: конвертации, валидации, хранение свойств и контроль доступа. Kendo UI предоставляет  базовый набор функциональности для работы с моделями:

{% highlight javascript %}
var Product = kendo.data.Model.define( {
    id: "id", // the identifier is the "id" field (declared below)
    fields: {
        /* name of the field */ name: {
            type: "string", // the field is a string
            validation: { // validation rules
                required: true // the field is required
            },
            defaultValue: "<empty>" // default field value
        },

        /* name of the field */ price: {
            type: "number", // the field is a number
            validation: { // validation rules
                required: true, // the field is required
                min: 1 // the minimum value is 1
            },
            defaultValue: 99.99 // default field value
        },

        /* name of the field */ id: {
            editable: false, // this field is not editable
            nullable: true // a default value will not be assigned
        }
    }
});
var product = new Product();
console.log(product.get("price")); // outputs "99.99" which is the default value
{% endhighlight %}

Субъективно, но в отличии от Knockout, в Kendo UI сущность модели реализованна гораздо лучше. Более подробно можно почитать в [документации](http://docs.telerik.com/kendo-ui/api/framework/model).

Если мы говорим про модели, то нельзя не упомянуть о мощном компненте [DataSource](http://docs.telerik.com/kendo-ui/api/framework/datasource), предназначенный для работы с коллекцией моделей. Также есть  компонент [HierarchicalDataSource](http://docs.telerik.com/kendo-ui/api/framework/hierarchicaldatasource) унаследованный от  DataSource, для работы с вложенной иерархией моделей. 

<small>Примечание: Я намеренно не вдаюсь в подробное описание <code>kendo.data.DataSource</code>, так как это тема отдельной статьи.</small>

Только два кита в виде <code>kendo.data.Model</code> и <code>kendo.data.DataSource</code>, плавающие в ядре Kendo UI, дают огромное преимущество перед Knockout.

#### ViewModel

Ключевым компонентом MVVM является ViewModel. Если совсем вкратце, то задача ViewModel передать данные из модели (<code>kendo.data.Model</code>) в представление (view). 

Рассмотрим пример. ViewModel в Kendo UI:

{% highlight javascript %}	
var viewModel = kendo.observable({
    someProp: "MVVM",
    displayGreeting: function() {
        var name = this.get("someProp");
        console.log("Hello, " + someProp + "!!!");
    }
});
{% endhighlight %}

#### View

Рассмотрим представление:

{% highlight html %}	
<div class="kendo-view">
    <input data-bind="value: someProp" />
    <button data-bind="click: displayGreeting">Display Greeting</button>
</div>
{% endhighlight %}

<small>Обратите внимание, хоть data-bind и выглядит как обычный JavaScript, но он таковым не является. К примеру <code>data-bind="text: someProp.toLowerCase()"</code> не сработает и выдаст синтаксическую ошибку. Более подробно о data-binding см. [здесь] (http://docs.telerik.com/kendo-ui/getting-started/framework/mvvm/bindings).</small>

И собственно сам data-binding:

{% highlight javascript %}	
kendo.bind($(".kendo-view"), viewModel);
{% endhighlight %}

Вот и все, наше первое MVVM-приложение готово! Конечно пример совсем простой, но он носит лишь демонстративный характер и надеюсь дает представление о там как работает связь View и ViewModel в Kendo UI. 

В следующих частях поговорим более подробно о DataSource, о встроенном шаблонизаторе (кстати шаблонизатор довольно [шустрый](http://docs.telerik.com/kendo-ui/getting-started/framework/templates/performance)) и роутинге. 

Отдельной главой можно рассказать о поддержке [SPA](http://docs.telerik.com/kendo-ui/getting-started/framework/spa/overview) (Single Page Application). 

-----------

Резюмируя. Странно, что Kendo UI, учитывая его open-source лицензию, не получил такого широкого распространения у разработчиков как Knockout. На мой взгляд Kendo UI, гораздо более зрелое решение в ряду других MVVM-фреймворков.

### Ссылке по теме

* [Документация Kendo UI](http://docs.telerik.com/kendo-ui)
* [Сравнение Kendo UI и Knockout](http://www.scottlogic.com/blog/2013/04/08/comparing-kendoui-and-knockout-with-a-bit-of-jquerymobile-on-the-side.html)
* [MVVM-руководство для JavaScript-разработчиков](http://addyosmani.com/blog/understanding-mvvm-a-guide-for-javascript-developers/)


