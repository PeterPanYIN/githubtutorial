# 本文是如下视频教程的笔记
[Learn Git in 15 Minutes Video](https://www.youtube.com/watch?v=nhNq2kIvi9s)

其后续教程为：
[Learn Github in 20 Minutes](https://www.youtube.com/watch?v=USjZcfj8yxE)
# 创建 Git 用户
该命令创建的用户不会用于登录某个账号，仅仅为了区分谁做出了哪些修改。

创建全局账号
```bash
git config --global user.name "Your name"
git config --global user.email "your@email.com"
```
如果不加 `--global` 表示创建局部账号，仅在当前仓库有效。

```bash
git config user.name #查看当前仓库用户名
git config user.email # 查看当前仓库用户的邮箱
git config --list # 查看所有的用户
# 或者 git config -l 
```
管理用户
```bash
git config --global --edit # 通过修改配置文件修改全局用户
# 上面的代码修改的是 ~/.gitconfig 文件，因此也可以直接修改这个文件 
vi ~/.gitconfig 

git config --edit # 或者 git config -e 修改的是当前仓库的用户

# 将当前的仓库的用户重置为 global 用户
git config --unset-all user.name
git config --unset-all user.email 
```



# 初始化代码仓库
```bash
git init
```
如果想把某个文件夹设为代码仓库，就进入该文件夹并输入上述命令。

```bash
git status
```
查看代码仓库状态，会输出当前未被提交的修改。

# 提交修改
选择性将被修改的文件加入暂存区（staging area）
```bash
git add index.html app.js
```
将所有的改变都加入
```
git add .
```
提交
```bash
git commit -m "message of the commit" # -m 设置本次提交的简介
```
如果不加 `-m`，将会弹出一个文本编辑器，强制你介绍本次提交的内容，这里可以写更多的内容，用换行符分开第一行（相当于`-m` 后跟着的内容）与大块的详情介绍，不输入将无法完成提交。
# git add 的几个 flag
* `git add -A` 将所有改动都加入暂存区
* `git add .` 只将新的文件和改动加入暂存区，删除当前目录或子目录的某个文件将不会被暂存
* `git add -u` 只暂存修改和和文件删除，不暂存新文件


`git add .` 扫描当前文件树，如果里面的文件内容出现了变动，或者有之前它没见过的文件，就把它加入到暂存区，它不暂存任何 `rm` 操作。

`git add -u` 扫描记录在案的文件，观察它们是否有改动或者被删掉了，因此它不会添加新文件，仅仅暂存已经跟踪到的文件的改动。

`git add -A` 等价于 `git add .; git add -u`

# 显示提交历史
```
git log
```
显示每次提交的历史，包括 commit hash 和 message

# 返回某次提交
```
git checkout <commit hash>
```
`<commit hash>` 为每次提交的唯一编码，不必输入完整
但一旦返回某次提交后，当前 status 会用红色字体标记为：`HEAD detached at xxx`，后面的 branch 将用于解决这一问题。

# 分支
## 增加分支
```
git branch <new-branch-name>
```
## 查看分支
```bash
git branch # 查看当前代码的各个分支
git branch -v # 查看每个分支最后一次提交的内容
```
## 切换分支
```
git checkout <branch-name> 
```
上文中的 `HEAD detached at xxx` 会显示为一个分支，当切换回 master 分支后，该分支将被删除。
在新的分支中修改代码，如果不提交就切回 master 分支，输入 status 将显示在新 branch 中修改的内容，但是如果在新分支中提交修改，再切换回 master 分支，将什么都不显示。

## 删除分支
```
git branch -d <branch-name>
```

## 融合分支
```
git merge <branch-name>
```

## HEAD detached at xxx
如果在 HEAD detached at xxx 中做了修改并且提交了，此时如果切换回 master, 会收到警告：
```
Warning: you are leaving 1 commit behind, not connected to
any of your branches:

  7bf2e87 new line from head detached

If you want to keep it by creating a new branch, this may be a good time
to do so with:

 git branch <new-branch-name> 7bf2e87

```
此时最好是开一个新的分支，按照提示，命令为：
```bash
git branch <new-branch-name> <commit hash> 
```
然后，再把该分支的内容 merge 回 master 分支
```
git merge <branch-name>
```
此时有可能会出现冲突：
```bash
Auto-merging index.html  # 修改了 index.html 的内容
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result. 
```
就是说，在 HEAD detached 状态下，除了修改的内容之外，其余内容和当前 master 分支不一致，自动化处理程序不知道要保存哪一个。
此时检查源文件，发现冲突的部分在其中被突出显示了出来：
```
<<<<<<< HEAD
    <h2>This is the second stage header</h2>
    <h3> Code added in new branch </h3>>
=======
    <h2> New line from head detached version</h2>
>>>>>>> temp
```
上面是 master 分支的部分，下面是 HEAD detecthed 分支的部分，可以根据实际情况选择保留其中一个分支的内容，或者两个分支的内容同时保留（删掉辅助线）。

或者干脆取消本次 merge
```
git merge --abort
```

































