# git常用命令

查看状态：`git status`命令可以让我们时刻掌握仓库当前的状态

```shell
git status
```

添加修改 -A 添加所有

```shell
git add -A
```

提交修改 -m 都得加上“消息”

```shell
git commit -m "首次提交"
```

```shell
git branch -vv
```

```shell
git push
```

`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，我们在第一行添加了一个`distributed`单词。

```shell
git diff 
```

版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用`git log`命令查看：按q退出

```shell
git log
```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

```shell
git log --pretty=oneline
```

```shell
% git log --pretty=oneline
435218c4c2b6412b44c3d48372bdd03bb7016501 (HEAD -> main, origin/main, origin/HEAD) 第三次提交
5efa3e0dfb2096999fd03724c518578c87afc62b 第二次提交
99ab3ad63155c9b4b70ee43769485ec11f2b2aaa 首次提交
5244d4220cd7bdacecfe8d70b1959c3a78680f0c Initial commit
```

在Git中，用`HEAD`表示当前版本，也就是最新的提交`435218c4c2b6412b44c3d48372bdd03bb7016501`上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```shell
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

也可以根据`append GPL`的`commit id`是`435218c`，于是就可以指定回到未来的某个版本：

```shell
% git reset --hard 435218c4c2b
HEAD 现在位于 435218c 第三次提交
```

**工作区和缓存**

Git和其他版本控制系统如SVN的一个不同之处就是有暂存区的概念。

**工作区（Working Directory）**

就是你在电脑里能看到的目录，比如我的`github`工作空间有两个文件夹`Java8、untiled`就是一个工作区：

![image-20201215140543851](/Users/test/Library/Application Support/typora-user-images/image-20201215140543851.png)

**版本库（Repository）**

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![git-repo](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)

我们把文件往Git版本库里添加的时候，是分两步执行的：

1. 第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
2. 第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

**撤销修改**

`git checkout -- file`可以丢弃工作区的修改：

```shell
$ git checkout -- readme.md
```

命令`git checkout -- readme.md`意思就是，把`readme.md`文件在工作区的修改全部撤销，这里有两种情况：

* 一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
* 一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。

**删除文件**

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：

```shell
$ git rm test.txt
rm 'test.txt'
$ git commit -m "remove test.txt"
```

## 远程仓库

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

**第1步：**创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```shell
$ ssh-keygen -t rsa -C "youremail@example.com"
```

一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

**第2步：**登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容：

![image-20201215144248910](/Users/test/Library/Application Support/typora-user-images/image-20201215144248910.png)

为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

## 分支管理

每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即`master`分支。`HEAD`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`HEAD`指向的就是当前分支。

我们创建`dev`分支，然后切换到`dev`分支：

```shell
$ git checkout -b dev
切换到一个新分支 'dev'
```

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

```shell
$ git branch dev
$ git checkout dev
切换到一个新分支 'dev'
```

然后，用`git branch`命令查看当前分支：

```shell
$ git branch
* dev
  main
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

把`dev`分支的工作成果合并到`master`分支上：

```shell
$ git merge dev
```

合并完成后，就可以放心地删除`dev`分支了：

```shell
$ git branch -d dev
```

**解决冲突**

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图。

**分支管理策略**

