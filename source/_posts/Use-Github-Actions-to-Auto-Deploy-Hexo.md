---
title: Use Github Actions to Auto Deploy Hexo
date: 2021-12-21 16:46:56
tags: 
    - Hexo 
    - Github
categories: 运维 
---
## Github Actions、Github Pages

简单的说Actions就是Github推出的CI工具，代码推送到Github后，可以触发一些列的动作。

Github Pages，Githu给出的最简介绍，"Websites for you and your projects."

## Personal access tokens and repository's Secrets

因为需要从blog的库去方位github.io的库，所以之前需要一些token的传递。

### Personal access tokens

在"Developer settings"中打开"Personal access tokens"，点击"Generate new token"生成新的token，"scopes"中只选择"repo"。

### Secrets

在Repository的Settings中，打开Secrets的选项，点击"New repository secret"生成Repository secrets，注意这个名称和后面的deply.yml中的一致性。

## deploy.yml

Github Pages是放在同名的仓库下面的，因为我使用了JamesIves/github-pages-deploy-action的deploy方式，这里有要注意的地方。在zhydong258.github.com下面，有两个branch，一个是master，一个是source。所有的hexo的源文件都放在source下面，提交后通过Actions自动在master下面生成静态页面。

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
