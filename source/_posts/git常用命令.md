---
title: git常用命令
date: 2022-09-19 22:23:15
cover: https://pic.imgdb.cn/item/66ebef4bf21886ccc0d0e972.jpg
tags: 
  - Java
  - Git
categories: 技术记录
description: 简单记录一下git的常用命令
---







# 常用积累

```
建立连接
	方法一：https连接
		也就是直接使用仓库相对于的http网址，一般会进行验证登录账号名和密码
	方法二：ssh连接
		这种格式一般就是git@xxx.git的形式了，一般要进行ssh免密，也就是在本地使用ssh-keygen -t rsa生成密钥的.pub文件，然后复制该密钥，去远程仓库配置ssh免密即可。
		
本地保存凭证(避免多次验证)
	git config credential.helper store	#永久保存凭证
	git config --global credential.helper store		#这会将后续的用户名和密码存储在~/.git-credentials
	git config --global会在~/.gitconfig文件添加配置
#或手动保存sudo vim ~/.git-credentials
	#添加如下，如
	https://<用户名>:<密码>@<远程仓库地址>
	#然后再保存
	git config --global credential.helper store
	
克隆
	克隆本地的GitLab项目
        克隆默认分支：git clone http://用户名:你的GitLabToken@192.168.9.24/yijia-hotel/yijia-hotel.git
        克隆master分支：git clone -b master --single-branch http://用户名:你的GitLabToken@192.168.9.24/yijia-hotel/yijia-hotel.git
        username：yixin
        token：你的GitLabToken(GitLab个人资料申请Access Tokens)
	

初始化
    初始化一个新的仓库：git init
    从远程仓库克隆项目到本地：git clone [url]
    	再使用git clone拉取到本地时，会把仓库自带的git信息带过来，建议直接用自带的。

文件操作
    查看工作区状态：git status
    添加文件到暂存区：git add [file1] [file2] ... 或 git add .
    提交更改到本地仓库：git commit -m "commit message"
    
查看历史记录
	查看提交日志：git log
    以简洁的方式查看提交历史：git log --oneline
    图形化显示分支合并历史：git log --graph

分支管理
    列出所有本地分支：git branch
    创建新分支：git branch [branch-name]
    切换分支：git checkout [branch-name]
    创建并切换到新分支：git checkout -b [branch-name]
    删除本地分支：git branch -d [branch-name]
    强制删除未被完全合并的分支：git branch -D [branch-name]
    合并指定分支到当前分支：git merge [branch-name]
    
远程操作
    查看远程仓库信息：git remote -v
    添加远程仓库：git remote add origin [url]
    	http：git remote add origin http://192.168.9.24/ai/uav-inspection-identification
    	git：
    拉取远程仓库更新：git pull [remote] [branch]
    推送本地分支到远程仓库：git push [remote] [branch]
    删除远程分支：git push origin --delete [branch-name]
    
撤销与重置
    取消暂存区中的文件更改：git reset HEAD [file]
    恢复工作区中的文件至最近一次提交的状态：git checkout -- [file]
    回退到特定的提交：git reset --hard [commit-id]
    
标签管理
    创建标签：git tag [tag-name]
    查看所有标签：git tag
    删除本地标签：git tag -d [tag-name]
    删除远程仓库标签：git push origin --delete tag [tag-name]
    
其他命令
    暂存当前工作进度：git stash
    应用之前暂存的工作进度：git stash apply
    恢复并删除之前暂存的工作进度：git stash pop
    查看引用日志（用于恢复误删的提交或分支）：git reflog

清空add的暂存区
	方法一：git reset HEAD [filename] 。然后在使用 git status检查状态
	方法二：git restore --staged .或者[filename]
	方法三：git rm --cached [filename] / git rm --cached -r [directory]

撤销commit，n代表撤销几次的commit
git reset --soft HEAD~n
```





# 前言

Git GUI：Git提供的图形界面工具

Git Bash：Git提供的命令行工具

.gitignore：放在项目根目录下，定义哪些不需要上传的文件

概述：Git是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过Git仓库来存储和管理这些文件，Git仓库分为两种:

​	本地仓库:开发人员自己电脑上的Git仓库

​	远程仓库:远程服务器上的Git仓库,gitHub,gitee,gitLab,BitBucker

注意：第一次安装要设置用户名和email地址

​	git config --global user.name "name"

​	git config --global user.email "your_email@example.com"

​	git config --list  查看配置信息

