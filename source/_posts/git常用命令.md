---
title: git常用命令
date: 2022-09-19 22:23:15
cover: https://wltc2-1258834326.cos.ap-guangzhou.myqcloud.com/2023/01/09/63bb9d9de16d0.jpg
tags: 
  - Java
  - Git
categories: 技术记录
description: 简单记录一下git的常用命令
---





**Git GUI：Git提供的图形界面工具**
**Git Bash：Git提供的命令行工具**

## git分布式版本控制工具

```
cover: ./img/wuwang.png
概述：Git是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过Git仓库来存储和管理这些文件，Git仓库分为两种:
	●本地仓库:开发人员自己电脑上的Git仓库
	●远程仓库:远程服务器上的Git仓库,gitHub,gitee,gitLab,BitBucker
注意：第一次安装要设置用户名和email地址
	git config --global user.name "yixin"
	git config --global user.email "heelo@qq.com"
	git config --list  查看配置信息
	git init  初始化(创建默认分支master)
```



### 1.Git工作流程图

​	1. clone（克隆）: 从远程仓库中克隆代码到本地仓库
​	2. checkout （检出）:从本地仓库中检出一个仓库分支然后进行修订
​	3. add（添加）: 在提交前先将代码提交到暂存区
​	4. commit（提交）: 提交到本地仓库。本地仓库中保存修改的各个历史版本
​	5. fetch (抓取) ： 从远程库，抓取到本地仓库，不进行任何的合并动作，一般操作比较少。
​	6. pull (拉取) ： 从远程库拉到本地库，自动进行合并(merge)，然后放到到工作区，相当于
​	fetch+merge
​	7. push（推送） : 修改完成后，需要和团队成员共享代码时，将代码推送到远程仓库

### 2.基础命令



	ls/ll 查看当前目录
	cat 查看文件内容
	touch 创建文件
	vi vi编辑器（使用vi编辑器是为了方便展示效果，学员可以记事本、editPlus、notPad++等其它编辑器）
	
	状态之间的转换：
	1. git add (工作区 --> 暂存区)
	2. git commit (暂存区 --> 本地仓库)
	
	查看修改的状态（status）
	作用：查看的修改的状态（暂存区、工作区）
	命令形式：git status
	
	添加工作区到暂存区(add)
	作用：添加工作区一个或多个文件的修改到暂存区
	命令形式：git add 单个文件名|通配符
	将所有修改加入暂存区：git add .
	
	提交暂存区到本地仓库(commit)
	作用：提交暂存区内容到本地仓库的当前分支
	命令形式：git commit -m '注释内容'
	
	查看提交日志(log)
	作用:查看提交记录
	命令形式：git log [option]
	options
	--all 显示所有分支
	--pretty=oneline 将提交信息显示为一行
	--abbrev-commit 使得输出的commitId更简短
	--graph 以图的形式显示
	
	起别名(alias)
	alias 新指令名='旧指令名'
	
	版本回退
	作用：版本切换
	命令形式：git reset --hard commitID
	commitID 可以使用 git-log 或 git log 指令查看
	如何查看已经删除的记录？
	git reflog
	这个指令可以看到已经删除的提交记录
### 3.分支

	head指的是当前分支
	查看本地分支
	命令：git branch
			git branch -r 列出所有远程分支
			git branch -a 列出所有分支
	
	创建本地分支
	命令：git branch 分支名
	
	*切换分支(checkout)
	命令：git checkout 分支名
	我们还可以直接切换到一个不存在的分支（创建并切换）
	命令：git checkout -b 分支名
	
	*合并分支(merge)
	一个分支上的提交可以合并到另一个分支
	命令：git merge 分支名称
	步骤：先切换到主分支，然后进行合并分支，然后输入注释即可
	
	删除分支
	不能删除当前分支，只能删除其他分支
	git branch -d b1 删除分支时，需要做各种检查
	git branch -D b1 不做任何检查，强制删除

### 4.冲突

​	当两个分支上对文件的修改可能会存在冲突，例如同时修改了同一个文件的同一行，这时就需要手动解决冲突，解决冲突步骤如下：
​	1. 处理文件中冲突的地方
​	2. 将解决完冲突的文件加入暂存区(add)
​	3. 提交到仓库(commit)

