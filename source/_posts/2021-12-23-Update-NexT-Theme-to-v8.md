---
title: Update NexT Theme to v8
date: 2021-12-23 17:10:41
tags: 
  - Hexo
  - NexT
categories: 运维
---

## NexT Compatibility with Hexo Version

|Version                  | Hexo 3.0.0-beta.4 or earlier | Hexo 3.0.0-rc.1 ~ 3.9	| Hexo 4.0~4.2.1	| Hexo 5.0 or later|
| :---------------------- | :--------------------------- | :--------------------- | :---------------- | :--------------- |
|NexT v0.4.5.1 or earlier | ✅ | ✅ | ❌Icon(2) | ❌Icon(2) |
|NexT v0.4.5.2 ~ v7.4.1 | ⚠️Data Files(1) | ✅ | ❌Icon(2) | ❌Icon(2) |
|NexT v7.4.2 ~ v8.1.0 | ⚠️Data Files(1) | ✅ | ✅ | ✅ |
|NexT v8.2.0 or later | ⚠️Nunjucks(3) | ⚠️ Nunjucks(3) | ⚠️ Nunjucks(3) | ✅ |

(1): Hexo 3.0.0-beta.4 or earlier does not support Data Files.

(2): Icons may not be displayed normally.

(3): Nunjucks renderer plugin required.

## NexT Repositories
Due to historical reasons, NexT has three different repositories.

|Years|Version|Repository
|---|---|---|
|2014 ~ 2017| v5 | https://github.com/iissnan/hexo-theme-next|
|2018 ~ 2019| v6 ~ v7 | https://github.com/theme-next/hexo-theme-next|
|2020|v8 | https://github.com/next-theme/hexo-theme-next|


## Install

### Use npm

```
$ cd hexo-site
$ npm install hexo-theme-next@latest
```

### Use git

```
$ git clone https://github.com/next-theme/hexo-theme-next themes/next
```

{% note warning %}
#### Add NexT as git submodule
blog本身的源码是用git管理的，所以NexT的添加，需要使用submodule的方式。
`git submodule add https://github.com/next-theme/hexo-theme-next themes/next`
{% endnote %}


## Configuration 

How to configure Hexo and NexT? The traditional approach is to store some options in site config file and other options in theme config file. This approach is applicable, but it is not smooth to update NexT theme from pulling or downloading new releases.

At present, NexT encourages users to use the Alternate Theme Config. It's a feature of Hexo and the documentation is here: Hexo Configuration.

This tutorial shows you how to configure NexT using Alternate Theme Config. Please choose only one of the following solutions and resume next steps.

_config.[name].yml
With this way, all your configurations locate in config file /_config.[name].yml. Replace [name] with the value of theme option in site config file, e.g. next.

Usage
Please ensure you are using Hexo 5.0 (or later).

Create a config file in site's root directory, e.g. _config.next.yml.

Copy needed NexT theme options from theme config file into this config file. If it is the first time to install NexT, then copy the whole theme config file by the following command:

### Installed through npm
cp node_modules/hexo-theme-next/_config.yml _config.next.yml

### Installed through Git

cp themes/next/_config.yml _config.next.yml