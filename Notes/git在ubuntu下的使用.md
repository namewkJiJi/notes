# git在ubuntu平台下的使用

参考连接：【Git(linux)】<https://www.bilibili.com/video/BV19J411j7SZ>

## git的安装

1. `sudo apt-get install git`
2. 首先需要创建一个文件夹作为git的“工作区” `mkdir git_test`
3. 对工作区进行初始化 `git init`

## git常见命令

- `git add [file lists]`文件列表可以是文件，也可以是文件夹，用空格分开
- `git commit -m '[version description]'` -m后面跟的是版本的描述,提交**暂存区**的修改
- 设置个人信息：

- ``` bash
    git git config --global user.email "you@example.com" 
    git config --global user.name "Your Name"
    ```

- `git log (--graph) (--pretty=oneline)` 查看版本序列，从当前的HEAD往前看（查看分支图形化）（简短显示）
- `git reset --hard [HEAD^]` 回退到前一个版本，^的个数代表回退的步数，~n与之相同
- `git reset --hard [id]` 将HEAD重置到任意版本，向前或向后都行
- `git reflog` 查看历史修改行为，能够查HEAD之后的版本序列号
- `git status` 查看**工作区**的状态，看看是否全部commit
- `git checkout -- [filename]` 丢弃**工作区**的改动
- `git reset HEAD [filename]` 取消暂存，配合上一条指令能够丢弃**暂存区**的改动
- `git diff HEAD -- [filename]` 对比修改（**工作区**文件和当前**版本文件**）
- `git diff <HEAD HEAD^>-- [filename]` 不同**版本**之间某文件的区别
- `rm [filename]` 删除**工作区**的某文件。若git未跟踪某文件，则该文件的删除无法通过git（checkout）进行找回
- `git rm [filename]` 将删除改动放到**暂存区**内

## git 分支管理

分支之间是互不干扰的
HEAD指针指向分支指针，分支指针指向当前分支的当前版本

- `git branch` 查看分支
- `git checkout -b [branch name]` 切换到一个新的分支，新分支具有老分支所有旧的记录
- `git chechout [branch name]` 切换到一个已存在的分支
- `git merge [branch name]` 将某个分支合并到主分支（master），快速合并=挪指针
- `git branch -d [branch name]` 删除分支
- 当两个分支对**同一个文件**都进行修改时产生**合并冲突**：手动修改来解决冲突，进行一次新的提交
- 快速合并不能成功且合并时没有冲突，合并之后会自动进行一次新的提交
- `git merge [branch name] --no-ff -m [version description]`禁止快速合并
- `git stash` 保存工作现场
- `git stash pop` 恢复工作现场

## git & github

- `git clone [....ssh]` 下载项目
- ?`git push (origin [local branch name])` 将本地分支提交到github,当本地分支跟踪远程分支时，不需要指定本地分支
- `git branch --set-upstream-to=origin/[remote branch name] [local branch name]` 本地分支跟踪远程分支，可用git status 查看跟踪状态
- `git pull origin [remote branch name]` 从远程分支拉取代码
- `git remote add origin git@github.com:namewkJiJi/cpp_net.git` 连接github
