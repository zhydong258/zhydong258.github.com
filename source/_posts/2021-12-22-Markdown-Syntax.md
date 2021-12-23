---
title: Markdown Syntax
date: 2021-12-22 19:02:32
tags: 
  - Hexo
  - Markdown
categories: 运维
---

## Markdown Syntax

### Include Posts

Include links to other posts.

```
{% post_path filename %}
{% post_link filename [title] [escape] %}
```

You can ignore permalink and folder information, like languages and dates, when using this tag.

For instance: 

```
{% post_link how-to-bake-a-cake %}
```

This will work as long as the filename of the post is how-to-bake-a-cake.md, even if the post is located at source/posts/2015-02-my-family-holiday and has permalink 2018/en/how-to-bake-a-cake.

You can customize the text to display, instead of displaying the post's title.

Post's title and custom text are escaped by default. You can use the escape option to disable escaping.

For instance:

Display title of the post.

```
{% post_link hexo-3-8-released %}
```

Display custom text.

```
{% post_link hexo-3-8-released 'Link to a post' %}
```

Escape title.

```
{{% post_link hexo-4-released 'How to use <b> tag in title' %}}
```
`<b>`会直接显示出来。

Do not escape title.
```
{% post_link hexo-4-released '<b>bold</b> custom title' false %}
```
<b>bold</b>这个单词会加粗。

## 链接到文章内的标题

```
[要显示的内容](#标题的名字)
```

注意：小括号中#后的标题的名字，不能用大写字母、空格和英文的句点，但问号和顿号可以。大写要改成小写，空格和句点改成短横杠。

中文的麻烦一些，处理方式如下，其中`<a>`可以是`<div>`、`<span>`、`<p>`等：

```
## <a id="section1">第一章</a>

可以参考前面的<a href="#section1">第一章</a>
```

### Escape Content

Hexo uses Nunjucks to render posts (Swig was used in older version, which share a similar syntax). Content wrapped with`{{ }}`or`{% %}`will get parsed and may cause problems. You can skip the parsing by wrapping it with the raw tag plugin, single backtick `` `{{ }}` ``or triple backtick.
Alternatively, Nunjucks tags can be disabled through the renderer's option (if supported), API or front-matter.

```
{% raw %}
Hello {{ world }}
{% endraw %}
```


````
```
Hello {{ world }}
```
````

常用的转义如下：
```
! &#33;         — 惊叹号 Exclamation mark
” &#34; &quot;  — 双引号 Quotation mark
# &#35;         — 数字标志 Number sign
$ &#36;         — 美元标志 Dollar sign
% &#37;         — 百分号 Percent sign
& &#38; &amp;   — Ampersand
‘ &#39;         — 单引号 Apostrophe
( &#40;         — 小括号左边部分 Left parenthesis
) &#41;         — 小括号右边部分 Right parenthesis
* &#42;         — 星号 Asterisk
+ &#43;         — 加号 Plus sign
< &#60; &lt;    —  小于号 Less than
= &#61;         — 等于符号 Equals sign
- &#45; &minus; — 减号
> &#62; &gt;    — 大于号 Greater than
? &#63;         — 问号 Question mark
@ &#64;         — Commercial at
[ &#91;         — 中括号左边部分 Left square bracket
\ &#92;         — 反斜杠 Reverse solidus (backslash)
] &#93;         — 中括号右边部分 Right square bracket
{ &#123;        — 大括号左边部分 Left curly brace
| &#124;        — 竖线Vertical bar
} &#125;        — 大括号右边部分 Right curly brace
```