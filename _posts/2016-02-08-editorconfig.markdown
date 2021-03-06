---
layout: post
title:  "Памятка про .editorconfig"
date:   2016-02-08 08:24:00 +0300
categories: tips
lang: ru
autor:  Andrey Boyko
---
Привет, разработчик!

Бывают случаи, когда помимо основной IDE используется простой редактор для быстрого просмотра/редактирования кода. Настройки синтаксиса(например табы или пробелы в качестве отступов) могут различаться. Когда над проектом работает несколько человек, и каждый использует разные настройки - тоже может привести к проблемам. Чтобы стандартизировать настройки синтаксиса для вашего проекта - используйте .editorconfig.
 
.editorconfig - конфигурационный файл для настроек синтаксиса. В зависимости от IDE или редактора вам нужно установить плагин. Например есть [плагин для PHPStorm](https://plugins.jetbrains.com/plugin/7294). Устанавливаем его, перезапускаем IDE, заходим в настройки Settings/CodeStyle/ и убеждаемся что галочка "Enable editorconfig" стоит.

Далее создаем внутри проекта пустой файл .editorconfig и вписываем в него настройки для проекта

{% highlight ini %}
# Корневой файл EditorConfig
root = true

# Для всех файлов
[*]
charset = utf-8
indent_style = tab
indent_size = 4

# Если нужно - отдельные настройки можно переписать для конретного типа файлов
[*.jade]
indent_style = space
indent_size = 2

[*.scss]
indent_style = tab
indent_size = 8

{% endhighlight %}

Далее данные настройки будут переписывать настройки IDE.

Документация по всем свойствам editorconfig доступна на [оф.сайте](http://editorconfig.org/)

На этом все.