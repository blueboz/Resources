# Github常用指令 #

## 配置命令 ##
### 查看配置信息 ###
	
    git config --list	显示所有配置
    git config -l

### 全局配置 ###

	git config --global user.name "blueboz" 配置用户名
	git config --global user.email "11173626291@qq.com"
	git config --system user.name "blueboz"

## git文件的三种状态 ##


- git中，文件有三种状态，已修改，已暂停，已提交，这三个文件分别放在工作目录，暂存区域和git目录。
- 当一个文件修改完毕之后，它仍然在工作目录。
- 只有当它被add之后，才会进入暂存区域，文件状态变为已暂存。
- 最后，当文件被 commit之后，它就会进去git目录区域。

这三个文件状态非常重要，请慢慢理解。

    git status 查看当前状态 

## add命令暂存已修改文件 ##

    git add .

## commit命令提交已暂存文件 ##

	git commit -m 'message'

## 分支 ##
如果事先没有作出任何的更改，一个项目是没有分支的，它只有一条主线，例如

    git branch	查看分支状态

命令行输出

	* master。

这里master就是主干，就是整个开发的流程，它前面的星号表示当前开发的流程。
如果在开发过程中，我们突然需要添加某个功能，或者打上某个补丁，可以在 master主干上添加一个分支，比如host，例如:


	git checkout -b host		//切换到host分支上面
	git checkout host 		//其中的-b 是可以省略的
	
这个时候，你可以清楚的看到星号已经在host头上.

	* host
 	  master

表示当前已经切换到host上，你可以开发你的补丁，也就是在本地仓库进行一系列的操作，而不会打乱主干的开发。
如果你在开发过程中想要回到主干，可以使用checkout命令进行切换

    git checkout master
返回到主干
## 合并 ##
当某个分支的开发结束后，你会需要将其合并到主干上，从而集中精力进行主干的开发。合并前只需要将指针切换到主干，即master，然后使用merge命令。
切换到host

    git checkout host 
在host中写入文件

	echo "hello branch!!!" >> file.txt
暂存修改文件的状态

	git add file
提交到仓库

	git commit file -m "add somthing"
切换到master

	~/test$ git checkout master 

合并，这个指令是重点！只有通过合并，打补丁的程序才能合并入主干

	git merge host 
此时的host分支已经失去作用了，可以将删除。

	~/test$ git branch -d host 
	Deleted branch host (was 0dacfd9).

## 远程库操作 ##
 
 
### 查看当前远程库 ###
 
	git remote
 
 
### 添加远程库 ###
 	
	git remote add <remote-name> <remote-url>
    git remote add jack git://github.com/blueboz/jack
 
 
### 从远程库抓取数据 ###
 
    git fetch [remote-name]
	git fetch origin
	git fetch jack
 
 
### 推送数据到远程仓库 ###
 
	git push [remote-name] [branch-name]
	git push -u origin master
	git push -u origin host			//前提是分支存在
	git push -u jack master
	我们也可以省略掉-u
	git push origin master			//直接将当前目录所在仓库推向远端
	
 
 
### 查看远程仓库 ###
 
	git remote [remote-name]
 
 
### 远程仓库的删除和重命名 ###
 
	git remote rm [remote-name] 
	git remote rename old-name new-name

## 查看改动 ##
	git diff README.txt

## 版本回退 ##
	git log 查看历史版本
	git reset --hard HEAD^		回退上一个版本
	git reset --hard HEAD^^		回退上上个版本
	git reset --hard HEAD^^^		回退上上上版本
	
	git reset  –hard 版本号		回退到指定版本
	那我们怎么知道要回退到哪一个版本的版本号是多少？
	git reflog					可以查看 

## 理解工作区与暂存区的区别 ##
>**工作区**：就是你在电脑上看到的目录，比如目录下testgit里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。

>**版本库(Repository)**：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

## 版本克隆 ##

	git clone http://github.com/blueboz/GDOU
## git init指令的使用 ##
当本地有一个项目，版本库中没有，我们可以首先在github上创建一个目录
![](http://static.open-open.com/lib/uploadImg/20141027/20141027155920_832.jpg)
github会提示我们有2种方法同步，一是用命令行创建，而是使用clone，在这里我们不使用
在本地项目所在目录下执行如下语句

	git init
	git touch Hello.java
	git add Hello.java
	git commit -m "Hello World Files"
	git remote add origin https://github.com/bleuboz/Hello.git
	git push -u origin master
至此，已经完成同步了

	work on the current change (see also: git help everyday)
	   addAdd file contents to the index
	   mv Move or rename a file, a directory, or a symlink
	   reset  Reset current HEAD to the specified state
	   rm Remove files from the working tree and from the index
	
	examine the history and state (see also: git help revisions)
	   bisect Use binary search to find the commit that introduced a bug
	   grep   Print lines matching a pattern
	   logShow commit logs
	   show   Show various types of objects
	   status 当前工作目录状态
	
	grow, mark and tweak your common history
	   branch List, create, or delete branches
	   checkout   分支切换
	   commit 提交
	   diff   查看修改内容
	   merge  合并
	   rebase Forward-port local commits to the updated upstream head
	   tagCreate, list, delete or verify a tag object signed with GPG
	
	collaborate (see also: git help workflows)
	   fetch  Download objects and refs from another repository
	   pull   同步
	   branch 查看分支
	   push   更新到版本库

