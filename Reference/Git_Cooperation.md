## Git Cooperation

> `fetch` 更新分支的引用副本，下载所有新的提交
>
> 远程引用更新跟踪分支常用pull实现
>
> > pull是`fetch` 和 `merge`或者`fetch` 和 `rebase` 的组合
> >
> > pull默认使用`merge`来更新本地分支，通过 `--rebase` 可通过rebase策略更新本地分支
> >
> > pull 在更新一个分支的本地副本时，参数中远程链接和远程分支名称是可以忽略的
> >
> > 变基：
> >
> > > 变基两个分支时只改变其中一个分支的历史，而另一个分支看上去没有什么变化 

### 常用

```bash
git ls-tree -r master --name-only
git branch 查看本地分支
git branch -D tmp 删除本地分支
git branch -r 查看远程分支
git push origin --delete tmp 删除远程分支
git config --global http.version HTTP/1.1

git clone 会默认创建master分支，和与其同名的远程分支保持一致

git config --system --list
git config --global --list
git config --local --list
git config --list

git log --all 显示所有log，即使回滚过soft

git reset --soft|--mixed|--hard <commit_id>
--mixed    会保留源码,只是将git commit和index 信息回退到了某个版本.
--soft   保留源码,只回退到commit信息到某个版本.不涉及index的回退,如果还需要提交,直接commit即可.
--hard    源码也会回退到某个版本,commit和index 都会回退到某个版本.(注意,这种方式是改变本地代码仓库源码)(可以解决 Unstaged changes after reset 问题)

git revert <commit_id>
注意：git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit，看似达到的效果是一样的,其实完全不同。


git ls-files --stage
```







### 创建本地仓库

- 创建本地仓库

  - 创建方式
    - clone
    - init
  - 创建工单来跟踪工作
  - 用合适的名字创建分支并开始工作
  - 工作完成
    - 工作经过了完全的测试
    - 代码符合相关标准
    - 运行相关测试集
    - 有注释或文档
    - 经过了拼写检查
  - 将工作目录的文件添加到暂存区
    - `git add --all`
  - 将暂存区的文件提交到本地仓库，便于共享
    - `git commit`
  - 将本地仓库推到集中式代码托管系统
    - `git push`
    - 暂存服务器，等待相关人员批准
  - 合并到master分支
    - `git merge`
  - 部署到product分支
  - 关闭工单

- Clone 已有项目

  - `git clone https://github.com/SinowX/MDGuide.git`
  - `git clone -b branchname https://github.com/SinowX/MDGuide.git `

- 将已有项目迁移到Git

  - `git init`
  - `git status`
    - 工作目录的更改可以被暂存在索引的一组提交中，每组都有一个不同的提交信息
  - `git add --all` 将仓库中所有文件添加至暂存区
  - `git commit -m "Initial import of all project files."`

- 初始化空项目

  - ```bash
    [workspace]$
    mkdir mywork
    cd mywork
    git init
    ```

- 查看历史记录

  - `git log`
    - `git log --oneline` 查看缩短的log



### 使用分支

#### 列出分支

- `git branch` 仅有本地分支
  - 同 `git branch --list`
- `git branch --all` 列出所有分支
- `git branch --remotes` 列出远程分支
  - `git branch -r`
  - 

#### 切换分支

- `git checkout --track origin/dev`

#### 创建分支

- `git checkout master`
- `git checkout -b newbranch master `

#### 删除分支

- `git branch -D tmp`
- 

### 在仓库中添加更改

- 添加到暂存区
  - `git add README.md process.png`
  - 支持通配符
    - `git add <dir_name>/*`
    - `git add *.svg`
  - 暂存git中所有已知的且在上次提交之后编辑或修改过的文件
    - `git add --update`
  - 暂存git所有已知但没有暂存的文件，以及任何当前未被git跟踪的文件
    - `git add --all`

#### 添加部分文件修改到暂存区

- `git add --patch filename`
- 比对某些行是否添加

#### 从暂存区移除文件

- `git reset HEAD file.md`
- 若要取消文件的某些修改，使用reset 与 --patch参数

#### 编写扩展提交消息

- `git commit -m "adding technical edits."`
- `git commit --amend`

### 使用标签

- `git log --oneline`
- `git log fa03c30 --max-depth=1`
  - `--max-depth` 表示想要显示的记录条数（当前似乎已改为 -数字）
- `git show fa03c30`
  - 显示单个提交的日志消息和文本diff
- 一旦找到了想要收藏的提交，就可用tag进行标记
- `git tag tagname fa04c30`
- `git tag`
  - 列出所有标签
- `git show tagname`
  - 根据标签查看提交信息

### 使用远程仓库

- `git remote --verbose`
- `git remote show`
- `git push`
- `git push --set-upstream my_gitlab 1-process`
  - 上传本地分支时设置上游分支



### 分支维护

- ```bash
  git checkout master
  git merge trunk
  //将trunk分支并入到master分支
  ```

- ```bash
  git branch --delete trunck
  //删除本地分支
  ```

- ```bash
  git push --delete orgin trunck
  //删除远程分支
  ```




### 撤销方法

| 目的                                                         | 备注                                                     | 命令                        |
| ------------------------------------------------------------ | -------------------------------------------------------- | --------------------------- |
| 舍弃对目录中某个文件的修改                                   | 修改文件未被暂存或提交                                   | checkout --filename         |
| 舍弃对目录中所有未保存变更                                   | 文件已暂存但未被提交                                     | reset --hard                |
| 合并与某个特定提交（不包含）之间的多个提交                   |                                                          | reset commit                |
| 移除所有未保存变更，包含未跟踪的文件                         | 修改的文件未被提交                                       | clean -fd                   |
| 移除所有已暂存变更和在某个提交之前提交的工作，但不移除心文件 |                                                          | reset --hard commit         |
| 移除之前的工作，但完整保留提交历史记录（前进式回滚）         | 分支已被发布，工作目录是干净的                           | revert commit               |
| 从分支历史记录中移除一个单独的提交                           | 修改的文件已经被提交，工作目录是干净的，分支尚未进行发布 | rebase --interactive commit |
| 保留之前的工作，但与另一提交合并                             | 选择squash选项                                           | rebase --interactive commit |



![alt Git Bitree](https://www.dropbox.com/s/q95o1q7zwlthh90/git_bitree.png?dl=0)



### 工单

- 问题
  - 大致描述你想要解决的问题
- 原因
  - 为什么想要做这件事，如果问题得到解决，谁将受益？
- 质量保证测试
  - 如何知道这个问题已经得到解决

### 工单流程

1. 在你的issue跟踪系统中创建一个新的工单，注明这个issue的编号。
2. 在你的本地仓库中，使用issuenumber-description格式创建一个新的分支。
3. 完成工单描述的工作（且只完成工单中描述的工作）。
4. 测试你的工作，确保已经完成并且是正确的。确保它能够通过开发环境下的QA测试。
5. 现在，你有了一个“杂乱”的工作目录，其中包含了新增的文件和（或）修改过的文件。将你的更改添加到本地仓库的暂存区。
6. 将你缓存的修改提交至仓库。
7. 将你的更改推送到备用服务器上。在很多情况下，这也将是追踪你的工单的地方，如GitLab、Bitbucket或GitHub。根据你的工单系统，现在可以将这个工单标记为已解决，但不需要将其标记为已关闭。
8. 当你对你的工作完全满意时，将你的工单分支并入主分支（通常是master）并将修改后的分支推送到代码托管系统中。
9. 再一次测试你的工作，确保没有后续问题。
10. 将你的工单标记为已关闭。

