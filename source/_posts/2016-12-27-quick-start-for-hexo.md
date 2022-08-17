---
title: Quick Start for Hexo
date: 2016-12-27 16:52:54
tags: Hexo
categories: 运维
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Install 

``` bash
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
```

### Create a new post

``` bash
$ hexo new "My New Post"
$ hexo new page about
$ hexo new page tags
```

### Create a new draft

``` bash
hexo new draft <title>
```

上面的命令会建立一篇文章的草稿，放到 source/_draft 中，可以使用 publish 发布到 source/_posts 中。

``` bash
hexo publish <title>
```

### Asset 资源文件夹

资源（Asset）代表 source 文件夹中除了文章以外的所有文件，例如图片、CSS、JS 文件等。比方说，如果你的Hexo项目中只有少量图片，那最简单的方法就是将它们放在 source/images 文件夹中。然后通过类似于 `![](/images/image.jpg)` 的方法访问它们。

对于那些想要更有规律地提供图片和其他资源以及想要将他们的资源分布在各个文章上的人来说，Hexo也提供了更组织化的方式来管理资源。这个稍微有些复杂但是管理资源非常方便的功能可以通过将 config.yml 文件中的 post_asset_folder 选项设为 true 来打开。

#### Embedding an image using markdown

hexo-renderer-marked 3.1.0 introduced a new option that allows you to embed an image in markdown without using asset_img tag plugin.

To enable:

```
_config.yml
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
```

Once enabled, an asset image will be automatically resolved to its corresponding post’s path. For example, "image.jpg" is located at "/2020-01-02-foo/image.jpg", meaning it is an asset image of "/2020-01-02-foo/" post, `![](image.jpg)` will be rendered as `<img src="/2020-01-02-foo/image.jpg">`.

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
$ hexo server -p 5000
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
$ hexo generate --deploy
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

### Add tags and categories

```yaml
tags:
  - Testing
  - Another Tag
categories: Label
```

More info: [Tags](http://theme-next.iissnan.com/theme-settings.html)
