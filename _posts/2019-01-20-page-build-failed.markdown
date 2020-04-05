---
layout:     post
title:      "写博客遇到的『page build failed错误』"
subtitle:   "『page build failed』when writing with GitHub Pages"
date:       2019-01-20 12:00:00
author:     "hischen"
header-img: "img/post-aaple.jpg"
header-mask: 0.3
catalog:    False
tags:
    - GitHub Pages
    - page build failed
    - not properly closed `
---



>写博客的时候，有一次在插入代码的时候，在C++里定义了一个_unordered_map_，用“{{”开始，“}}”结束的，然后就收到了GitHub官方的Page build failure邮件：


>The page build failed for the `master` branch with the following error:
>{% raw %}The variable `{{ }` on line 75 in `***` was not properly closed with `}}`. For more information, see https://help.github.com/en/github/working-with-github-pages/troubleshooting-jekyll-build-errors-for-github-pages-sites#tag-not-properly-terminated.{% endraw %}


　　{% raw %}找了很多原因，发现如果 markdown 文件包含代码块（无论是C++，Python等还是LaTeX），且代码块中包含花括号 { 或 }，尤其是包含 {% 或 {{ 符号组合时，GitHub Page 会报错。{% endraw %}
　　{% raw %}众所周知，GitHub Page 默认使用 [Jekyll](https://jekyllrb.com/)来从Markdown文件生成网页，而Jekyll是使用[Liquid](https://shopify.github.io/liquid/) 模版语言的，Jekyll通过Liquid模板语言在文本中加入简单的标记，从而自动化的将文本形成静态网页。Liquid代码可以分为以下 [三类](https://shopify.github.io/liquid/basics/introduction/)：
- Objects：tell Liquid where to show content on a page.Objects and variable names are denoted by double curly braces: {{ and }}.
- Tags：create the logic and control flow for templates. They are denoted by curly braces and percent signs: {% and %}.
- Filters：change the output of a Liquid object. They are used within an output and are separated by a |.
{% endraw %}
　　　{% raw %}如果在你的GitHub Pages里的Markdown文件里的代码段中出现了与 上述Liquid 代码的三类标记相同的字符组合，你的GitHub Pages网站就会发生构建错误，在你GitHub 账号的邮箱里，也会收到page build failed(构建失败)的邮件，例如：
>The page build failed for the `master` branch with the following error:
>The variable `{{ }` on line ** in `***.md` was not properly closed with `}}`.

>The tag `{%` on line ** in `***.md` was not properly closed with `%}`.
{% endraw %}
　　{% raw %}Jekyll 在构建网站时，将代码段中的"{{"或 "{%" 符号组合识别为 _Liquid Objects_或者_Liquid tag_ 的起始部分，从而会导致『page build failed』的错误.{% endraw %}
　　{% raw %}根据来自stackoverflow [_ignore-a-specific-tag-in-jekyll_](https://stackoverflow.com/questions/16256799/ignore-a-specific-tag-in-jekyll)的答案，尝试在出现以上三种标记的代码段前加入 _{% raw %}_ ，代码段后加入 _{% endraw %}_ ，即：

    {% raw %}
  	{{ ... }}
	{% endraw %}
{% endraw %}
即可避免这个错误。
