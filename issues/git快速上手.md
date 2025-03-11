

可以通过以下命令查看所有的配置以及它们所在的文件:
git config --list --show-origin


重要，信息会写入每次修改，不可更改：
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com


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





问题：

https://docs.github.com/zh/authentication/troubleshooting-ssh/error-permission-denied-publickey


解决ssh -T测试成功，但是git push失败的问题

PS E:\prj\data-analysis> ssh -vT git@github.com
OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2
debug1: Connecting to github.com [20.205.243.166] port 22.
debug1: Connection established.
debug1: identity file C:\\Users\\white/.ssh/id_rsa type -1


ssh -vT git@github.com
sh -vT git@github.com
OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2
debug1: Connecting to github.com [20.205.243.166] port 22.
debug1: Connection established.
debug1: identity file C:\\Users\\white/.ssh/id_rsa type 0
debug1: identity file C:\\Users\\white/.ssh/id_rsa-cert type -1
debug1: identity file C:\\Users\\white/.ssh/id_ecdsa type -1
debug1: identity file C:\\Users\\white/.ssh/id_ecdsa-cert type -1
debug1: identity file C:\\Users\\white/.ssh/id_ecdsa_sk type -1
debug1: identity file C:\\Users\\white/.ssh/id_ecdsa_sk-cert type -1
debug1: identity file C:\\Users\\white/.ssh/id_ed25519 type -1
debug1: identity file C:\\Users\\white/.ssh/id_ed25519-cert type -1
debug1: identity file C:\\Users\\white/.ssh/id_ed25519_sk type -1
debug1: identity file C:\\Users\\white/.ssh/id_ed25519_sk-cert type -1
debug1: identity file C:\\Users\\white/.ssh/id_xmss type -1
debug1: identity file C:\\Users\\white/.ssh/id_xmss-cert type -1
debug1: identity file C:\\Users\\white/.ssh/id_dsa type -1
debug1: identity file C:\\Users\\white/.ssh/id_dsa-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_for_Windows_9.5






























































