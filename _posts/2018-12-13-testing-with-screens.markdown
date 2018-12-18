---
layout: post
title:  "Автоматическое тестирование верстки"
date:   2018-12-13 13:16:00 +0300
categories: how-to
lang: ru
autor:  glivera-team
---
Привет, разработчик!

Частая проблема верстальщика - поправил стили в одном месте, а изменилось в нескольких местах. И верстальщик замечает это слишком поздно. В данном посте я расскажу о регрессивном тестировании. Суть метода заключается в том, что вы делаете скриншоты сайта(не вручную конечно), а затем после внесения каких-либо правок вы делаете новые скриншоты и сравниваете их с предыдущими. Если есть какие-либо отличия - тесты об этом говорят и показывают. Можно делать скриншоты как сайтов, так и отдельных блоков.

Для данного метода нам потребуется:

* <b><a href="https://gulpjs.com/" target="_blank">Gulp</a></b> - система сборки.
* <b><a href="https://github.com/GoogleChrome/puppeteer" target="_blank">puppeteer</a></b> - node.js библиотека, которая позволяет управлять браузером Chromium без пользовательского интерфейса.
* <b><a href="https://github.com/mapbox/pixelmatch" target="_blank">pixelmatch</a></b> -  библиотека сравнения изображений на уровне пикселей, созданная для сравнения снимков экрана в тестах.
* <b><a href="https://www.npmjs.com/package/pngjs" target="_blank">pngjs</a></b> -  простой PNG кодер/декодер для node.js
* <b><a href="https://nodejs.org/api/fs.html" target="_blank">fs</a></b> -  модуль предоставляет API для взаимодействия с файловой системой
* <b><a href="https://www.npmjs.com/package/path" target="_blank">path</a></b> - предоставляет утилиты для работы с путями файлов и каталогов

Также для удобства отображения всех результатов на одной странице в браузере нам понадобятся дополнительно пакеты:  
* <b><a href="https" target="_blank">http</a></b>
* <b><a href="https" target="_blank">node-static</a></b>
* <b><a href="https" target="_blank">chrome-launcher</a></b>


## Установка
Устанавливаем gulp:
{% highlight html %}
npm install gulp -g
{% endhighlight %}
В корне вашего проекта инициализируем npm:
{% highlight html %}
npm init
{% endhighlight %}
Устанавливаем остальные пакеты:
{% highlight html %}
npm i chrome-launcher fs http node-static path pixelmatch pngjs puppeteer
{% endhighlight %}

Установка может занять некоторое время, так как в пакет puppeteer входит установка браузера chromium

## Настройка gulp.js

Прописываем зависимости для установленных пакетов

{% highlight html %}
const puppeteer = require('puppeteer');
const fs = require('fs');
const PNG = require('pngjs').PNG;
const pixelmatch = require('pixelmatch');
const chromeLauncher = require('chrome-launcher');
const http = require('http');
const staticN = require('node-static');
const path = require('path');
{% endhighlight %}

Конфигурация под конкретный проект:

Указываем ширину страницу при тестировании
{% highlight html %}
var initialPageWidth = 1920;
{% endhighlight %}

И список тестируемых страниц вносим в массив `pageList`:
{% highlight html %}
var pageList = [
	'index',
	'gallery',
	'faq'
];
{% endhighlight %}

## Будущая структура результатов теста

{% highlight html %}
└── test/                 
	  ├── before/           
	  │   ├── index.png
		│		├── ...
	  │   └── pageXX.png  
		├── after/  
	  │   ├── index.png
		│		├── ...
	  │   └── pageXX.png  
		├── difference/
	  │		├── index.png
		│		├── ...
	  │   └── pageXX.png  
		└── index_test.html
{% endhighlight %}

Все результаты будут храниться в папке `test`, котороая будет поделена еще на 3 подкаталога: 
* <b>before</b> - для хранения эталонных скриншотов
* <b>after</b> - для хранения новых скриншотов, которые впоследствии будут сравниваться
* <b>difference</b> - для хранения результата сравнения новых скриншотов с эталонными

Создаем переменные для сохранения путей: 
{% highlight html %}
var beforeDir = 'test/before/',
		afterDir = 'test/after/',
		diffDir = 'test/difference/';
{% endhighlight %}

## Создание тасков

### 1.Такс создания эталонных скриншотов

Изначально с помощью модуля `fs` создаем каталоги в которых будут храниться результаты тестирования, если они отсутствуют. А так же очищаем от старых скриншотов, если такие имеются.
{% highlight html %}
if (!fs.existsSync('test')){
		fs.mkdirSync('test');
	}

	if (!fs.existsSync(beforeDir)){
		fs.mkdirSync(beforeDir);
	}

	if (fs.existsSync(beforeDir)){
		fs.readdir(beforeDir, (err, files) => {
			for (const file of files) {
				fs.unlink(path.join(beforeDir, file), err => {});
			}
		});
	}
{% endhighlight %}


