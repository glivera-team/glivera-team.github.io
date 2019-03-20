---
layout: post
title:  "Automation layout start"
keywords: "frontend, html, css, javascript, bash"
date:   2019-03-16 12:24:00 +0300
categories: how-to
lang: en
autor:  Andrey Boyko
---
Hello, reader!

If you create websites on a stream, the start of a project turns into a routine. Earlier, in order to create a site layout from scratch, I had to manually do the following operations:

1. Creation of the main project folder in a certain place on the principle of `site_number_of the site (site_name)`, for example `site_49 (dota)`
2. The clone of the start template with github in the site subfolder
3. Deleting the git folder (to safely upload the project to a new repository)
4. Creating a folder for customer files (fonts, layouts, TR)
5. Link of globally installed npm packages for automation (in your case it can be npm install)
6. Opening a project in the editor (in my case IDE PhpStorm)
7. Starting  gulp (or some npm run start)

But now I’m just opening the console and write:

{% highlight html %}
new 49 dota
{% endhighlight %}

And now, after a moment, a project with the necessary structure is open in the editor, and in the browser, browsersync has opened to me localhost with the main page of the project. Exactly automation will be discussed in this article.

## Console installation

1. Install <a href="https://git-scm.com/download/win" target="_blank">git</a>
2. Download <a href="http://cmder.net">cmder</a> (mini-version) and unpack it in `C:/cmder`. Run `C:/cmder/Cmder.exe` as administrator, press Win+Alt+P to go to the settings.
3. Go to Startup → Tasks. Click the Add default tasks... to add default tasks. In the list of Predefined tasks, you should see {Bash :: Git bash}.
4. In the settings: Startup → select the Auto save/restore opened task. Now, when you open cmder, you will see tabs with the same consoles that you worked with before closing. Save.
5. Press Ctrl+T in the main window of the program to create a new tab with the console and in the Create new console section select {Bash :: Git bash}. If necessary, you can check the box “run as Administrator”.
6. Close the initially open tab (the very first, almost certainly, powershell.exe) by clicking the middle mouse button or from the tab context menu.

## Editing .bashrc

In order to write aliases and functions for automation, we need to create a .bashrc file at <mark>C:/Users/USER_NAME/.bashrc</mark> (perhaps the file has already been created). Let’s write the first alias in this file:

{% highlight bash %}
alias pro='cd /e/workflow/job/tasks_from_Andrew/'
{% endhighlight %}

Now when you run the <mark>pro</mark> command, we will go to the folder I specified (in it I store projects, you set yours). Next, let’s write an alias for running the project in phpstorm, pay attention to the escaping character

{% highlight bash %}
alias storm='/c/Program\ Files\ \(x86\)/JetBrains/PhpStorm\ 2016.1.1/bin/PhpStorm.exe' $*
{% endhighlight %}

After the path, we specify <mark>$*</mark> to substitute the path that needs to be opened in the editor. After this, let’s write a function to create a new project:

{% highlight bash %}
createNewProject() {
    # Go to the folder with the projects
    pro
    # Create a new folder, $1 - project number, $2 - name
    mkdir site_$1\($2\)
    # Go to the folder with the project
    cd site_$1\($2\)
    # Create a folder for sources (layouts, fonts)
    mkdir source
    # Clone a starting template from github
    git clone https://github.com/glivera-team/glivera-team-template.git
    # Rename the name of the template (in this case glivera-team-template) to the 'site' folder
    mv /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/glivera-team-template /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/site
    # Goto to the site folder
    cd site
    # Delete unnecessary folders and folder with git
    rm -rf .git settings.jar
    # Link global packages (here you can put npm install)
    npm link gulp rimraf gulp-jade gulp-sass gulp-inline-image gulp-autoprefixer gulp-plumber gulp-directory-sync browser-sync gulp-concat gulp-cssfont64 gulp-html5-lint gulp-purifycss gulp-uglify gulp-imagemin imagemin-pngquant gulp-csso gulp-sourcemaps gulp-postcss postcss-assets
    # Open a project in PhpStorm
    storm /e/workflow/job/tasks_from_Andrew/site_$1\($2\)/site
    # Run gulp (or npm run start)
    gulp
}

{% endhighlight %}
After we have done this, we write an alias and assign it a function:
{% highlight bash %}
alias new=createNewProject
{% endhighlight %}

The final file looks like this:

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

## We use

After each editing of `.bashrc`, you must restart the `cmder`. After restarting we need to write:

{% highlight bash %}
new 49 dota
{% endhighlight %}

And here we are - you get the desired result. In this article, I have described the automation of my routine, but your path may be different. If you have a lot of cases of the beginning of work, then it is better to deal with generators, for example, <a href="http://yeoman.io/" target="_blank">Yeoman</a>.

That's all. See you.