​	git init  初始化(创建默认分支master)



# Git工作流程

​	1.clone（克隆）: 从远程仓库中克隆代码到本地仓库

​	2.checkout（检出）:从本地仓库中检出一个仓库分支然后进行修订

​	3.add（添加）: 在提交前先将代码提交到暂存区

​	4.commit（提交）:提交到本地仓库。本地仓库中保存修改的各个历史版本

​	5.fetch(抓取) ：从远程库，抓取到本地仓库，不进行任何的合并动作，一般操作比较少。

​	6.pull(拉取)：从远程库拉到本地库，自动进行合并(merge)，然后放到到工作区，相当于fetch+merge

​	7.push（推送）:修改完成后，需要和团队成员共享代码时，将代码推送到远程仓库



# 基础命令

​	ls/ll：查看当前目录

​	cat：查看文件内容

​	touch：创建文件

​	vi：vi编辑器

## 状态之间的转换

1. git add (工作区 --> 暂存区)
2. git commit (暂存区 --> 本地仓库)

## 查看修改的状态（status）

作用：查看的修改的状态（暂存区、工作区）

命令形式：git status



## 添加工作区到暂存区(add)

作用：添加工作区一个或多个文件的修改到暂存区

命令形式：git add 单个文件名|通配符

将所有修改加入暂存区：git add .

添加两个文件：git add file1.txt file2.txt

添加整个目录：git add directory/

添加所有txt文件：git add *.txt

刷新暂存区状态：git add --refresh

注意：

​	`git add` 只是将更改放入暂存区，并不意味着最终提交。只有通过 `git commit` 提交后，更改才会被永久保存到本地仓库。

​	在没有提交的情况下多次运行 `git add` 对同一个文件，只会保留最后一次的状态。

​	使用 `.gitignore` 文件可以帮助你避免无意中添加不需要跟踪的文件



## 提交暂存区到本地仓库(commit)

作用：提交暂存区内容到本地仓库的当前分支

命令形式：git commit -m '注释内容'



## 查看提交日志(log)

作用：查看提交记录

命令形式：git log [option]

options

​	--all 显示所有分支

​	--pretty=oneline 将提交信息显示为一行

​	--abbrev-commit 使得输出的commitId更简短

​	--graph 以图的形式显示



## 起别名(alias)

alias 新指令名='旧指令名'



## 版本回退(reset)

作用：版本切换

命令形式：git reset --hard commitID

commitID 可以使用 git-log 或 git log 指令查看

查看已经删除的记录：git reflog









# 分支

​	head指的是当前分支

## 查看本地分支

​	命令：git branch

​		git branch -r 列出所有远程分支

​		git branch -a 列出所有分支



## 创建本地分支

命令：git branch 分支名



## *切换分支(checkout)

从当前分支切换到指定的分支：git checkout 分支名

创建并切换到一个新的分支：`git checkout -b <new-branch-name>`。

强制切换到指定分支，并丢弃所有未提交的更改：`git checkout -f <branch-name>`

进入分离头指针状态：`git checkout <commit-hash>`

​	使用特定提交的哈希值来切换到那个提交的状态。在这种状态下，HEAD 不指向任何分支，而是直接指向一个具体的提交。

​	这通常用于查看历史提交的内容或者测试某个历史状态，但需要注意的是，在这种状态下进行的更改不会自动关联到任何分支。



基于特定提交创建新分支：`git checkout -b <new-branch-name> <commit-hash>`



## *合并分支(merge)

一个分支上的提交可以合并到另一个分支

命令：git merge 分支名称

步骤：先切换到主分支，然后进行合并分支，然后输入注释即可



## 删除分支

不能删除当前分支，只能删除其他分支

git branch -d b1 删除分支时，需要做各种检查

git branch -D b1 不做任何检查，强制删除





# 冲突

​	当两个分支上对文件的修改可能会存在冲突，例如同时修改了同一个文件的同一行，这时就需要手动解决冲突，解决冲突步骤如下：

 - 处理文件中冲突的地方
 - 将解决完冲突的文件加入暂存区(add)
 - 提交到仓库(commit) ,使用-i参数强制提交

# SSH公钥

```
推荐：ssh-keygen -t ed25519 -C "你的邮箱@example.com"

一般会让你选择保存路径，如下：
	Enter file in which to save the key:
直接回车即可，默认保存到：~/.ssh/id_ed25519

然后会问你passphrase：
	Enter passphrase:
这个 passphrase 是给私钥加密用的。
建议设置一个 passphrase，尤其是笔记本电脑。
```