После проходим по массиву страниц pageList, который мы заполнили ранее. 
В `page.goto` указываем адрес страниц, в данном случае это `http://localhost:1337/`
В `page.screenshot` - каталог для сохраниния скриншотов, а так же указываем `fullPage: true` для сохранения страницы целиком, а не определенной высоты 
{% highlight html %}
pageList.map(async function(element, index) {
	const browser = await puppeteer.launch();
	const page = await browser.newPage();

	await page.setViewport({ width: initialPageWidth, height: 0 });

	await page.goto('http://localhost:1337/' + element + '.html');

	await page.screenshot({path: beforeDir + element + '.png', fullPage: true});
	console.log(element + ' page +');

	await browser.close();	
})
{% endhighlight %}

Полность таск создания эталонных скриншотов выглядит так:
{% highlight html %}
gulp.task('test-init', function() {
	if (!fs.existsSync('test')){
		fs.mkdirSync('test');
	}

	if (!fs.existsSync(beforeDir)){
		fs.mkdirSync(beforeDir);
	}

	if (fs.existsSync(beforeDir)){
		fs.readdir(beforeDir, (err, files) => {
			for (const file of files) {
				fs.unlink(path.join(beforeDir, file), err => {});
			}
		});
	}

	pageList.map(async function(element, index) {
		const browser = await puppeteer.launch();
		const page = await browser.newPage();
	
		await page.setViewport({ width: initialPageWidth, height: 0 });
	
		await page.goto('http://localhost:1337/' + element + '.html');

		await page.screenshot({path: beforeDir + element + '.png', fullPage: true});
		console.log(element + ' page +');
	
		await browser.close();	
	})
})
{% endhighlight %}

Запукается таск с помощью команды:
{% highlight html %}
gulp test-init
{% endhighlight %}

### 2.Такс создания новых скриншотов и их сравнения с эталонными

Как и в предыдущем таске, создаем необходимые каталоги или очищаем их от старых файлов:

{% highlight html %}
var clearDir = [diffDir, afterDir, 'test/']

if (!fs.existsSync(afterDir)){
	fs.mkdirSync(afterDir);
}

if (!fs.existsSync(diffDir)){
	fs.mkdirSync(diffDir);
}

clearDir.map(function(element, index) {
	if (fs.existsSync(element)){
		fs.readdir(element, (err, files) => {
			for (const file of files) {
				fs.unlink(path.join(element, file), err => {});
			}
		});
	}
});
{% endhighlight %}

Далее так же проходим массив страниц для создания новых страниц, но теперь и сравнивая их с эталонными

{% highlight html %}
pageList.map(async function(element, index) {
	const browser = await puppeteer.launch();
	const page = await browser.newPage();

	await page.setViewport({ width: initialPageWidth, height: 0 });

	await page.goto('http://localhost:1337/' + element + '.html');

	await page.screenshot({path: afterDir + element + '.png', fullPage: true});

	await browser.close();

	pageName = element;
	img1[index] = await fs.createReadStream(afterDir + element + '.png').pipe(new PNG()).on('parsed', function() { parse2(element, index)});
})
{% endhighlight %}

Код выше аналогичен созданию эталонных скриншотов, кроме дополнительного внесения каждого сделанного скриншота в массив `img1`
Далее с помощью функий `parse2` мы вносим и эталонные скриншоты в массив `img2`, после сравниваем их с помощью фунции `doneReading`, полученный результат записывая в необходимый каталог:

{% highlight html %}
function parse2(element, index, pageName) {
	img2[index] = fs.createReadStream(beforeDir + element + '.png').pipe(new PNG()).on('parsed', function() { doneReading(img1[index], img2[index], element)});
}

function doneReading(img1, img2, pageName) {
	var diff = new PNG({width: img1.width, height: img1.height});

	pixelmatch(img1.data, img2.data, diff.data, img1.width, img1.height, {threshold: 0.5});


	diff.pack().pipe(fs.createWriteStream(diffDir + pageName + timeMod + '.png'));
	console.log(pageName + ' ---- page compared');
}
{% endhighlight %}


Для дополнительного удобства, мы выводим все изображения сравненных страниц на одной странице и открываем ее в браузере.

Создадим переменную, которую будем использоват как модификатор в именах изображений и страниц для избежания кеширования в браузере:
{% highlight html %}
var timeMod = new Date().getTime();
{% endhighlight %}


