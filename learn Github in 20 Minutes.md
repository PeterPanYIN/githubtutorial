# Learn Github in 20 Minutes
Note for the video [Learn Github in 20 Minutes](https://www.youtube.com/watch?v=nhNq2kIvi9s).

Github 是公共的 Git 仓库管理平台，可以方便团队协作。

## 使用 Github
通常的工作流程：将现有的仓库推送到 Github
1. 在本地 add/commit 代码
2. 去 Github 上创建一个仓库
3. 将本地的仓库与 Github 的仓库联系起来
4. 将本地仓库的代码推送到 Github

## 修改 branch 的名字
在github 页面创建了一个代码仓库后，会进入设置界面，展示了如何向 github 提交的命令行代码。其中包含了将 branch 改名的代码：
```bash
git branch -M main #将当前默认的 branch 名字（master）改为 main
# https://www.sunzhongwei.com/master-ending-of-era-of-git-branch-m-main
```
## Personal token
然后按照如下指示提交代码，

```bash
git remote add origin https://github.com/PeterPanYIN/githubtutorial.git
git push -u origin main
```
第一行的意思是将 github 仓库的网址命名为 origin， 也可以把它命名为别的，只要后文中的 push 和它保持一致即可。

第二行中 `-u` 表示将本地的 repo 和 github 上的建立联系通道，下次再传的时候就可以不用为 `push` 加任何参数了。这里的 `-u` 是 `--set-upstream` 的简写，两者完全等价。

发现出现新的错误：
```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead. 
```
于是又按照其错误信息提供的[链接](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)，生成了个人的 token

```
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
```
继续按照上述代码 push，但是输入密码时输入的是token。
生成新的 token 后，旧的自动失效。因此也可以不必记住复杂的token，随时 push 随时生成也可以。

此外，还可以用如下命令，将 token 保存到：`~\.git-credentials` 中：
```
git config --global credential.helper store
```
执行这个命令之后，下次 push 时还需要再输入一次 token，再往后就不用输入 token 了。

## Pull
和别人合作时，别人也可以向 github 的代码仓库中提交代码，或者我从另一个机器上向 github 中提交了代码。此时我想在本地同步 github 上的内容，就要用到 pull。
```
git pull origin main # origin 远程代码仓库，main 本地代码仓库
```

如果在本地修改了一部分内容，在 github 上修改了另一部分内容，git pull 会出错：
```
error: Your local changes to the following files would be overwritten by merge:
	learn Github in 20 Minutes.md
Please commit your changes or stash them before you merge.
```
如何解决这个问题呢？
错误提示中给出了解决方案：要么先把本地修改提交，要么先保存，然后才能将 github 上的修改与本地的merge。

我们首先试验 commit 方案，即commit 完成后，再 pull。实验发现此时文件中将会出现两个版本的差异，此时需要人工决定保存哪一个，或者两个都保存。


然后我们试验 git stash，我们分别在本地和网页版的github上添加不同内容，然后观察 stash 是如何处理版本冲突的。

```bash
# 输入 git stash，可以让本地的 status 不再显示已修改但未 commit 的内容，让git认为本地版本已经 commit 过了
git stash 
# 然后再用 git pull，就不会报错了
git pull
# 然后输入 git stash pop，将之前修改的内容显示回来，此时文件中会显示同步的内容和 stash 的内容的差异，和 merge collision 差不多，需要手工修改决定保留哪个，或者都保留
git stash pop
```
`git stash pop` 将会恢复堆栈中最新的 stash，也可以用
```
git stash apply
```
将堆栈中保存的 stash 添加到当前文件中

此外，推荐为 git stash 添加说明，即 
```bash
git stash save "test-cmd-stash" # 用 save 为 stash 添加说明
```
显示所有 stash，删除 stash
```bash
git stash list # 显示 stash
git stash drop stash@{0} # 删除 stash@{0}

```

使用 stash 的好处是，可以暂时保存当前修改，不必 commit 到branch 或者 master 中。具体[见这篇文章](https://www.cnblogs.com/tocy/p/git-stash-reference.html)

## 不同分支

```bash
git checkout -b testbranch # 创建一个名为 testbranch 的分支并切换进去
``` 
在该分支做修改，然后提交
```bash
git add .
git commit
git push -u origin testbranch # 这时 github 上会多一个名为 testbranch 的分支
```

远程删除 branch
```bash
git push -d origin testbranch # 删除 github上的分支
# 回顾
git branch -d testbranch # 删除本地分支
git branch -D testbranch # 强制删除没有 merge 的 branch
```
## pull request
1. fork 一个 repository
2. 修改该 repository 中的代码
3. 通过 github 中的按钮来 pull request ，等待该repository的管理员同意或者拒绝



## 删除 commit history
参考链接：[how to delete all commit history in github](https://stackoverflow.com/questions/13716658/how-to-delete-all-commit-history-in-github)

方案一：
1. 删除当前代码仓库的 .git 文件夹
2. `git init` 重置当前文件夹
3. `git remote add origin https://github.com/PeterPanYIN/githubtutorial.git`
4. `git add .`
5. `git commit -m "delete commit history"`
6. git push -f origin master

删掉 .git 文件可能会导致问题。

方案二：
1. `git checkout --orphan latest_branch` 创建了一个新的名为 `latest_branch` 的分支，该分支是“干净”的，和 `git init` 在一个新文件夹中干的事一样，只不过它是在一个已经是 repository 的文件夹这么干。
2. 暂存所有修改：`git add -A`
3. 提交修改：`git commit -m "delete all commit history" `
4. 删除 `main` branch: `git branch -D main`，由于此时 main branch 并没有被 merge 进当前的branch，因此需要用 `-D` 而非 `-d`。
5. 重命名当前 branch 到 main `git branch -m main` 
6. 最后，强制更新当前的代码仓库：`git push -f origin main`



---

bilibili 教学视频https://www.bilibili.com/video/BV1Xx411m7kn/?p=2&spm_id_from=pageDriver

