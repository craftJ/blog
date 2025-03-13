

可以通过以下命令查看所有的配置以及它们所在的文件:
git config --list --show-origin


重要，信息会写入每次修改，不可更改：
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com


配置别名
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
git config --global alias.last 'log -1 HEAD    看最后一次提交
git config --global alias.unstage 'reset HEAD --'  取消暂存文件
git config --global alias.visual '!gitk'  定义命令，传给shell执行gitk命令


列出所有 Git 当时能找到的配置
git config --list 


多个配置位置，用来判断哪个最终生效，
git config --show-origin [配置项]


获取仓库，两个方式，本地初始化，远程克隆
1. git init
2. git clone https://github.com/libgit2/libgit2 [new dir]
	https协议: https://
	git协议:  git://
	ssh协议： user@server:path/to/repo.git
	

查看文件状态
git status
git status -s 简短输出


跟踪一个文件
git add

配置无需跟踪的文件，忽略
.gitignore


查看尚未暂存的改动
git diff

查看已暂存的改动
git diff --staged

提交更新
git commit -m [提交信息]

提交并显示差异
git commit -v

跳过暂存区，直接提交
git commit -a

彻底移除文件，暂存区及目录
git rm [filename]

对已修改或add，强制彻底移除文件，暂存区及目录
git rm -f [filename]

暂存区移除，不跟踪后续
git rm --cached [filename]

移动文件
git mv file_from file_to

查看历史记录
git log -p 显示每次差异
git log -2 近两次
git log --stat  附带一些总结
git log --graph 显示分支及合并历史
git log --decorate 查看各个分支当前所指的对象
git log featureA..origin/featureA  <分支A>..<分支B>显示在 <分支B> 中但不在 <分支A> 中的提交。
git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" \
   --before="2008-11-01" --no-merges -- t/  指定目录t及时间范围，抛开merge类型的提交记录
git log --pretty=format:"%h - %an, %ar : %s"  自定义格式
	%H/%h  提交的完整/简短hash

	%T/%t  文件树的完整/简短hash

	%P/%p  父提交的完整/简短hash

	%an/%ae 作者名字/邮件地址

	%ad/%ar 作者修订日期（可以用 --date=选项 来定制格式）/ 作者修订日期，按多久以前的方式显示

	%cn/%ce 提交者的名字/电子邮件地址

	%cd/%cr 提交日期/提交日期（距今多长时间）

	%s 提交说明


追加提交
git commit --amend


取消暂存
git reset HEAD [filename] ..


危险命令，撤销修改，用最新的版本覆盖
git checkout -- [filename]

查看远程仓库
git remote -v


添加远程仓库
git remote add <shortname> <url> 

列出所有远程跟踪分支
git branch -r


拉取远程更新，会拉取所有该仓库的所有分支更新
git fetch <remote>

清理本地仓库中已经不存在于远程仓库的远程分支引用
git fetch --prune  

推送修改到远程分支
git push <remote> <branch>
git push -u <远程仓库> <本地分支>:<远程分支>
git push -u origin featureA (相当于git push -u origin featureA:featureA)用于将本地分支推送到远程仓库，并设置上游分支为origin/featureA


查看远程分支信息
git remote show <remote> 


重命名远程仓库
git remote rename [oldname] [newname]


删除远程仓库
git remote remove paul

查看标签
git tag
git tag -l [正则匹配]


创建标签
git tag -a v1.4 -m "my version 1.4"  附注标签
git tag v11.4  轻量标签
git tag -a v1.2 9fceb02 对历史版本打标签


向远程仓库推送标签
git push origin <tagname>
git push origin --tags   推送所有标签

删除标签
git tag -d [tagname]


检出指定标签版本，最好创建一个分支，否则会导致进入detached head状态
git checkout -b version2 v2.0.0
git checkout -b sc/ruby_client master  基于master分支创建分支并切到新分支


创建一个新分支
git checkout -b feature/new-feature
git branch testing
git branch [branch name]       仅创建分支，不切换。
git checkout -b [branch name]  创建并切换到新分支，适合日常开发。
git switch -c [branch name]    创建并切换到新分支，功能更清晰，推荐在 Git 2.23+ 中使用。


删除一个分支
git branch -d [branch name]
git push origin --delete serverfix  删除远程分支


列出分支
git branch -a
git branch -vv 列出最后一次提交
git branch --merged/--no-merged 已经合并/没有合并到当前分支的分支


rebase
如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基
$ git checkout experiment
$ git rebase maste
$ git rebase --onto master server client  server到client之间的差异去rebase，server本身的改动暂时不合并


合并
git fetch origin  先拉取最新
git log main..feature  确保自己明确合并的差异
git checkout <想要更改的分支>
git merge <想要合并的原分支>

解决冲突
git status  
打开冲突文件，找到冲突标记（<<<<<<<、======= 和 >>>>>>>），并手动选择保留或修改冲突的内容
git add <冲突文件>
git commit

强制创建一个合并提交
git merge --no-ff feature

仅允许 fast-forward 合并。如果需要创建合并提交，Git 会报错
git merge --ff-only feature

在合并过程中遇到冲突时，可以使用 --abort 选项放弃合并，恢复到合并之前的状态
git merge --abort


归档打包
git archive master --prefix='project/' --format=zip > `git describe master`.zip


生成简短log
git shortlog --no-merges master --not v1.0.1 是一个用于生成提交日志摘要的命令，其作用是列出在 master 分支中，但不在标签 v1.0.1 所表示的提交历史中的所有提交记录，并且排除合并提交


Git 时常会自动对仓库进行重新打包以节省空间
git gc

git数据恢复
1. git reflog  记录每次HEAD指针变更
2. git fsck 实用工具，将会检查数据库的完整性。 如果使用一个 --full 选项运行它，它会向你显示出所有没有被其他对象指向的对象：


git查看空间占用
git count-objects -v


git调试
常规跟踪
GIT_TRACE=true git lga

网络包跟踪
GIT_TRACE_PACKET=true git ls-remote origin

git命令耗时分析
GIT_TRACE_PERFORMANCE=true git gc



========================
问题：

https://docs.github.com/zh/authentication/troubleshooting-ssh/error-permission-denied-publickey


解决ssh -T测试成功，但是git push失败的问题

PS E:\prj\data-analysis> ssh -vT git@github.com
OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2
debug1: Connecting to github.com [20.205.243.166] port 22.
debug1: Connection established.
debug1: identity file C:\\Users\\testuser/.ssh/id_rsa type -1


ssh -vT git@github.com
sh -vT git@github.com
OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2
debug1: Connecting to github.com [20.205.243.166] port 22.
debug1: Connection established.
debug1: identity file C:\\Users\\testuser/.ssh/id_rsa type 0
debug1: identity file C:\\Users\\testuser/.ssh/id_rsa-cert type -1
debug1: identity file C:\\Users\\testuser/.ssh/id_ecdsa type -1



