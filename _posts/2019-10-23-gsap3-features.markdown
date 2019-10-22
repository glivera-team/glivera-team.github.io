---
layout: post
title:  "Gsap5 новые фичи"
excerpt: "Creating a stylegide with sc5"
keywords: "layout, frontend, design, sc5, styleguide, html, css, javascript"
date:   2019-10-23 11:55:00 +0300
categories: structure
lang: ru
autor:  glivera-team
---

Gsap3 принес с собой очень много разных полезных нововведений, на этой странице вы можете познакомится с некоторыми из них.

## Упрощенное API:

<b>1.</b> TweenMax, TweenLite, TimelineLite, TimelineMax, больше не нужно писать. Теперь оно все объединено в один объект <b>gsap.</b>

{% highlight javascript %}

// аналог старой версии TweenMax.to(...)
gsap.to(".class", {duration:2, x:100});

// создание таймлайна
var tl = gsap.timeline();
tl.to(".class", {duration:2, x:100});

{% endhighlight %}

Внутри существует один <b>Tween</b> класс, который заменяет TweenLite/TweenMax и Timeline класс который заменяет TimelineLite/TimelineMax, они оба имеют все функции такие как repeat, yoyo и т.д. Когда вы вызываете один из gsap методов например таких как .to() .from() и т.к, он возвращает экземпляр соответствующего класса с легко сцепляемыми методами.

<b>2.</b> продолжительность анимации объявляется внутри объекта со свойствами (старый синтаксис по прожнему работает)

Это дает несколько преимуществ:

1. Лучшая читаемость
2. Это намного лучше подходит для keyframes
3. Он позволяет наследовать длительность по умолчанию
4. Вы можете использовать функциональные значения

{% highlight javascript %}

// Старый способ - продолжительность указывалась вторым параметром
TweenMax.to(".class", 1, {x:100});

// Новый способ - продолжительность анимации является свойством объекта vars
gsap.to(".class", {duration:1, x:100});

{% endhighlight %}

### 3. Сокращенная запись изингов  - меньше набирать текста, лучше читается и нет проблем с импортом. Вот теперь как пишим изинги:

{% highlight javascript %}

// Старый способ - продолжительность указывалась вторым параметром
TweenMax.to(".class", 1, {x:100});

// Новый способ - продолжительность анимации является свойством объекта vars
gsap.to(".class", {duration:1, x:100});

// Старый ==> Новый
Elastic.easeOut ==> "elastic.out" // или просто "elastic" потому что".out" стоит по умолчанию
Elastic.easeIn ==> "elastic.in"
Elastic.easeInOut ==> "elastic.inOut"
Elastic.easeOut.config(1, 0.5) ==> "elastic.out(1, 0.5)" // или просто "elastic(1, 0.5)"

// и другие настройки для изингов теперь писать гораздо проще
SteppedEase.config(5) ==> "steps(5)"
SlowMo.ease.config(0.5, 0.8) ==> "slow(0.5, 0.8)"
RoughEase.ease.config({points:40}) ==> "rough(40)"
ExpoScaleEase.config(0.5, 3) ==> "expoScale(0.5, 3)"

{% endhighlight %}

## Значения по умолчанию

{% highlight javascript %}

gsap.timeline({defaults:{ease:"back", duration:2}})
    .to(".class-1", {x:100}) // наследует ease и duration от родителя
    .to(".class-2", {y:200}) // так же наследует эти свойства

{% endhighlight %}

Теперь не нужно указывать повторяющиеся свойства каждому блоку отдельно, достаточно добавить их родителю в defaults и значение будет скопировано всем дочерним элементам.

## Stagger(пошаговые) анимации

Больше нет необходимости в старых методах staggerTo()/staggerFrom()/staggerFromTo(), вы можете добавить пошаговую анимацию таким образом:

{% highlight javascript %}

gsap.to(".class", {
  x:"+=100",
  duration:1,
  stagger: 0.5 //simple stagger of 0.5 seconds
});

{% endhighlight %}

Предыдущие методы также все еще поддерживаются.

## MotionPathPlugin (заменяет BezierPlugin)

{% highlight javascript %}

gsap.to("#rect", { motionPath: "#path", duration: 5 });
MotionPathHelper.create("#rect")

{% endhighlight %}

Позволяет превратить любой path из SVG в траекторию движения.
Также вы можете воспользоваться утилитой MotionPathHelper для создания собственного path.

