## 项目操作
添加监控文件
git add *.html

初始化git仓库
git init 

提交变化
git commit -m "initial project version"
commit需要有message，没有则会调用文本编辑器
git commit

clone项目
git clone https://github.com/libgit2/libgit2 
git clone https://github.com/libgit2/libgit2 mylibgit

显示为追踪的文件
git status

查看未提交的修改
git diff
git diff --staged
git diff --cached

删除缓存文件
git rm --cached +path
删除缓存文件以及物理文件
git rm --f +path

提交数据到远程仓库
git push
从远程仓库拉取数据
git pull

一直按住esc ，再连续按大写的z两次就退出来了。

## 配置

查看配置
git config --list

git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。
git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。
git stash list: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。
git stash clear: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。