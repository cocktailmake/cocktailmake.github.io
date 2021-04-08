This document explains how to setup the blog system Nikola on your
local and push new posts to GitHub Pages.

Base Information
----------------

GitHub Pages
https://cocktailmake.github.io/

How to setup GitHub Pages
https://pages.github.com/

The master branch is disclosed on the Pages.


Setup Nikola on Your Local
--------------------------

Install the base package Nikola

```
$ pip3 install nikola
```

Install an optional package which enables to automatically compile the
source and serve on your local Nikola server. When you save the source
texts on your editor, the Nikola server automatically compiles and
reload your web browser.

```
$ pip3 install watchdog
```

Add New Posts
-------------

Reference
Handbook: https://getnikola.com/handbook.html

The configuration of Nikola indicates to make new posts on a branch
"src".  When we deploy, the source posts are compiled and
automatically merged into master branch and pushed.

For writers, please work on your dedicated branches as below. After
writer have done, please commit and push and request reviewers to
check and deploy GitHub Pages.


1. Create a new issue on GitHub.

2. Create a new branch from "src" branch with the issue number.

```
$ git checkout -b doc/YOUR_ISSUE_NUMBER
```

3. Add a new post.

The default format is reStructuredText ( https://en.wikipedia.org/wiki/ReStructuredText ).

```
$ nikola new_post
```

If you would like to edit with Markdown, you can specify with a command option -f.

```
$ nikola new_post -f markdown
```

Emacs orgmode is also enabled.
Reference
https://streakycobra.github.io/posts/blogging-in-org-mode-with-nikola/

```
$ nikola new_post -f orgmode
```

3.1. Add your name as author on the top of the post.

The format is,

```
.. author: YOUR NAME
```

3.2. Add `previewimage` and set a image path

For examples,

```
#+BEGIN_COMMENT
.. title: Improvement of Git commands with fzf
.. slug: improvement-of-git-commands-with-fzf
.. date: 2020-07-04 23:30:00 UTC+09:00
.. tags: git, fzf
.. category: tool
.. link: 
.. description: 
.. type: text
.. previewimage: /images/improvement-of-git-commands-with-fzf/demo-log.png
.. author: Hitoshi Uchida
#+END_COMMENT
```

4. Check the pages on web browsers

Run the Nikola local server. Your web browser is automatically opened at a new tab.

```
$ nikola auto -b
```

In case you have error when running Nikola server as ~Please install htmlize from https://github.com/hniksic/emacs-htmlize~, it is because htmlize no longer be shipped with org-mode, and you can fix it by
* Clone emacs-htmlize in this repo https://github.com/hniksic/emacs-htmlize
* Edit emacs's ~init.el~, normally it is `~/.emacs` and add the following line:

```
(add-to-list 'load-path "[absolute path to emacs-htmlize directory]")
(require 'htmlize)
```

5. Revise the posts

The Nikola server automatically recompiles and reload the browser tab.

6. Push your working branch and request reviewers to check


(Optional) Change Theme
-----------------------

Reference
https://themes.getnikola.com/

There are some available themes and we can install and apply.

1. Install a new theme and save to /plugins directory.

```
$ nikola theme -i THEME_NAME
```

2. Apply the theme with changing THEME variable on conf.py.
