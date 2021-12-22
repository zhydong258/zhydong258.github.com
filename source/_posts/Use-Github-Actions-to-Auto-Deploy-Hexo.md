---
title: Use Github Actions to Auto Deploy Hexo
date: 2021-12-21 16:46:56
tags: 
    - Hexo 
    - Github
categories: 运维 
---
## Github Actions、Github Pages

简单的说Actions就是Github推出的CI工具，代码推送到Github后，可以触发一系列的动作。

Github Pages，Githut给出的最简介绍，"Websites for you and your projects."

## Personal access tokens and repository's Secrets

因为需要从Blog的库去访问username.github.com的库，所以之间需要一些token的传递。

### Personal access tokens

在"Developer settings"中打开"Personal access tokens"，点击"Generate new token"生成新的token，"scopes"中只选择"repo"，生成token字符串。

### Secrets

在Repository的Settings中，打开Secrets的选项，点击"New repository secret"生成Repository secrets，填写名称和token的字符串，注意这个名称和后面的deply.yml中的一致性。

## deploy.yml

Github Pages是放在用户同名仓库下面的，因为我使用了JamesIves/github-pages-deploy-action的deploy方式，这里有要注意的地方。在zhydong258.github.com下面，有两个branch，一个是master，一个是source。所有的hexo的源文件都放在source下面，提交后通过Actions自动在master下面生成静态页面。

workflows下的部署文件。
``` yaml
name: Deploy GitHub Pages

# 触发条件：在 push 到 source 分支后
on:
  push:
    branches:
      - source
    paths-ignore: # 下列文件的变更不触发部署，可以自行添加
      - README.md
      - LICENSE

# 任务
jobs:
  build-and-deploy:
    # 服务器环境：最新版 Ubuntu
    runs-on: ubuntu-latest
    steps:
      # 拉取代码
      - name: 📦 Checkout
        uses: actions/checkout@v2
        with:
          persist-credentials: false
      
      # 生成静态文件
      - name: 🔧 Install and Build
        run: |
          npm install hexo-cli -g
          npm install
          hexo clean
          hexo generate
        # 如果采用 yarn 进行包管理，则使用下面的代码
        #  yarn
        #  yarn build

      # 部署到 GitHub Pages
      - name: 🚀 Deploy
        uses: JamesIves/github-pages-deploy-action@4.1.7
        with:
          # GitHub 的 Personal access tokens
          token: ${{secrets.BLOG_DEPLOY}}
          # 要部署到的分支
          branch: master
          # 上一步生成的静态文件的地址
          folder: public
```
## Github branch

因为需要创建source这个branch，简单的命令如下：

``` bash
## step 1：创建本地分支
git branch source

## step 2：切换到新创建的分支
git checkout source

# 上述两条命令等价于 `git checkout -b source`

## step 3：添加项目中所有文件
git add .

## step 4：
git commit -m "New Branch source"

## step 5：将新分支 push 到 GitHub
git push --set-upstream origin source
```

## 为什么用Actions

<!-- more -->

最开始的hexo环境和blog源码都是在本地的，使用hexo的deploy命令发布到到username.github.com仓库，这也没什么不好。后来，想在不同的电脑上去写blog，发现hexo的环境容易搭建，但是没有blog的源码，随在GitHub上建立名为Blog的仓库，用于同步不同电脑上的源码，也用于备份。

这样只需要在安装好hexo环境的电脑上clone/pull下来Blog这个仓库就可以开始blog的编辑了。hexo new hexo generate hexo deploy,发布blog到username.github.com这个仓库，最后git push把blog的源码推送到GitHub中的Blog仓库。

这样的工作流本来也凑合，但是想用Actions实现过程的自动化，也就是省略掉hexo generate和hexo deploy，所以增加了上面的workflows。最初的想法是push到Blog仓库后，触发Actions，然后自动deploy到username.github.com。Actions的yml文件到是比较好写的，网上的教程是利用ssh的私钥和公钥的方式，从Blog向username.github.com去部署，我不想采用这种方式，所以才出现了上面的[deploy.yml](#deploy-yml)中的实现方式和配置方式。

今天在看GitHub关于Pages的文档时，发现其可以配置source对应的branch和folder，不过folder只能是/(root)和/docs这个两种。我修改了hexo的config，然其把文件发布到docs下面，然后直接push到username.github.com，并在username.github.com的Pages中设置/docs。这样的方式好处是只用master分支就可以解决blog源码的备份和Pages的发布。

能不能再优化下，username.github.com下的master存放blog的源代码，再gh-pages下发布Pages，利用Actions自动发布。调整下吧，Pages的source设置切换到gh-pages这个branch的/(root)。修改下上面的yml文件。

``` yaml
name: Deploy GitHub Pages

# 触发条件：在 push 到 source 分支后
on:
  push:
    branches:
      - master
    paths-ignore: # 下列文件的变更不触发部署，可以自行添加
      - README.md
      - LICENSE

# 任务
jobs:
  build-and-deploy:
    # 服务器环境：最新版 Ubuntu
    runs-on: ubuntu-latest
    steps:
      # 拉取代码
      - name: 📦 Checkout
        uses: actions/checkout@v2
        with:
          persist-credentials: false
      
      # 生成静态文件
      - name: 🔧 Install and Build
        run: |
          npm install hexo-cli -g
          npm install
          hexo clean
          hexo generate
        # 如果采用 yarn 进行包管理，则使用下面的代码
        #  yarn
        #  yarn build

      # 部署到 GitHub Pages
      - name: 🚀 Deploy
        uses: JamesIves/github-pages-deploy-action@4.1.7
        with:
          # GitHub 的 Personal access tokens
          token: ${{secrets.BLOG_DEPLOY}}
          # 要部署到的分支
          branch: gh-pages
          # 上一步生成的静态文件的地址
          folder: public
```