Далее создаем список всех страниц с именем и изображением
{% highlight html %}
var imgList = pageList.map(function(file, i) {
	return '<li style="width: 49%; display: inline-block; list-style: none; background-color: #888;"><h2 style="font: 3vw sans-serif; margin: 0; padding: 1em; text-align: center;">' + pageList[i] + '</h2><img style="width: 100%; display: block;" src="difference/' + file + timeMod + '.png"/></li>'
})
{% endhighlight %}

И вносим созданный выше список в html файл
{% highlight html %}
fs.writeFile('test/index_test' + timeMod + '.html', imgList, function (err) {});
{% endhighlight %}

И в завершение создаем локальный сервер с этой страницей и открываем ее в браузере Google Chrome:
{% highlight html %}
var fileServer = new staticN.Server();

http.createServer(function (req, res) {
	req.addListener('end', function () {
			fileServer.serve(req, res);
	}).resume();
}).listen(8080);

chromeLauncher.launch({
	startingUrl: 'http://localhost:8080/test/index_test' + timeMod + '.html',
	userDataDir: false 
}).then(chrome => {
	console.log(`Chrome debugging port running on ${chrome.port}`);
});
{% endhighlight %}




Полность таск создания новых скриншотов скриншотов и сравнениях их с эталонными выглядит так:
{% highlight html %}
gulp.task('test-compare', function() {
	var timeMod = new Date().getTime();
	var clearDir = [diffDir, afterDir, 'test/']

	if (!fs.existsSync(afterDir)){
		fs.mkdirSync(afterDir);
	}
	
	if (!fs.existsSync(diffDir)){
		fs.mkdirSync(diffDir);
	}
	
	clearDir.map(function(element, index) {
		if (fs.existsSync(element)){
			fs.readdir(element, (err, files) => {
				for (const file of files) {
					fs.unlink(path.join(element, file), err => {});
				}
			});
		}
	});

	function doneReading(img1, img2, pageName) {
		var diff = new PNG({width: img1.width, height: img1.height});
		pixelmatch(img1.data, img2.data, diff.data, img1.width, img1.height, {threshold: 0.5});
		diff.pack().pipe(fs.createWriteStream(diffDir + pageName + timeMod + '.png'));
		console.log(pageName + ' ---- page compared');
	}

	function parse2(element, index, pageName) {
		img2[index] = fs.createReadStream(beforeDir + element + '.png').pipe(new PNG()).on('parsed', function() { doneReading(img1[index], img2[index], element)});
	}

	pageList.map(async function(element, index) {
		const browser = await puppeteer.launch();
		const page = await browser.newPage();
		await page.setViewport({ width: initialPageWidth, height: 0 });
		await page.goto('http://localhost:1337/' + element + '.html');
		await page.screenshot({path: afterDir + element + '.png', fullPage: true});
		await browser.close();
		pageName = element;
		img1[index] = await fs.createReadStream(afterDir + element + '.png').pipe(new PNG()).on('parsed', function() { parse2(element, index)});
	})

	var imgList = pageList.map(function(file, i) {
		return '<li style="width: 49%; display: inline-block; list-style: none; background-color: #888;"><h2 style="font: 3vw sans-serif; margin: 0; padding: 1em; text-align: center;">' + pageList[i] + '</h2><img style="width: 100%; display: block;" src="difference/' + file + timeMod + '.png"/></li>'
	})

	fs.writeFile('test/index_test' + timeMod + '.html', imgList, function (err) {});

	var fileServer = new staticN.Server();

	http.createServer(function (req, res) {
		req.addListener('end', function () {
				fileServer.serve(req, res);
		}).resume();
	}).listen(8080);

	chromeLauncher.launch({
		startingUrl: 'http://localhost:8080/test/index_test' + timeMod + '.html',
		userDataDir: false 
	}).then(chrome => {
		console.log(`Chrome debugging port running on ${chrome.port}`);
	});
})
{% endhighlight %}

Запукается таск с помощью команды:
{% highlight html %}
gulp test-init
{% endhighlight %}

## Обзор результатов

При совпадении новых скриншотов с эталонными, результат будет выглядеть подобным образом:
<img alt="" src="../../../../i/test-compare.jpg">

При несовпадении изображений(теста у эмелентов на странице Catalogue) разница будет выделена красным цветом:
<img alt="" src="../../../../i/test-compare2.jpg">

Также можно изменить чувствительность сравнения изображений с помощью параметра `threshold`, он задается при вызове `pixelmatch`, может иметь значения от 0 до 1, где меньшее значение соответствует большей строгости сравнения.
В данном примере он вызывается внутри функции `doneReading` со значением `threshold: 0.5`