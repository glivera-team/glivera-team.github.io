---
layout: post
title:  "Позиционирование блоков"
date:   2018-12-13 10:00:00 +0300
categories: how-to
lang: ru
autor:  glivera-team
---

В этой короткой статье мы разберем как лучше позицинировать подобные блоки.
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_1.jpg">

Эти изображение должны быть отдельными блоками так как в каждом из них при наведении будет проигрываться видео.

## 1. Неправильный способ

Имеется <b>div.hero_picture</b> который висит абсолютом и растянут на всю ширину и высоту секции
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_2.jpg">

После этого мы размещаем дочерние элементы по моду от центра родительского блока, то есть здесь идет позиционирование элементов относительно секции посредством top, bottom, left, right.
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_3.jpg">

### Итог неправильного способа
<iframe height='265' scrolling='no' title='Positioning block bad implementation' src='//codepen.io/edudiak/embed/MzzVzg/?height=265&theme-id=0&default-tab=css,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/edudiak/pen/MzzVzg/'>Positioning block bad implementation</a> by Evgen (<a href='https://codepen.io/edudiak'>@edudiak</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### Минусы этого способа

* При уменьшении размеров этих блоков при помощи задания font-size у родителя <b>(для этого нужно использовать относительные единицы измерения) </b>
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_8.jpg">
эти блоки смещается влево, то есть они не привязаны точно к центральной точке своего родителя.

* Если придется выровнять родительский блок не по левому краю а по правому, то здесь тоже тупик, не получится нормально без костылей сместить их, так как они привязаны к блоку который имеет размеры 100x100.

## 2. Правильный способ

Структура у нас остается такая же. Только теперь стили для <b>div.hero_picture</b> будут такими:
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_4.jpg">

Мы распологаем родительский элемент в том месте где он должен быть. А у дочерних элементов стили будут такими:
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_5.jpg">

Здесь мы размещаем их по центру родителя при помощи top: 50% и left: 50% а уже после по моду от центра при помощи margin мы перемещаем их в то место где они должны быть по макету.

Чем хорош этот способ ? Теперь блок <b>div.hero_picture</b>  которые вмещает в себя блоки с фотографиями можно перемещать как угодно, достаточно просто изменить позицию и все будет отрабатывать как надо.
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_6.jpg">

Также если вы захотите уменьшить этот блок при помощи font-size <b>(для этого нужно использовать относительные единицы измерения)</b> то он будет уменьшаться так как надо, центральная точка будет неизменна и внутренние элементы не будут смещаться в стороны при уменьшении.
<img alt="how-to: позиционирование блоков" src="../../../../i/block_positioning_7.jpg">

### Итог правильного способа
<iframe height='265' scrolling='no' title='Positioning block good implementation' src='//codepen.io/edudiak/embed/dQQegg/?height=265&theme-id=0&default-tab=css,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/edudiak/pen/dQQegg/'>Positioning block good implementation</a> by Evgen (<a href='https://codepen.io/edudiak'>@edudiak</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>