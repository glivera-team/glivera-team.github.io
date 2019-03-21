---
layout: post
title:  "Memo about .editorconfig"
date:   2016-02-09 08:24:00 +0300
categories: tips
lang: en
autor:  Andrey Boyko
---
Hi, developer!

There are cases when, in addition to the main IDE, a simple editor is used to quickly view/edit of the code. Syntax settings (for example, tabs or spaces as indents) may differ. When several people work on a project, and each uses different settings, this can also lead to problems. To standardize syntax settings for your project use .editorconfig.
 
.editorconfig is a configuration file for syntax settings. Depending on the IDE or editor, you need to install the plugin. For example, there is a plugin for PHPStorm (https://plugins.jetbrains.com/plugin/7294). Install it, restart the IDE, go to Settings/CodeStyle/ and make sure that the "Enable editorconfig" checkbox is checked.

Next, we create an empty .editorconfig file inside the project and write the settings for the project into it.

{% highlight ini %}
# EditorConfig root file
root = true

# For all files
[*]
charset = utf-8
indent_style = tab
indent_size = 4

# If necessary - individual settings can be rewritten for a specific file type.
[*.pug]
indent_style = space
indent_size = 2

[*.scss]
indent_style = tab
indent_size = 8

{% endhighlight %}

Further, these settings will overwrite the IDE settings.

Documentation about all editorconfig properties is available on the official site (http://editorconfig.org/).

That's all.