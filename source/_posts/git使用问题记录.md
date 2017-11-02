---
title: git使用问题记录
date: 2017-11-02 11:28:57
tags:
	- git
---
### 1. 合并分支后，发现合并错了分支。master上还没有提交任何东西，想取消这次合并，让分支回到位，肿么办？
```
$ git reset --hard 【merge前的版本号】
``` 
### 2. 如果当前分支上git status 有文件改动列表 执行git checkout .无效
<!-- more -->
```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
(use "git add <file>..." to include in what will be committed)
pic2.58.com/m58/m3/img/car/jiancezhenbao/icon-close.png
pic2.58.com/m58/m3/img/car/jiancezhenbao/icon_right.png
pic2.58.com/m58/m3/img/car/jiancezhenbao/pc_n_icon.png
pic2.58.com/test.txt
static.58.com/car/app/2017/jianceyanbao_order.html
static.58.com/car/app/2017/jianceyanbao_zhenbao.html
static.58.com/car/m/2017/payfail.html
static.58.com/m58/m3/carused/web_app/jianceyanbao_search/
static.58.com/m58/m3/css/car/cst_app_post/cheshangtong_ershouche.css
static.58.com/m58/m3/js/car/cst_app_post/data/cheshangtong_ershouche.js
"\357\274\201"
nothing added to commit but untracked files present (use "git add" to track)
```
==nothing added to commit but untracked files present (use "git add" to track)==
这些文件又不是你想要修改的，执行git checkout . 无效

执行以下命令使用 
```
git clean -f     
```

### 3.git 解决冲突 merge之后，发现别的文件冲突，而自己也没有修改过这些文件
> - git reset HEAD  src/...**/** 文件
>- 然后再执行git checkout src/...**/** 文件
>- 这个时候再执行git commit 释放冲突状态
>- 然后git push