### 5.SSH公钥

​	生成SSH公钥
​		ssh-keygen -t rsa
​		不断回车
​	如果公钥已经存在，则自动覆盖

	Gitee设置账户共公钥
		获取公钥
			cat ~/.ssh/id_rsa.pub
	验证是否配置成功
	ssh -T git@gitee.com

### 6.远程仓库



	添加远程仓库
	此操作是先初始化本地库，然后与已创建的远程库进行对接。
	命令： git remote add <远端名称> <仓库路径>
	远端名称，默认是origin，取决于远端服务器设置
	仓库路径，从远端服务器获取此URL
	例如: git remote add origin git@gitee.com:yixin0724/git_test.git


​	
​	查看远程仓库
​	命令：git remote
​	
​	推送到远程仓库
​	命令：git push [-f] [--set-upstream] [远端名称 [本地分支名][:远端分支名] ]
​	如果远程分支名和本地分支名称相同，则可以只写本地分支
​	git push origin master
​	-f 表示强制覆盖
​	--set-upstream 推送到远端的同时并且建立起和远端分支的关联关系。
​	git push --set-upstream origin master
​	如果当前分支已经和远端分支关联，则可以省略分支名和远端名。
​	git push 将master分支推送到已关联的远端分支。
​	
​	本地分支与远程分支的关联关系
​	查看关联关系我们可以使用 git branch -vv 命令
​	
​	从远程仓库克隆
​	如果已经有一个远端仓库，我们可以直接clone到本地。
​	命令: git clone <仓库路径> [本地目录]
​	本地目录可以省略，会自动生成一个目录
​	
	从远程仓库中抓取和拉取
	远程分支和本地的分支一样，我们可以进行merge操作，只是需要先把远端仓库里的更新都下载到本
	地，再进行操作。
	抓取 命令：git fetch [remote name] [branch name]
	抓取指令就是将仓库里的更新都抓取到本地，不会进行合并
	如果不指定远端名称和分支名，则抓取所有分支。
	
	拉取 命令：git pull [remote name] [branch name]
	拉取指令就是将远端仓库的修改拉到本地并自动进行合并，等同于fetch+merge
	如果不指定远端名称和分支名，则抓取所有并更新当前分支。
	注意：如果当前本地仓库不是从远程仓库克隆，而是本地创建的仓库,井且仓库中存在文件,此时再从远程仓库拉取文件的时候会报错(fatal: refusing to merge unrelated histories )
	解决此问题可以在git pull命令后加入参数--allow-unrelated-histories
	然后进入vim里面输入注释
	
	解决合并冲突
	可以先进行git pull然后在进行git push


​	


### 7.标签

```
	概述：Git中的标签，指的是某个分支某个特定时间点的状态。通过标签，可以很方便的切换到标记时的状态。
	比较有代表性的是人们会使用这个功能来标记发布结点(v1.0、v1.2等)。
	常用命令：
		git tag：列出已经有的标签
		git tag [name]：创建标签
		git push 远程仓库名字 标签名字：将标签推送到远程仓库
		git cherkout -b [NewbranchName] [name]：检出标签(也就是把标签当时的状态下载下来，在这里分支是新的，会把文件状态赋给他)
		
		
		
idea中使用git
步骤：
	①去setting里面的version control进行配置，然后对git的路径进行选则cmd/git.exe
	②获取仓库
	从远程仓库克隆(*)
		点击VCS里面的Get from Version Control，然后选择克隆的仓库和要放在哪里选择即可
	操作：
		查看远程仓库
			点击git里面的Manage Remotes即可
		添加远程仓库
		推送至远程仓库
			右键项目点击git里面的push即可
		注意：如果远程的与本地不一样，先拉取，或者创建仓库时创建空的仓库
		从远程仓库拉取
			右键git里面的pull

	本地初始化仓库
		点击idea中的VCS，然后点击create  git repository，然后选择项目文件，相当于把项目当成一个仓库

分支操作				
将分支推送到远程仓库，直接点击分支点击对应的push

合并分支
假设需要把b1合并到master下，先切换的master下，然后点击b1里面的合并当前分支即可


.gitignore是定义哪些不需要上传的文件
```

