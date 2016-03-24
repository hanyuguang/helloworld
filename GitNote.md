#教程
以下内容基本来自以下教程。

[教程1-官方教程中文版，内容较多](https://git-scm.com/book/zh/v2/)

[教程2-图文还带小视频](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

----

##乱码问题
[http://howiefh.github.io/2014/10/11/git-encoding/](http://howiefh.github.io/2014/10/11/git-encoding/)  
	网页显示乱码则设置浏览器编码为UTF-8即可，Chrome的更多工具里

----

##免费私有库的git托管平台
[Coding平台](https://coding.net/git)

----

##经验
###别名
	git config --global alias.st status 
之后打`git st`即代表`git status`,常用的还有以下三个

	git config --global alias.co checkout
	git config --global alias.ci commit
	git config --global alias.br branch


###Http和SSH
- 使用 HTTP 的话Clone时不需要GitHub 帐号，而SSH的话需要帐号而且要管理者上传 SSH 密钥才能访问项目。  
- 而如果用https的话每次push提交都要输账号密码，SSH则不需要多次输入。  
- SSH使用：[生成SSH-Key](https://help.github.com/articles/generating-an-ssh-key/)后，发给Github项目负责人，让他增加你的SSHkey就可以提交了。

###少用pull，多用fetch和merge
pull是下载并自动合并，而fetch是下载，但不合并，此时可以先`diff`一下再决定是否合并

----

##Git命令
git diff 查看当前文件和当前暂存库的区别，比如git diff HEAD -- readme.txt   
git diff --staged 查看上次提交和当前暂存库的区别  
git commit 加上 `-a` 选项

	Git 就会自动把所有已经add过的文件暂存起来一并提交，从而跳过 git add 步骤。
###push后默认只看到master分支，需要dev等其他分支则
	git checkout -b dev origin/dev

[标签的配置](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013762144381812a168659b3dd4610b4229d81de5056cc000)

###更新为最新数据
	git fetch origin
`git log -p -2 `查看日志，用`-p`选项展开显示每次提交的内容差异，用`-2`则仅显示最近的两次更新：`--pretty `选项用于让每个提交独占一行，在提交频繁时有用。
###撤销提交

	git checkout -- filename.rb 将filename.rb返回到暂存区的内容
	git reset HEAD filename.rb 撤销filename.rb的暂存(撤销add)，该文件变成已修改未暂存
	
	场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
	
	场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区(即add)时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
	
	场景3：已经提交了不合适的修改(即commit)到版本库时，想要撤销本次提交，则需要版本回退了，不过前提是没有推送到远程库。
###Stash暂存-压栈式存储
当在fun分支改动一部分后需要去解决一个bug，此时因工作区和暂存区都有未处理的文件，
所以无法切换分支，但你又不想为这个小更改提交一次，于是可以`git stash -u`存储工作区和暂存区内容（去掉-u参数则只存储暂存区）。然后去修bug，修好**回到fun分支**用`git stash list`查看有无stash暂存，有就可以`git stash pop`回到上次的暂存并删除stash内容（如果只恢复不删除则`git stash apply`，要删的话就`git stash drop`）。
###删除文件
1. 要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。可以用` git rm `命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。
2. 如果只是简单地从工作目录中手工删除文件，运行` git status `时就会在 “Changes not staged for commit” 部分（也就是未暂存清单）看到。  
3. 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项` -f`（译注：即 force 的首字母），以防误删除文件后丢失修改的内容。  
4. 另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。换句话说，仅是从跟踪清单中删除。比如一些大型日志文件或者一堆 .a 编译文件，不小心纳入仓库后，要移除跟踪但不删除文件，以便稍后在` .gitignore `文件中补上，用` --cached `选项即可：  
	`$ git rm --cached readme.txt`  
	后面可以列出文件或者目录的名字，也可以使用 glob 模式。比方说：  
	`$ git rm log/\*.log`  
	注意到星号 * 之前的反斜杠 \，因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开（译注：实际上不加反斜杠也可以运行，只不过按照 shell 扩展的话，仅仅删除指定目录下的文件而不会递归匹配。上面的例子本来就指定了目录，所以效果等同，但下面的例子就会用递归方式匹配，所以必须加反斜杠。）。此命令删除所有`log/ `目录下扩展名为` .log `的文件。类似的比如：  
		`$ git rm \*~`  
	会递归删除当前目录及其子目录中所有 ~ 结尾的文件。
###重命名

	git mv file_from file_to

###rebase衍合命令
	跟merge一样是合并的，不同的是它是把补丁都找到再打上，而merge是直接新建一个版本
	https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88

----

##分支

创建分支

	git branch testing
切换分支

	git checkout testing
创建并切换分支

	git checkout -b testing
删除分支

	git branch -d testing

###分支的例子
已有master分支，然后正在newFun分支开发新功能，突然要修复master的bug，于是：
	
	创建并切换到修复bug的分支 git checkout -b hotfix
	改完后切换回master，合并hotfix分支到master里
	git checkout master
	git merge hotfix
	之后可以删除hotfix分支了，它没用了
	git branch -d hotfix
	此时，newFun分支并没有修复bug的内容，如果要合并这个bug内容就在newFun分支里合并master
	git merge master
	完成新功能后合并到master即可
	git checkout master
	git merge newFun
	git branch -d newFun

----

##版本回退-当commit后要回到旧版本
先`git log`查看一下，再`git reset --hard HEAD^`上一个版本，`^^`为上上个版本，也可以直接数字表示`HEAD~10`。退回旧版本后因为`log`里不存在新版的日志，所以要返回到新版的话可以
    
法1：窗口未关闭的话直接找上次查看`log`的新版本id；  
法2：`git reflog`查看每条命令的记录，再`git reset --hard id`回去。


----
##团队合作
`Collaborators`：合作者，对仓库有**读写**权限。

[小团队合作可以看看这里](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013760174128707b935b0be6fc4fc6ace66c4f15618f8d000)
###给开源项目做贡献
[此链接也是上面的git教程里的一部分](https://git-scm.com/book/zh/v2/GitHub-%E8%B4%A6%E6%88%B7%E7%9A%84%E5%88%9B%E5%BB%BA%E5%92%8C%E9%85%8D%E7%BD%AE)

	1. fork别人的
	2. clone到本地，创建新分支，在新分支上改代码
	3. 改好后commit和push到自己的该分支仓库（就是官方代码的副本，因为是fork来的）
	4. 在github上创建pull请求，维护者看到请求可以针对某行代码评论，然后在github上评论区讨论，维护者查看修改，同意的话就Merge请求。如果需要检查对方代码可以把对方代码pull下来，再merge到自己本地master并push(有请求时Github有具体代码提示，如https://git-scm.com/book/en/v2/book/06-github/images/maint-02-merge.png)

----
[配置别名](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375234012342f90be1fc4d81446c967bbdc19e7c03d3000)

[自己搭git服务器](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000)

