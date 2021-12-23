---
title: Update NexT Theme to v8
date: 2021-12-23 17:10:41
tags: 
  - Hexo
  - NexT
categories: 运维
---

## Install

```
$ cd hexo-site
$ npm install hexo-theme-next@latest
```

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