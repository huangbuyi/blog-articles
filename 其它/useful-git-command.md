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