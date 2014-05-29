---
layout: post
title: Активен ли пользователь? 
keywords: ["js", "user-activity"]
---

В одном из рабочих тасков, у меня возникла несложная задача &mdash; разлогинивать пользователя если он неактивен. В итоге родился небольшой модуль [DetectUserActivity](https://github.com/shaggysmile/UserActivityDetection).

Решение простое. Запускается интервал с заданной задержкой. По истечении заданного таймаута (если он не будет обнулен) выполняется коллбэк `onUserAway`. 

Если пользователь активен, двигает мышкой, кликает, скроллит страницу т.е. совершает какие-либо действия, внутренний счетчик обнуляется. На данный момент, активен это &mdash; `onmousemove`, `onkeypress`, `onscroll`, `onclick`. 

<small>`@TODO:` В идеале этот список нужно расширить и улучшить.</small>

Кстати для обнуления счетчика, в модуле не используется дорогостоящая цепочка clearInterval/setInterval. 

Решение более дешевое, вот фрагмент:

{% highlight javascript %}
/**
 * Check is user away
 *
 * @private
 * @method isUserAway
 * @return {Boolean}
 */
function isUserAway() {
    return getIdleTimestampDifference() >= timerTimeout;
}

/**
 * Get difference between current date and 'timestamp'
 *
 * @private
 * @method isUserActivity
 * @return {Number}
 */
function getIdleTimestampDifference() {
    return new Date() - timestamp;
}
{% endhighlight %}

Пример использования модуля:

{% highlight javascript %}
require(['UserActivityDetection'], function(UserActivityDetection){
    UserActivityDetection.init({
        timerTimeout : 1000,
        timerDelay: 200
    });

    UserActivityDetection.onUserAway = function(timestamp) {
        console.log('onUserAway', timestamp);
    };

    UserActivityDetection.onUserActivity = function(timestamp) {
        console.log('onUserActivity', timestamp);
    };
});
{% endhighlight %}

Исходники и демо смотрите на [Гитхабе](https://github.com/shaggysmile/UserActivityDetection).



