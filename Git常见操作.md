### 本地创建仓库，关联远程仓库，上传文件

1.初始化本地仓库
```git
git init
```
2.关联远程仓库
```git
git remote add origin git@github.com:wildape/backend_practice.git  
```
3.可以上传文件
```git
git push -u origin master
```
### 从远程仓库clone文件到本地
1.找一个文件目录，直接clone  
```git
git clone git@github.com:wildape/backend_practice.git 
```
2.创建分支并切换分支  
```git
git checkout -b dev
git switch -c dev
# 相当于
git branch dev
git checkout dev
```
3.之后可以在dev分支操作add/commit
4.如果dev开发完成，可以将dev合并到master
```git
# 先切换到matser分支
git checkout master
git switch master

# 禁用Fast forward 通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
git merge dev
# 一般使用以下禁用fast forward方式来merge
git merge --no-ff -m "merge with no-ff" dev
```
5.合并完成，可以继续在dev开发，也可以删除分支
```git
git branch -d dev
```

### 分支

1.可以本地创建一个分支，这个分支默认从master处创建分支
```git
git switch -c defen-dev
```
2.如果远程仓库没有该仓库，将该分支提交时
```git
git push -u origin defen-dev
```
3.如果远程存在同名分支，则直接使用
```git
git push origin defen-dev
```
4.拉取其他分支/本地创建分支跟踪远程分支
```git
# 以下两种都可以
git checkout -b dev origin/denfen-dev
git switch -c dev origin/master
```
### 多人协作开发
通常工作模式：
* 每个人都在自己分支上开发，开发完成通常多次`add/commit`；之后提交到远程分支`git push origin <branch-name>`;
* 正常是推送成功；如果推送失败，则因为远程分支比你的本地更新，先用`git pull`试图合并；
* 如果合并存在冲突，则需要解决冲突，并在本地提交；
* 没有冲突或者解决掉冲突后，再`git push origin <branch-name>`。