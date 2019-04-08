# 提交

- `git commit` 操作会打开默认的编辑器让你来输入提交信息 使用 `-m`选项可以直接在bash中输入提交信息
- `git rm <file>`会让git停止跟踪此文件并且将它删除 加上`--cached`选项会停止跟踪此文件但是不会在磁盘上删除它

# 撤销操作

- 如果在一次提交中漏掉了什么东西更改完成之后可以使用 `git commit--amend` 来覆盖上一次的提交.
- 如果想撤销对某一个文件的更改的话使用 `git checkout -- <file>` 来将这个文件回复到上一次提交时的状态
- `git statsh` 可以将当前工作区的所有文件更改都丢弃掉恢复到上次提交时的状态
- `git reset HEAD <file>` 可以将文件移出暂存区(stage) (我觉得没什么卵用的操作

# 远程仓库

- `git fetch [remote]` 将拉取远程仓库中所有有更改的分支到本地 
- `git fetch`将会抓取上一次抓取后的所有新推送上去的内容将他们作为分支存在本地 默认会抓取所有的分支更新 如果想抓取某一个分支的话可以使用`git fetch [remote-name] [branch-name]`来抓取特点的分支
- `git branch -r`可以查看到所有远程分支 `-a` 查看所有远程本地分支
- `git pull [remote-name] [remote-branch]:[local-brnach] ` pull操作将会拉取远程某个分支的更新然后将其合并到`local-branch`上, 如果需要合并的是当前分支就不需要指定`local-branch`
- 如果远程分支和本地分支有跟踪关系 `git pull`就不需要指定分支命. (git会默认将远程master和本地master关联 在本地master中执行`git pull origin`  将会默认拉取远程的master更新并合并)
- `git branch --set-upstream [local-branch]  [remote-branch]` 这条命令可以将两个分支建立跟踪关系

# 打标签

- `git tag` 列出所有标签
- git中有两种标签类型`轻量标签(lightweight)`和`附注标签(annotated)`
- 轻量标签只是一次提交的索引 附注标签我暂时用不上
- `git tag [tag-name]` 为当前分支最后一次提交打上标签

# 分支

- `git branch [branch-name]` 创建新分支

- `git branch -d [branch-name]` 删除分支

- `git checkout [branch-name]` 切换分支

- 开发新功能时不要在`master`分支上直接开发 应该打开一个新的分支 开发完毕后合并到`master`上

- 新功能开发完毕后删除开发分支

- 如果两个分支的关系是上下游 那么合并这两个分支只是简单的将指针指向下游的分支而已

  ### 分支管理

   -  `git branch --merged`将显示所有本地分支中那些已经合并过的分支 (可以安全的删除)
   -  `git branch -d [branch-name]`删除分支 如果这个分支没有被合并过会出警告 `-D` 选项强制删除

  ### 远程分支

  - `git checkout --track [remote]/[branch]`在本地创建一个跟踪远程分支的新分支

  - `git checkout -b [branch] [remote]/[branch]`同上

  - `git branch -u [remote]/[branch]` 更改当前分支的远程跟踪分支

  - 跟踪分支执行`git pull`操作会默认拉取指定的远程分支

  - `git branch -vv` 查看所有分支的远程跟踪分支

  - `git push origin --delete [branch]` 删除远程分支

    - 你在本地将dev的分支合并到了master并且推送到了远端 远端现在任然还有一个dev分支 删掉这个dev就用这个

  - 变基

    - `git rebase`  `git merge` 

    - `git rebase`将一个分支的修改提交移到另一个分支上

      ```bash
      $ git checkout experiment
      $ git rebase master
      First, rewinding head to replay your work on top of it...
      Applying: added staged command
      ```

      把分支`experiment中的提交移动(变基)到master中

  - 