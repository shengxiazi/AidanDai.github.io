#git再次提交（更改分支）

注：本文永久更新链接，markdown格式源码  [Github: Aidan Dai](https://github.com/AidanDai/blog)

******************************

[TOC]

**前言：**

由于团队决定使用**git集中式工作流**开发项目，集中式工作流如下图：

![集中式工作流](http://git.oschina.net/progit/figures/18333fig0501-tn.png)

但是在git集中式工作流下并不能控制具体分支的权限，所以开发人员有可能会出现误操作，将自己独立开发分支的内容提交到了主分支上，例如开发人员
在本地 master 分支上做了一个 commit ( 38361a68138140827b31b72f8bbfd88b3705d77a ) ， 如何把它放到本地 develope 分支上？

##使用 cherry-pick

根据git 文档：

<pre>Apply the changes introduced by some existing commits
就是对已经存在的 commit 进行 apply (可以理解为再次提交）
</pre>


1、简单用法：

```git
git cherry-pick <commit id>
```

例如：

```git
$ git checkout develope
$ git cherry-pick 38361a68     
```

- 这个 38361a68 号码，位于：
```git
$ git log
commit 38361a68138140827b31b72f8bbfd88b3705d77a
Author: Siwei Shen <siwei.shen@focusbeijing.com>
Date:   Sat Dec 10 00:09:44 2011 +0800
```

- 如果顺利，就会正常提交。结果：
```git
Finished one cherry-pick.
# On branch develope
# Your branch is ahead of 'origin/develope' by 3 commits.
```

- 如果在cherry-pick 的过程中出现了冲突
```git
Automatic cherry-pick failed.  After resolving the conflicts,
mark the corrected paths with 'git add <paths>' or 'git rm <paths>'
and commit the result with:
git commit -c 15a2b6c61927e5aed6718de89ad9dafba939a90b
```

- 就跟普通的冲突一样，手工解决：
git status    # 看哪些文件出现冲突,手动解决它,重新 cherry-pick
both modified:      app/models/user.rb

参考原文地址：

[wangjia55的专栏：git cherry-pick. 如何把已经提交的commit, 从一个分支放到另一个分支](http://blog.csdn.net/wangjia55/article/details/8489748)
