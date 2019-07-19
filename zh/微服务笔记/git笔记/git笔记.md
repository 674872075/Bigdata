##git常用操作##

	git pull 从github仓库拉取最新代码(合并本地仓库)

	git pull --rebase origin master  从github仓库拉取最新代码(会覆盖本地仓库)

	git add [.|文件名] 把工作区的所有变化，(就是你的所有改动)，都添加到 版本库/暂存区。
	
	git commit -m "说明信息"  更进一步提交，并说明提交log。

	git status .：	查看当前路径下的的状态。git下最最常用的一个命令。
	
	git push -u origin master: 	 把版本库的所有更新内容， 都推送到远程服务器。(就是推代码/推上去)
