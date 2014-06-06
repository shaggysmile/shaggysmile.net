---
layout: post
title: Решите, пожалуйста, следующие задачи
keywords: ["js", "яндекс"]
---

В выходные, так как дело было вечером и делать было нечего, я решил совместить приятное с полезным. Представим, что мы претендуем на позицию [Разработчик интерфейсов](http://company.yandex.ru/job/vacancies/dev_int_yaservices.xml) в Яндексе. Попробуем выполнить предложенные задания.

-----------------------

## Вопрос 1
Необходимо передать на сервер набор параметров, не перезагружая при этом страницу браузера. Расскажите, как это сделать: перечислите все возможные способы и назовите самый кроссбраузерный.

***XMLHttpRequest*** &mdash; первое что приходит в голову. 

{% highlight javascript %}
var xhr = new XMLHttpRequest();
xhr.open('POST', '/', true);
xhr.send('someParam=1');
{% endhighlight %}

Можно воспользоваться любой библиотекой вроде jQuery. Вполне кроссбраузерное решение.

***Iframe*** &mdash; гораздо менее кроссбраузерное и эффективное решение. "Хакер мод турн он" :)

{% highlight javascript %}
var iframe = document.createElement('iframe');
iframe.src = '/?someParam=1';
document.body.appendChild(iframe);
{% endhighlight %}

***Динмачиное создание script или img с последующей передачей параметров через атрибут src*** &mdash; вполне кроссбраузерно, но не семантично. 

В случае с <code>img</code>, элемент необходимо удалять после загрузки. В случае с тегом <code>script</code> засоряем <code>head</code>. Но способ конечно джедайский. 

{% highlight javascript %}
var script = document.createElement('script');
script.src = '/?someParam=1';
document.head.appendChild(script);
{% endhighlight %}

***EventSource или WebSockets*** &mdash; экзотичный способ и работает лишь в современных браузерах. На память код не помню :)

***Flash, Silverlight, Java*** и прочие сторонние плагины для браузера. С приходом мобильный эры уже не кроссбраузерно.

В нашем моем топе побеждает <code>XMLHttpRequest<code>, как самое логичное, удобное и кроссбраузерное решение.

-----------------------

## Вопрос 2

Мы уделяем большое внимание производительности наших приложений. Нередко случается, что отдельно взятая функция (например, обработчик события) работает достаточно долго, а вызывается часто, что сильно ухудшает отзывчивость интерфейса.

Предложите общее решение, позволяющее вызывать любую функцию не чаще N раз в секунду, даже если её вызов происходит чаще. 

Задание само по себе несложное. Манипуляции с очередью и таймаутом. <code>delay</code> в одну секунду я сделал опциональным:

{% highlight javascript %}
/**
 * Helper function calling any function no more N times per specified delay
 *
 * @param {Function} obj
 * @param {Number} frequency of call function
 * @param {Number} delay in milliseconds
 * @return {Function}
 */
function delayFuncByFrequencyOfCall(obj, frequency, delay) {

    // Check type of obj
    if(!(obj && obj.constructor && obj.call && obj.apply))
        throw new Error(obj+' is not a function');

    // Set default values
    var totalFrequencyOfCall = frequency || 5;
    var delayTime = delay|| 1000;

    var timeout = null;
    var frequencyOfCallCounter = 0;
    var isNextFuncCall = true;

    return function() {
        if(isNextFuncCall ){
            if(frequencyOfCallCounter<totalFrequencyOfCall) {
                frequencyOfCallCounter+=1;
                obj();
            } else {
                isNextFuncCall = false;
                frequencyOfCallCounter = 0;
                timeout = setTimeout(function(){
                    isNextFuncCall = true;
                }, delayTime);
            }
        }
    }
}

// Some handler
function onSomeButtonHandler() {
    console.log('Click on some button!');
}

var someHandler = delayFuncByFrequencyOfCall(onSomeButtonHandler, 1, 5000);
$('body').on('click', someHandler);
{% endhighlight %}

-----------------------

## Вопрос 3

Напишите функцию, которая из произвольного входящего массива выберет все комбинации чисел, сумма которых будет равняться 10.

Признаться, не очень люблю такие задачи и наверняка есть более оптимальное решение.

<small><code>@TODO:</code>Полистать учебник по алгоритмам.</small>

### Подумаем

В первую очередь я бы уменьшил количество интерации за счет проверки уникальности элементов массива. Но сделаем это опционально, как и поиск произвольной суммы.

1. Обегаем массив.
2. Если текущий элемент массива больше или равен нужной нам сумме выходим из интерации цикла (если равен, то заносим во временный массив, который мы будем возвращать в результате функции).
3. Далее, с помощью рекурсии начинаем поиск комбинаций исключая текущее значение.
4. В итоге объединяем результат и возвращем массив найденных комбинаций.

{% highlight javascript %}
/**
 * Find sum combinations from specified array
 *
 * @param {Array} array
 * @param {Number} targetSum
 * @param {Boolean} isUniq
 * @return {Array}
 */
function findSumCombinations(array, targetSum, isUniq) {
    var results = [];
    var array = isUniq && getUniqArrayElements(array) || array;

    if (array.length) {
        for (var i = 0; i < array.length; i++) {
            var item = array[i];
            if (item > targetSum) continue;
            if (item === targetSum) {
                results.push([item]);
                continue;
            }
            var subResults = findSumCombinations(array.slice(i + 1), targetSum - item, isUniq);
            if (subResults.length) {
                for (var j = 0, k = subResults.length; j < k; j++) {
                    results.push([item].concat(subResults[j]));
                }
            }
        }
    }

    function getUniqArrayElements(array) {
        for (var i = array.length - 1; i > 0; i--) {
            var hasElement = array.indexOf(array[i]);
            if (hasElement != -1 && hasElement != i) array.splice(i, 1);
        }
        return array;
    }

    return results;
}

console.log(findSumCombinations([ 1, 5, 6, 4, 2, 8], 10, true));
{% endhighlight %}

### Итого

Вот и все. Задания выполнены, отправляем в Яндекс, ждем приглашение на собеседование :)

P.S. Я думаю Яндексу давно пора обновить задания в вакансиях. 



