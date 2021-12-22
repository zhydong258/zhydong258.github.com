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

\{% post_path filename %\}

\{% post_link filename \[title\] \[escape\] %\}

You can ignore permalink and folder information, like languages and dates, when using this tag.

For instance: \{% post_link how-to-bake-a-cake %\}.

This will work as long as the filename of the post is how-to-bake-a-cake.md, even if the post is located at source/posts/2015-02-my-family-holiday and has permalink 2018/en/how-to-bake-a-cake.

You can customize the text to display, instead of displaying the post’s title.

Post’s title and custom text are escaped by default. You can use the escape option to disable escaping.

For instance:

Display title of the post.\{% post_link hexo-3-8-released %\}

Display custom text.\{% post_link hexo-3-8-released 'Link to a post' %\}

Escape title.\{% post_link hexo-4-released 'How to use <b> tag in title' %\}, <b>会直接显示出来。

Do not escape title.\{% post_link hexo-4-released '<b>bold</b> custom title' false %\} bold这个单词会加粗。