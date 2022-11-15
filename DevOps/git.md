---
title: Git
---

一个分布式的版本控制系统

## 常用的命令

`git init`
初始化一个项目（在项目的根目录下生成一个.git 的文件）

`git add [filename]`
将文件提交到暂存区

`git status`
查看工作树状态 [🤏](https://git-scm.com/docs/git-status/zh_HANS-CN)

`git remote add`
管理远程仓库信息

`git commit`
提交到本地仓库

`git reset HARD [version]`
回退版本

`git log`
查看历史版本

`git fetch`
拉取远程仓库

`git merge`
合并代码

`git pull`
合并远程仓库代码 === git fetch + git merge

`git diff`
对比分支差异

`git branch`
创建分支

`git checkout`
创建分支并切换分支

## 一些用法记录

#### .gitignore 新写入的规则不生效问题
```js
git rm -r --cached <path>
```

#### 网络代理
```js
// 设置网络代理
git config --global http.proxy socks5://127.0.0.1:7000

// 获取网络代理
git config --global --get http.proxy

// 取消网络代理
git config --global --unset http.proxy
```

#### 克隆仓库最近的一次提交
```js
git clone <repo-url> --depth=1
```

## Git 与 SVN 的区别

> Git 是分布式的，SVN 是集中式（ 体现在 Git 可以在本地开发完先 commit 到本地，然后可以推送给不同远程仓库。推送给 github | gitee | gitlab 去中心化的 ）
>
> Git 有本地仓库 SVN 没有

## merge 和 rebase 区别

> merge 是将两个分支的历史合并到一起，现有分支并不会被更改，它会比对双方不同的文件缓存下来，生成一个 commit（适用于 master 分支，使得改动可以被追踪）
>
> rebase 是修改提交历史，比对双方的 commit，然后找出不同的去缓存（适用次分支）

[原理解读文章](https://www.cnblogs.com/mamingqian/p/9711975.html)
[阮一峰](http://www.ruanyifeng.com/blog/2018/10/git-internals.html)
[Git 官方](https://git-scm.com/book/zh/v2)