## Random()

Определяет случайное значение либо из диапазона (пример random(-100, 100)), либо из массива значений (пример random([red, blue, green])). Так же можно округлит полученное значение к нужному числу.

{% highlight javascript %}

gsap.to(".class", {
  x:"random(-100, 100, 5)" //chooses a random number between -100 and 100 for each target, rounding to the closest 5!
});

{% endhighlight %}

## getProperty()

Возвращает свойство елемента с возможность преобразования едениц измерения.

{% highlight javascript %}

gsap.getProperty("#myElement", "backgroundColor");
gsap.getProperty("#element", "width", "em"); // вернет ширину в em

{% endhighlight %}

## Effects

Вы можете создать необходимую вам анимацию и сохранить ее под удобным вам именем

{% highlight javascript %}

// создаем эффект с именем fade
gsap.registerEffect({
    name: "fade",
    defaults: {duration:2}, 
    effect: (targets, config) => {
        return gsap.to(targets, {duration: config.duration, opacity:0});
    }
});

// используем эффект с именем fade:
gsap.effects.fade(".box");

{% endhighlight %}

## Keyframes

{% highlight javascript %}

gsap.to(".class", {keyframes: [ 
  {x:100, duration:1},
  {Y:200, duration:1, delay:0.5},
  {rotation:360, duration:2, delay:-0.25}
]});

{% endhighlight %}

Позволяет передать все значения для анимации в одном массиве вместо создания множества блоков для анимации.

## Префиксы "<" и ">"

Используется при необходимости разместить время начала следующей анимации относительно начала или конца предыдущей анимации

"<" указывает на время начала последней добавленной анимации
">" указывает на время окончания последней  добавленной анимации

{% highlight javascript %}

var tl = gsap.timeline();
tl.to(...)
  .to(..., "") // начинается сразу после времени окончания предыдущей анимации
  .to(..., ">-0.5") // перекрывается с предыдущей анимацией на 0,5 секунды

{% endhighlight %}

## Анимация к значениям "auto" у width/height

Это звучит просто, но это может быть очень удобно, когда вы расширяете что-то до размера, который определяет содержимое блока.

## repeatRefresh

## then() 

Если вы предпочитаете изпользовать Promise вместо onComplete, теперь можете использовать then() который вернет Promise.

{% highlight javascript %}

gsap.to(".class", {duration:1, x:100}).then(yourFunction).then(...);

{% endhighlight %}

## SnapPlugin

Заменяет <b>RoundPropsPlugin</b>, но с более интуитивным названием и большим количеством функций. Добавляет модификатор к любому свойству, которое поддерживает пошаговую анимацию.

## Доступ/контроль над глобальной временной шкалой

{% highlight javascript %}

gsap.globalTimeline.timeScale (0,1); // замедляем все
gsap.globalTimeline.pause (); // остановим все, хотя вы можете использовать вместо этого gsap.exportRoot(), чтобы исключить delayedCalls()

{% endhighlight %}

## translateX, translateY, rotate, rotateX, and rotateY

Для лучшего соответствия имен, которые используются большинством разработчиков в CSS, "translateX", "translateY", "rotate", "rotateX" и "rotateY" могут использоваться вместо 
x, y, rotation, rotationX, and rotationY.

{% highlight javascript %}

gsap.to(".class", {
  translateX: 100, // same as x:100
  translateY: 100, // same as y:100
  rotate: 360      // same as rotation:360
});

rotation:"270_short" // анимация по кратчайшему направлению
rotation:"270_cw"    // анимация по часовой стрелке
rotation:"270_ccw"   // анимация против часовой стрелки

{% endhighlight %}

## Единицы измерения для x/y трансформаций

{% highlight javascript %}

gsap.to(".class", {
  x:"50vw", // без указания единиц используются 'px'
  y:"5em"
});

{% endhighlight %}

<b>GSAP 3.0</b> больше не использует matrix() и matrix3d() для CSS-преобразований, по этому стали доступны другие единицы измерений.

## Полезные методы 

<b>16.1 gsap.utils.random()</b> - Определяет случайное значение либо из диапазона, либо массива

{% highlight javascript %}

// случайное число от -100 до 100
let num = gsap.utils.random (-100, 100);

// случайное значение из массива
let random = gsap.utils.random (["red", "blue", "green"]);

