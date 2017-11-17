---
layout: post
title:  "Spotify. Working with sections"
date:   2017-11-17 17:00:00 +0300
categories: spotify
lang: en
autor: Nikolay Gavrilov
---
# **Spotify. Working with sections**

First of all, we need to understand why the "sections" were introduced, what exactly they give the developers to those directly to the owners of the stores. The functionality of the sections was introduced by Shopify developers in late 2016 and provides the ability to create a user interface that allows the store administrator to easily add or change the order of the output, whether simple text sections or complex sliders and galleries.

All changes the administrator sees in real time, which is very convenient. The control is available from the administrative part of the store (see figures 1 and 2).

![foto 1] (https://github.com/glivera-team/Wiki/blob/master/img/sect1.jpg)
_img.1_

![foto 2] (https://github.com/glivera-team/Wiki/blob/master/img/sect2.jpg)
_img.2_

Sections can be included statically (for example, the header or footer of the template), they can also be added or deleted dynamically via the admin interface on the main page of the topic. In the example above, you can see the editing of the content of the static section using the example of a slider. Also visible is the "add section" button, which allows you to display dynamic sections. All this we will consider in this article.

Section templates are located in the sections folder and are displayed similarly to snippets. If the section file is located under the path `sections/sponsored.liquid`, then output it as follows.

**`{% section “sponsored” %}`**

**Note:** the extension `.liquid` should not be specified, as it is done in snippet topics.

Consider a real example to understand the possibilities of the sections. Figure 3 shows the contents of the file `sections/footer.liquid`

![foto 3](https://github.com/glivera-team/Wiki/blob/master/img/sect3.jpg)
_img.3_

As you can see, the file contains a mixture of HTML, Liquid placeholders and JSON, similar to the theme configuration file `settings_schema.json.` The `settings_schema.json` functional remains, the sections simply add additional functionality.

At the top of the template is HTML, which will be displayed on the page. Within each of the `h1` and `p` elements, there are Liquid placeholders using this syntax **`{{section.settings. [X]}}`**.

In our example, the section template will output data that matches **`{{section.settings.title}}`** and **`{{section.settings.description}}`**.

All this is excellent, but how does Shopify know what to output in these placeholders? This is indicated in JSON (see figure 4), which is located between the opening and closing tags **`{% schema%}`**.

![foto 4](https://github.com/glivera-team/Wiki/blob/master/img/sect4.jpg)
_img.4_

To display the section in the "theme customization" area of the store, we need to assign it an identifier, specifying the value **"name"** on the top level of our JSON.

Next comes the configuration level, which contains two sub-nodes, in which the properties `“id”`, `“type”`, `“label”` и `“default”` are indicated. Each of them, depending on its value, determines the way the administrator interface is displayed. Let's consider each one in turn:

## **id**

An identifier that indicates that it is from this sub-node that you need to display information in the corresponding placeholder (see figure 5).

![foto 5](https://github.com/glivera-team/Wiki/blob/master/img/sect5.jpg)
_img.5_

In spite of the fact that the identifiers in the section file must be unique, their uniqueness in all section files is not required. Therefore, it is quite normal to have `“id”: “title”` in files of several sections. Also the settings of sections with settings in `settings_schema.json` will not conflict.

## **type**

Indicates the type of control that will be displayed in the administration part. List of frequently used parameters:

* **`text:`** one-line text field
* **`textarea:`** multi-line text field
* **`richtext:`** a text field with the capabilities of a text editor
* **`image_picker:`** loading images
* **`radio:`** radio buttons
* **`select:`** drop-down list
* **`checkbox:`** checkboxes
* **`range:`** the range or value slider

Some types require additional JSON to work. For example, drop-down lists or radio buttons. Detailed information can be found on the official documentation page

## **label**

Displays the title of the control that is displayed in the administration section.

## **default**

Adds a **placeholder** to the control. It is worth noting that this value will be used until the section is updated by the store administrator.

There are also types for outputting url addresses, custom HTML, etc. You can get acquainted with them by following the above [https://help.shopify.com/themes/development/theme-editor/settings-schema](shopify documentation).

Also in the section files, you can add custom JS or CSS using special tags **`{% stylesheet%}`** and **`{% javascript%}`** (see figure 6).

![foto 6](https://github.com/glivera-team/Wiki/blob/master/img/sect6.jpg)
_img.6_

But what about the purity of the code? After all, this means that we will have a bunch of scattered in-line css and js. There is good news. Shopify combines all CSS and JS into one file, which is entered through the Liquid `content_for_header` placeholder. Also, you can use SASS when writing custom styles, you can do this using the **`{% stylesheet 'scss'%}`** tag.

If you look at the output of the section on the page, we notice that it is wrapped in a `<div>` element with a unique identifier and the `shopify-section` class.

```HTML
<div id="shopify-section-footer" class="shopify-section">
```

You can add a custom class to this element by using the `“class”` property in your JSON (see figure 7)

![foto 7](https://github.com/glivera-team/Wiki/blob/master/img/sect7.jpg)
_img.7_

As a result, the sections are a powerful tool in the development of Shopify themes and their further administration.