---
layout: post
title:  "Автоматизация старта верстки"
excerpt: "Как стать начинающим верстальщиком"
keywords: "верстка, frontend, html, css, javascript, bash"
date:   2016-05-18 08:24:00 +0300
categories: how-to
lang: ru
autor:  Гатилин Максим
---
Привет, читатель!

Если вы верстаете сайты на потоке, старт проекта превращается в рутину. Чтобы с нуля создать верстку сайта раньше мне приходилось делать вручную следующие операции:

1. Создание в определенном месте жесткого диска главной папки проекта по принципу site_номер_сайта(название_сайта), например site_49(dota)
2. Клон стартового шаблона с github в подпапку site
3. Удаление папки git(чтобы безболезненно залить проект в новый репозиторий)
4. Создание папки для файлов заказчика(шрифты, макеты, ТЗ)
5. Линк глобально установленных npm-пакетов для автоматизации(в вашем случае может быть npm install)
6. Открытие проекта в редакторе(в моем случае IDE PhpStorm)
7. Запуск галпа(или какого-нибудь npm run start)

Но теперь я просто открываю консоль и пишу:

{% highlight html %}
new 49 dota
{% endhighlight %}

И вот спустя мгновения проект с нужной структурой открыт в редакторе, а в браузере browsersync открыл мне localhost с главной страницей проекта.
О автоматизации и пойдет речь в этой статье.

## Установка консоли
1. Устанавливаем <a href="https://git-scm.com/download/win" target="_blank">git</a>
2. Скачиваем <a href="http://cmder.net">cmder</a>(mini-версию) и распаковываем в C:/cmder . Запускаем C:/cmder/Cmder.exe от имени администратора, нажать Win + Alt + P для перехода к настройкам.
3. Переходим в Startup → Tasks. Нажимаем кнопку Add dafault tasks... для добавления задач по умолчанию. В списке Predefined tasks должен быть {Bash::Git bash}.
4. В настройках: Startup → выбираем радиокнопку Auto save/restore opened task. Теперь при открытии cmder Вы будете видеть вкладки с теми же консолями, с которыми работали перед закрытием. Сохранить.
5. В главном окне программы нажать Ctrl + T для создания новой вкладки с консолью и в секции Create new console выбрать {Bash::Git bash} . При необходимости, можно поставить флажок «run as Administrator».
6. Закрыть открытую изначально вкладку (самая первая, почти наверняка, powershell.exe) кликом средней кнопки мыши или из контекстного меню вкладки.

Об основах использования подобной консоли вы можете почитать <a href="http://nicothin.pro/page/console-windows" target="_blank">на блоге Николая Громова</a>, а мы перейдем к автоматизации.

## Редактируем .bashrc

Для того чтобы писать алиасы и функции для автоматизации нам нужно создать файл .bashrc по адресу <mark>C:/Users/ИМЯ_ПОЛЬЗОВАТЕЛЯ/.bashrc</mark>(возможно файл уже создан). В в этом файле напишем первый алиас:
{% highlight bash %}
alias pro='cd /e/workflow/job/tasks_from_Andrew/'
{% endhighlight %}

Теперь при запуске команды <mark>pro</mark> мы будем переходить в папку которую я указал(в ней я храню проекты, вы ставите свою). Далее напишем алиас для запуска проекта в phpstorm, обратите внимание на экранирование символов:
{% highlight bash %}
alias storm='/c/Program\ Files\ \(x86\)/JetBrains/PhpStorm\ 2016.1.1/bin/PhpStorm.exe' $*
{% endhighlight %}
После пути мы указываем <mark>$*</mark>, чтобы подставлять путь, который надо открыть в редакторе. Далее напишем функцию для создания нового проекта:
{% highlight bash %}
createNewProject() {
    # Переходим в папку с проектами
    pro
    # Создаем новую папку, $1 - номер проекта, $2 - название
    mkdir site_$1\($2\)
    # Переходим в папку с проектом
    cd site_$1\($2\)
    # Создаем папку для исходников(макеты, шрифты)
    mkdir source
    # Клонируем стартовый шаблон с гитхаба
    git clone https://github.com/glivera-team/glivera-team-template.git
    # Переименовываем название шаблона(в данном случае glivera-team-template) на папку 'site'
    mv /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/glivera-team-template /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/site
    # Переходим в папку site
    cd site
    # Удаляем ненужные папки и папку с гитом
    rm -rf .git settings.jar
    # Линкуем глобальные пакеты(сюда можно поставить npm install)
    npm link gulp rimraf gulp-jade gulp-sass gulp-inline-image gulp-autoprefixer gulp-plumber gulp-directory-sync browser-sync gulp-concat gulp-cssfont64 gulp-html5-lint gulp-purifycss gulp-uglify gulp-imagemin imagemin-pngquant gulp-csso gulp-sourcemaps gulp-postcss postcss-assets
    # Открываем проект в PhpStorm
    storm /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/site
    # Запускаем gulp(или npm run start)
    gulp
}
{% endhighlight %}
Далее делаем алиас и присваем ему функцию:
{% highlight bash %}
alias new=createNewProject
{% endhighlight %}

Итоговый файл выглядит так:
{% highlight bash %}
alias pro='cd /e/workflow/job/tasks_from_Andrew/'
alias storm='/c/Program\ Files\ \(x86\)/JetBrains/PhpStorm\ 2016.1.1/bin/PhpStorm.exe' $*

createNewProject() {
    pro
    mkdir site_$1\($2\)
    cd site_$1\($2\)
    mkdir source
    git clone https://github.com/glivera-team/glivera-team-template.git
    mv /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/glivera-team-template /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/site
    cd site
    rm -rf .git settings.jar
    npm link gulp rimraf gulp-jade gulp-sass gulp-inline-image gulp-autoprefixer gulp-plumber gulp-directory-sync browser-sync gulp-concat gulp-cssfont64 gulp-html5-lint gulp-purifycss gulp-uglify gulp-imagemin imagemin-pngquant gulp-csso gulp-sourcemaps gulp-postcss postcss-assets
    storm /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/site
    gulp
}

alias new=createNewProject
{% endhighlight %}

## Используем

После каждого редактирования .bashrc необходимо перезапустить cmder. После перезапуска пишем
{% highlight bash %}
new 49 dota
{% endhighlight %}
И вы получите желаемый результат. В этой статье я описал автоматизацию своей рутины, ваш же путь может быть иным. Если у вас много кейсов начала работы, то лучше разобраться с генераторами, например <a href="http://yeoman.io/" target="_blank">Yeoman</a>.

На этом все.