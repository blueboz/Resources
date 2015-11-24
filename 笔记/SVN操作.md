# SVN学习总结 #
Svn（SubVersion）是一个版本控制软件。能够解决如下问题
- 代码管理混乱
- 占用空间
- 代码冲突
- 难以追查问题代码的修改人
- 难以恢复到旧版本
- 难以进行权限控制

## 创建仓库 ##
    svnadmin create [directory]
	svnadmin create E:\svn\Project
## 仓库启动 ##
	svnserve -d -r [directory]
	svnserve -d -r E:\svn\Project
-r 表示指定目录作为根目录，即svn://localhost/，如果指定目录下有仓库，那么可以通过名称直接获取到仓库。

示例：
	
	#首先我们在E:根目录下创建一个svntest目录
	>mkdir svntest
	#进入svntest目录 
	>cd svntest
	#在目录下，我们创建
	>mkdir repository
	>cd repository
	>mkdir svn
	>cd svn
	#当前路径是E:/svntest/repository/svn
	#到这里，我们只是创建了一个路径而已，没有其它意思，只是路径名称符合规则罢了。没有其它特殊
	#在当前的svn目录下，我们姑且指定svn目录为所有仓库的父目录
	#	在svn目录下，创建3个目录，分别作为三个项目的仓库。
	#	名称可以随便写，这里指定为projectA,projectB,projectC
	#	E:/svntest/repository/svn
	#		|--projectA
	#		|--projectB
	#		|--projectC
	>mkdir projectA
	>mkdir projectB
	>mkdir projectC
	#创建3个仓库
	>svn create projectA
	>svn create projectB
	>svn create projectC
	#返回上一级目录
	>cd ..
	#	E:/svntest/repository/（当前在这里）
	#		|--svn
	#			|--projectA
	#			|--projectB
	#			|--projectC
	#上面的位置我们可以知道，现在repository目录下
	#指定svn目录为仓库的根目录
	>svnserve -d -r ./svn
	#到这里，我们的仓库启动完毕。当前控制台是被锁定了。
	#为了使用svn仓库，我们只有新建一个控制台
	#进入E盘
	>E:
	>mkdir mysvn
	>cd mysvn
	>svn checkout svn://localhost/projectA myProjectA
	#将项目projectA从svn仓库中检出到myProjectA目录下
	#进入检出的目录下面。我们便可以在里面进行一系列操作了
	>cd myProjectA
	
## 注意 ##
**仓库只有经过创建还有启动之后，才能够被远端访问**