生成SSH公钥：ssh-keygen -t rsa（不断回车，如果公钥已经存在，则自动覆盖）

指定密钥长度和文件名：ssh-keygen -t rsa -b 4096 -C "注释" -f ~/.ssh/id_rsa_scsf

Github设置账户公共密钥

​	个人账户设置，密钥部署/ssh，将生成的~/.ssh/id_rsa.pub公钥文件添加进去。设置这种全局密钥，不用再为单独库进行设置密钥。

验证是否配置成功

​	ssh -T git@github.com	//若22端口失败，尝试443端口

在.ssh下创建config文件，添加内容

```
Host github.com
  Hostname ssh.github.com
  Port 443
```

ssh -vT git@github.com	//如果一直连接报错，用这个查看日志



关于ssh下的known_hosts 文件

- **用途**：每当您通过 SSH 首次连接到一个远程主机时，SSH 客户端会自动将该主机的公钥指纹（通常是主机密钥）存储在您的本地 `~/.ssh/known_hosts` 文件中。这是为了确保下次连接同一台主机时，其提供的密钥与之前记录的相匹配。如果密钥不匹配（例如，因为主机已被重新安装或替换），SSH 会发出警告，提示可能存在安全风险。
- **内容**：此文件包含了一系列主机名、IP地址以及对应的公钥信息。它有助于验证远程主机的真实性，是 SSH 安全模型的重要组成部分。

known_hosts.old 文件

- **用途**：`known_hosts.old` 文件通常是在执行某些操作（如使用 `ssh-keygen -R` 命令移除特定主机条目或更新 `known_hosts` 文件时）自动生成的备份文件。它保存了 `known_hosts` 文件的前一版本，以便在需要时可以恢复之前的设置。
- **内容**：这个文件的内容基本上是旧版 `known_hosts` 文件的副本，包含了曾经被标记或修改前的所有主机信息。

简而言之，`known_hosts` 文件用于维护已知主机的安全性验证信息，而 `known_hosts.old` 则作为更改前的备份，以提供一种恢复机制以防出现错误或其他问题。保持对这些文件的关注和适当的管理可以帮助提高你的 SSH 使用体验的安全性和可靠性。如果你发现不再需要 `known_hosts.old` 文件，也可以安全地删除它，不过保留它作为一种额外的安全措施也是个不错的选择。





# 远程仓库

## 添加远程仓库

​	此操作是先初始化本地库，然后与已创建的远程库进行对接。

​	命令： git remote add <远端名称> <仓库路径>

​	远端名称，默认是origin，取决于远端服务器设置

​	仓库路径，从远端服务器获取此URL

​	例如: git remote add origin git@gitee.com:yixin0724/git_test.git



## 查看远程仓库

命令：git remote -v



## 推送到远程仓库

命令：git push [-f] [--set-upstream] [远端名称 [本地分支名][:远端分支名] ]

如果远程分支名和本地分支名称相同，则可以只写本地分支

git push origin master

​	-f 表示强制覆盖

​	--set-upstream 推送到远端的同时并且建立起和远端分支的关联关系。

例如：git push --set-upstream origin master

如果当前分支已经和远端分支关联，则可以省略分支名和远端名。

git push 将master分支推送到已关联的远端分支。



## 本地分支与远程分支的关联关系

git branch -vv：查看关联关系



## 从远程仓库克隆

如果已经有一个远端仓库，我们可以直接clone到本地。

命令: git clone <仓库路径> [本地目录]

本地目录可以省略，会自动生成一个目录



## 从远程仓库中抓取和拉取

远程分支和本地的分支一样，我们可以进行merge操作，只是需要先把远端仓库里的更新都下载到本地，再进行操作。

抓取 命令：git fetch [remote name] [branch name]

抓取指令就是将仓库里的更新都抓取到本地，不会进行合并

如果不指定远端名称和分支名，则抓取所有分支。



拉取 命令：git pull [remote name] [branch name]

拉取指令就是将远端仓库的修改拉到本地并自动进行合并，等同于fetch+merge

如果不指定远端名称和分支名，则抓取所有并更新当前分支。

注意：如果当前本地仓库不是从远程仓库克隆，而是本地创建的仓库,井且仓库中存在文件,此时再从远程仓库拉取文件的时候会报错(fatal:refusing to merge unrelated histories )