// случайное число в диапазоне от -100 до 100, привязанное к ближайшему приращению 5
let num = gsap.utils.random (-100, 100, 5);

// используем 4-й параметр (логический), чтобы вернуть функцию вместо числа
let randomizer = gsap.utils.random (-100, 100, 5, true);

{% endhighlight %}

<b>16.2 gsap.utils.snap()</b>

<b>16.3 gsap.utils.wrap()</b> - циклично проходит по массиву, если число превышает длину - переносится на начало, а если меньше 0, то идет с конца

{% highlight javascript %}

let num = gsap.utils.wrap (["red", "green", "blue"], 4); // "зеленый" (индекс 4 перенесется на индекс 1 в массиве из 3 элементов)

По этому же принципу используется метод cycle:
let wrapper = gsap.utils.cycle(["red", "green", "blue"]);
let num = wrapper(4) // "green"

{% endhighlight %}

<b>16.4 gsap.utils.toArray()</b> - преобразует текст селектора (".class"), массив ([el1, el2, el3]), NodeList (document.querySelectorAll(".Class")), объект ({prop: 0}) или почти любой массив -подобный объект (даже результат jQuery) в массив. Это отличный способ просто нормализовать вещи и убедиться, что они аккуратно упакованы в массив.

<b>16.5 gsap.utils.distribute()</b> - -------

<b>16.6 gsap.utils.getUnit()</b> - выносит в строку еденицы измерения у заданного значения

{% highlight javascript %}

gsap.utils.getUnit("50%"); // "%"
gsap.utils.getUnit("100vw"); // "vw"

{% endhighlight %}

<b>16.6 gsap.utils.clamp()</b> - проверяет находится ли заданное значение(3-й параметр) в заданном диапазоне(1-2 параметров) и возвращает либо само значение, либо ближайшее допустимое значение из диапазона.

{% highlight javascript %}

let num = gsap.utils.clamp(0, 100, 105); // 100
let num = gsap.utils.clamp(0, 100, -50); // 0
let num = gsap.utils.clamp(0, 100, 20);  // 20

Если не указать значение(3-й параметр) будет возвращена функция
let clamper = gsap.utils.clamp(0, 100);
let num = clamper(-10); //0

{% endhighlight %}

<b>16.7 gsap.utils.mapRange()</b> - пропорционально переносит значение из первого диапазона во второй

{% highlight javascript %}

let num = gsap.utils.mapRange(-10, 10, 100, 200, 0); // 150

//Если не указать значение(5-й параметр) будет возвращена функция
let mapper = gsap.utils.mapRange(-10, 10, 100, 200);
let num = mapper(0); // 150

{% endhighlight %}

<b>16.8 gsap.utils.interpolate()</b> - линейно интерполирует между почти любыми двумя значениями, учитывая предоставленный прогресс (между 0 и 1, где 0,5 - это половина между). Интерполировать можно цисла, строки(при условии, что в них есть совпадающие количества чисел) или даже обьекты.

{% highlight javascript %}

// числа
let value = gsap.utils.interpolate(0, 200, 0.5); // 100 (halfway between 0 and 200)

//числа(функция)
let interp = gsap.utils.interpolate(0, 200); // <- returns a function since value (3rd parameter) was omitted
value = interp(0.5); // 100

// строки
let interp = gsap.utils.interpolate("20px", "40px");
value = interp(0.5); // 30px

// цвета
let interp = gsap.utils.interpolate("red", "blue");
value = interp(0.5); // rgba(128,0,128,1)

// объекты
let interp = gsap.utils.interpolate({a:0, b:10, c:"red"}, {a:100, b:20, c:"blue"});
value = interp(0.5); // {a: 50, b: 15, c: "rgba(128,0,128,1)"}

{% endhighlight %}

<b>16.9 gsap.utils.splitColor()</b> - преобразует значение цвета на основе строки в массив, состоящий из [красного, зеленого, синего] (или, если требуется альфа-значение). Работает с rgb (), rgba (), hsl (), hsla (), шестнадцатеричным или любым из основных названных цветов.

{% highlight javascript %}

gsap.utils.splitColor("red");     // [255, 0, 0]
gsap.utils.splitColor("#6fb936"); // [111, 185, 54]
gsap.utils.splitColor("rgba(204, 153, 51, 0.5)"); // [204, 153, 51, 0.5]

{% endhighlight %}