---
title: Git文件上传的一个错误分析
date: 2018-05-11 09:58:24
tags: 随笔
---

# 需求： 通过git上传本地文件到github远程仓库
## 上传一个本地本地文件的操作流程

 1. 在需要上传的文件夹下，打开Git bash here。
 2. git init初始化这个文件夹，生成.git的文件夹。
 3. git add . (注意这有一个点)，上传所有文件。
 4. git commit -m '描述信息' ，提交到本地仓库。
 5. git remote add origin git@github.com:AFeng521web/仓库名.git，关联远程库。
 6. git push -u origin master，提交到远程仓库。
 
<!-- more -->

## 源代码如下
```
赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop
$ mkdir test

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop
$ cd test

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test
$ git init
Initialized empty Git repository in C:/Users/赵亚峰/Desktop/test/.git/

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test (master)
$ git add .

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test (master)
$ git commit -m '一次错误的提交'
[master (root-commit) 096e483] 一次错误的提交
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test (master)
$ git remote add origin git@github.com:AFeng521web/test.git

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test (master)
$ git push -u origin master
To github.com:AFeng521web/test.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:AFeng521web/test.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test (master)
$ git pull --rebase origin master
warning: no common commits
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:AFeng521web/test
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: 一次错误的提交

赵亚峰@DESKTOP-0JOAFKG MINGW64 ~/Desktop/test (master)
$ git push -u origin master
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 315 bytes | 315.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:AFeng521web/test.git
   0ecc51a..8696bf9  master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.

```

### To github.com:AFeng521web/test.git
### ! [rejected]        master -> master (fetch first)
### error: failed to push some refs to 'git@github.com:AFeng521web/test.git'

### 接下来，分析产生这个错误的原因：
因为在远程的github上创建了readme.md文件，所以一直导致上传不成功。

### 解决办法
```
$ git pull --rebase origin master
```
把远程的readme.md文件拉取到本地，在通过```git push -u origin master
```关联远程库，这下就会上传成功了。

## 一点心得：要是通过git上传本地文件，最好不要在github仓库创建时创建readme.md文件。


