---
title: 【杂记】Git
category: Notes
date: 2024-01-01
---

### 常用命令查表

+ Linux 相关命令

|命令|用途|
|---|---|
|`vim a.txt`|在 vim 中打开 a.txt|
|`cat a.txt`|显示 a.txt 文件内容|
|`ls`|列出当前目录所有文件|
|`ll`|等价于 `ls -l`，列出当前目录所有文件并显示详细信息|


### Git 的四大区域

+ 工作区（workspace）

+ 暂存区（staging area）

+ 本地库（local repository）

+ 远程库（remote repository）


### 详细内容

#### 初始化相关

+ `git init` 初始化仓库。
+ `git clone [url]` 从远程下载一个仓库。

#### 暂存区相关

+ `git add` 添加文件到暂存区。

    + 添加一个或多个文件到暂存区：`git add [file1] [file2] ...`
    
    + 添加指定目录到暂存区，包括子目录：`git add [dir]`
    
    + 添加当前目录下的所有文件到暂存区：`git add .`

+ `git rm --cached [file]` 删除 **暂存区** 中的 file。
  
+ `git status` 查看在你上次提交之后是否有对文件进行再次修改。

  + 使用 `git status -s` 获得更简短的报告。

+ `git diff [file]` 获取 file 文件在 **工作区** 和 **暂存区** 的差异。

#### 本地库相关

+ `git commit -m [message]` 提交本地库。其中 [message] 是本次提交的备注信息。
  
  + 使用 `git commit [file1] [file2] ... -m [message]` 来提交暂存区中的指定几个文件。

+ `git reflog` 查看 commit 简略日志。

+ `git log` 查看 commit 详细日志。

#### 版本回退

+ `git reset --hard [versionID]` 穿越回到 [versionID] 对应的版本。**这会将暂存区与工作区都回退到上一次版本!**

#### Branch

+ `git branch` 显示当前所有的分支。
  
  + 使用 `git branch -v` 显示更为详细的信息。

+ `git branch [branchname]` 创建一个名为 branchname 的分支。
 
+ `git checkout [branchname]` 切换到名为 branchname 的分支。
  
  + 在切换分支之前，**请保证目前工作区的修改已被 commit**。否则将无法进行分支切换。  

+ `git merge` 合并分支。

  + 在切换分支之前，**请保证目前工作区的修改已被 commit**。否则将无法进行分支切换。 
    
  + 假设当前在分支 A 下，`get merge B` 将会把 B 中的修改合并到 A 中。这一操作不会对分支 B 产生任何影响。

  + 如果合并产生冲突（conflict），则需要人为地决定新内容。**冲突合并完成后，需要重新 add 和 commit。**此时的 commit 后面不能携带 [file] 参数。

#### 提交到 Github

+ `git remote` 查看当前的别名（alias）库。
  
  +  使用 `git remote -v` 查看更详细的信息。

+ `git remote add [alias] [url]` 把 url 的别名设置为 alias。

+ `git push [alias] [branch]` 将本地库的 branch 分支推送到 alias 对应的远程库。

+ `git pull [alias] [branch]` 将别名为 alias 的远端的 branch 分支拉取下来。假设目前在分支 A，拉去下来的内容也会被保存在分支 A。

  + 如果本地仓库和远程仓库没有关联，可能产生 refusing to merge unrelated histories 错误。解决方案是：在 pull 后紧根 `--allow-unrelated-histories` 选项。