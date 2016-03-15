#[ 笔记 ] git 基础命令在学习

注：本文永久更新链接，markdown格式源码  [Github: Aidan Dai](https://github.com/AidanDai/blog)

*******************************

[TOC]

## 一、前言

　　总结一下之前学习过的平时常用的git命令 。。。。

　　还是先贴张图（此图来自[阮一峰：Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)）

	![git 工作环境](http://image.beekka.com/blog/2014/bg2014061202.jpg)

## 二、git branch 和 git checkout

```git
git branch 						//查看当前分支
git branch -r 					//列出远程分支
git branch -a 					//列出所有分支

git branch branchName 			//创建分支
git checkout branchName 		//切换分支
git checkout -b branchName 		//创建并切换到分支

git checkout  					//后面不跟任何参数，则就是对工作区进行检查
git checkout --filename 		//从暂存区中恢复文件(确保filename与branch名称不同)

git status 						//查看状态
```

## 三、git clone 和 git remote

<pre>git clone <版本库的网址> <本地目录名></pre>
git clone支持多种协议，除了HTTP(s)以外，还支持SSH、Git、本地文件协议等，下面是一些例子

<pre>
$ git clone http[s]://example.com/path/to/repo.git/
$ git clone ssh://example.com/path/to/repo.git/
$ git clone git://example.com/path/to/repo.git/
$ git clone /opt/git/project.git
$ git clone ftp[s]://example.com/path/to/repo.git/
$ git clone rsync://example.com/path/to/repo.git/
</pre>

//SSH协议还有另一种写法
<pre>git clone [user@]example.com:path/to/repo.git/</pre>


```git
git remote
git remote -v  								//查看远程主机的网址
git remote show <主机名> 					//查看该主机的详细信息
git remote add <主机名> <网址> 				//添加远程主机
git remote rm <主机名>  					//删除远程主机
git remote rename <原主机名> <新主机名> 	//重命名远程主机
```
## 四、git pull 和 git push

<pre>
$ git pull <远程主机名> <远程分支名>:<本地分支名>
$ git push <远程主机名> <本地分支名>:<远程分支名>
                            from         to
</pre>

```git
git pull origin master:master
//取回origin主机的master分支，与本地的master分支合并

git push origin master:master
//推送本地的master分支，与origin主机的master分支合并



git pull origin master
//如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

git push origin master
//本地的master分支推送到origin主机的master分支。如果后者不存在，则会被新建

git pull origin
//本地的当前分支自动与对应的origin主机”追踪分支”(remote-tracking branch)进行合并。追踪分支一般是远程的同名分支

git push origin
//当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略

git pull
//当前分支自动与唯一一个追踪分支进行合并

git push
//当前分支只有一个追踪分支，那么主机名都可以省略

```

## 五、git merge 和 git rebase


###简单合并

![简单合并之前的分支](http://git.oschina.net/progit/figures/18333fig0313-tn.png)

```git
git checkout master
git merge hotfix
```

![快照前进合并之后的分支](http://git.oschina.net/progit/figures/18333fig0314-tn.png)

###复杂合并

```git
git checkout master
git merge iss53
```

![复杂合并之前的分支](http://git.oschina.net/progit/figures/18333fig0316-tn.png)

请注意，这次合并操作的底层实现，并不同于之前快进"Fast forward"式的并入方式。因为这次你的开发历史是从更早的地方开始分叉的。由于当前 master 分支所指向的提交对象（C4）并不是 iss53 分支的直接祖先，Git 不得不进行一些额外处理。就此例而言，Git 会用两个分支的末端（C4 和 C5）以及它们的共同祖先（C2）进行一次简单的三方合并计算。图 3-16 用红框标出了 Git 用于合并的三个提交对象：

这次，Git 没有简单地把分支指针右移，而是对三方合并后的结果重新做一个新的快照，并自动创建一个指向它的提交对象（C6）。这个提交对象比较特殊，它有两个祖先（C4 和 C5）。

值得一提的是 Git 可以自己裁决哪个共同祖先才是最佳合并基础；这和 CVS 或 Subversion（1.5 以后的版本）不同，它们需要开发者手工指定合并基础。所以此特性让 Git 的合并操作比其他系统都要简单不少。

![复杂合并之后的分支](http://git.oschina.net/progit/figures/18333fig0317-tn.png)


###简单衍合

![简单衍合之前的分支](http://git.oschina.net/progit/figures/18333fig0327-tn.png)

```git
git checkout experiment
git rebase master
```

它的原理是回到两个分支最近的共同祖先，根据当前分支（也就是要进行衍合的分支 experiment）后续的历次提交对象（这里只有一个 C3），生成一系列文件补丁，然后以基底分支（也就是主干分支 master）最后一个提交对象（C4）为新的出发点，逐个应用之前准备好的补丁文件，最后会生成一个新的合并提交对象（C3'），从而改写 experiment 的提交历史，使它成为 master 分支的直接下游

![简单衍合之后的分支](http://git.oschina.net/progit/figures/18333fig0329-tn.png)


###复杂衍合

![复杂衍合之前的分支](http://git.oschina.net/progit/figures/18333fig0331-tn.png)

```git
git rebase --onto master server client
```

这好比在说：“取出 client 分支，找出 client 分支和 server 分支的共同祖先之后的变化，然后把它们在 master 上重演一遍”。是不是有点复杂？不过它的结果如图 3-32 所示，非常酷（译注：虽然 client 里的 C8, C9 在 C3 之后，但这仅表明时间上的先后，而非在 C3 修改的基础上进一步改动，因为 server 和 client 这两个分支对应的代码应该是两套文件，虽然这么说不是很严格，但应理解为在 C3 时间点之后，对另外的文件所做的 C8，C9 修改，放到主干重演。）：

![复杂衍合一次后的分支](http://git.oschina.net/progit/figures/18333fig0332-tn.png)


现在可以快进 master 分支了

```git
git checkout master
git merge client
```

![快进 master 分支](http://git.oschina.net/progit/figures/18333fig0333-tn.png)

现在我们决定把 server 分支的变化也包含进来。我们可以直接把 server 分支衍合到 master，而不用手工切换到 server 分支后再执行衍合操作<pre>git rebase [主分支] [特性分支] </pre>命令会先取出特性分支 server，然后在主分支 master 上重演：


![复杂衍合第二次后的分支](http://git.oschina.net/progit/figures/18333fig0334-tn.png)


然后就可以快进主干分支 master 了：

```git
git checkout master
git merge server

```

现在 client 和 server 分支的变化都已经集成到主干分支来了，可以删掉它们了。最终我们的提交历史会变成图 3-35 的样子：

```git
git branch -d client
git branch -d server
```
![复杂衍合完成后的分支](http://git.oschina.net/progit/figures/18333fig0335-tn.png)


### 衍合的风险

呃，奇妙的衍合也并非完美无缺，要用它得遵守一条准则：

**一旦分支中的提交对象发布到公共仓库，就千万不要对该分支进行衍合操作。**

如果你遵循这条金科玉律，就不会出差错。否则，人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你。

在进行衍合的时候，实际上抛弃了一些现存的提交对象而创造了一些类似但不同的新的提交对象。如果你把原来分支中的提交对象发布出去，并且其他人更新下载后在其基础上开展工作，而稍后你又用 git rebase 抛弃这些提交对象，把新的重演后的提交对象发布出去的话，你的合作者就不得不重新合并他们的工作，这样当你再次从他们那里获取内容时，提交历史就会变得一团糟。

### 手动解决冲突
　　

## 六、git log


```git
git log --stat -n 5   		// 简单的列出了修改过的文件

git log -p  -n 5  			// 详细的列出修改过的文件，及提交文件的对比

git log --graph 			// ASCII 字符串表示的简单图形，形象地展示了每个提交所在的分支及其分化衍合情况


git log --all --decorate --graph

git log --pretty=oneline 	// 只显示哈希值和提交说明

git log --pretty=oneline/short/full/fuller/format:""(格式等)

git log --name-only  		// 仅在提交信息后显示已修改的文件清单

git log --no-merges 		// 不显示merge的log
```

### 常用的命令：

```git
git log --name-status -n 5 --no-merges path/filename 			// 显示新增、修改、删除的文件清单(不包含merge的log)
git log --name-status --skip=5 -n 5 --no-merges path/filename 	// 略过5条,从第6条开始取5条log
```


## 七、git stash

### 保存工作现场

```git
git stash     					// 保存工作现场 do some work

git pop 						// 返回工作现场

git stash list 					//查看 stash 队列

git stash pop stash@{num}
// num就是list中要恢复的工作现场编号
// 使用pop命令恢复的工作现场，其对应的stash 在队列中删除

git stash apply stash@{num}
// num就是list中要恢复的工作现场编号
// 使用apply命令恢复的工作现场，其对应的stash 在队列中不删除

git stash clear 				// 情况 stash 队列

```
## 八、其他命令

```git
git revert [commit_id]  		//回退到指定的commit前版本
```
## 九、参考资料

[我所记录的git命令（非常实用）
](http://www.cnblogs.com/fanfan259/p/4810517.html)

[Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

[git checkout 命令详解](http://www.tuicool.com/articles/A3Mn6f)

[Git 分支](http://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html)