解决此问题可以在git pull命令后加入参数--allow-unrelated-histories

然后进入vim里面输入注释



## 解决合并冲突

可以先进行拉取git pull，然后在进行推送git push







# 标签

​	概述：Git中的标签，指的是某个分支某个特定时间点的状态。通过标签，可以很方便的切换到标记时的状态。

​	比较有代表性的是人们会使用这个功能来标记发布结点(v1.0、v1.2等)。

​	常用命令：

​		git tag：列出已经有的标签

​		git tag [name]：创建标签

​		git push 远程仓库名字 标签名字：将标签推送到远程仓库

​		git cherkout -b [NewbranchName] [name]：检出标签(也就是把标签当时的状态下载下来，在这里分支是新的，会把文件状态赋给他)

注意：

- 切换分支前先提交本地的修改
- 代码及时提交，提交过了就不会丢



# git推送github

| 对比项               | HTTPS                                  | SSH                               |
| -------------------- | -------------------------------------- | --------------------------------- |
| 远程地址             | `https://github.com/user/repo.git`     | `git@github.com:user/repo.git`    |
| 身份验证方式         | Token / OAuth 凭据                     | SSH 公钥/私钥                     |
| 是否需要 GitHub 密码 | 不需要，密码认证已移除                 | 不需要                            |
| 第一次配置难度       | 较低                                   | 稍高                              |
| 日常使用体验         | 配好 GCM 后也很方便                    | 配好 SSH 后非常方便               |
| 适合场景             | 新手、公司代理网络、临时环境           | 长期开发、个人电脑、频繁 push     |
| 核心风险             | Token 泄露                             | 私钥泄露                          |
| 推荐保护方式         | GCM 保存 Token，最小权限，设置过期时间 | 私钥加 passphrase，使用 ssh-agent |

## HTTPS推送

现在用 Token / Git Credential Manager 验证身份

认证流程

```
git push
  ↓
Git 读取远程地址：
https://github.com/user/repo.git
  ↓
通过 HTTPS/TLS 安全连接 GitHub
  ↓
GitHub 要求认证
  ↓
Git 从 Credential Manager 取 Token
  ↓
如果没有保存过，就让你登录或输入 Token
  ↓
GitHub 检查 Token：
    1. Token 是否真实有效？
    2. Token 是否过期？
    3. Token 是否有这个仓库权限？
    4. Token 是否有写入 Contents 的权限？
    5. 账号是否被组织限制、SSO 限制等？
  ↓
通过则允许 push
```

## SSH 方法：用公钥 / 私钥验证身份

SSH 和 HTTPS 最大的区别是：

> HTTPS 主要靠 Token；SSH 主要靠密钥对。
>
> 私钥：留在你电脑本地
> 公钥：上传到 GitHub
>
> ~/.ssh/id_ed25519      私钥
> ~/.ssh/id_ed25519.pub  公钥
>
> 使用 SSH Key 连接 GitHub 时，需要把公钥添加到 GitHub 账户中；每次 Git 认证时，如果密钥设置了 passphrase，可能会要求输入 SSH Key 的 passphrase，除非你已经存储了它。

认证流程

```
git push
  ↓
Git 发现远程地址使用 SSH
  ↓
调用本地 ssh 客户端连接 git@github.com
  ↓
GitHub 发送一个随机挑战
  ↓
你的本地 SSH 客户端用私钥完成签名
  ↓
GitHub 用你账户中保存的公钥验证签名
  ↓
验证成功：说明你确实拥有对应私钥
  ↓
GitHub 再检查你是否有仓库写权限
  ↓
允许或拒绝 push
```

如果你的私钥设置了 passphrase，那么每次 push 都输入 passphrase 会比较麻烦。

这时可以使用 `ssh-agent`。

它的作用是：

> 临时帮你记住已经解锁的私钥。

流程是：

```
第一次使用 SSH Key
  ↓
输入私钥 passphrase
  ↓
ssh-agent 记住解锁后的私钥
  ↓
后续 git push 不需要重复输入
```

常见命令：

```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

测试 SSH：

```
ssh -T git@github.com
```

成功时一般会看到类似：

```
Hi username! You've successfully authenticated
```

GitHub 官方也提供了用 `ssh -T git@github.com` 测试 SSH 连接的方法。





# idea中使用git

```
步骤：
	①去setting里面的version control进行配置，然后对git的路径进行选择cmd/git.exe
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





```
