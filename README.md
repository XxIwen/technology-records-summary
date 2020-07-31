# Git Summary
#### 一、常用命令
- 添加一个远程仓库 （先有本地库，后有远程库的时候，如何关联远程库）
  ```
  git remote add origin git@server-name:path/repo-name.git // 关联一个远程库
  git push -u origin master
  ```

- 克隆一个仓库
  ```
  git clone git@github.com:XxIwen/gitskills.git
  ```


- 创建分支
  ```
  git checkout -b issue-101
  git merge --no-ff -m "merged bug fix 101" issue-101
  git branch -d issue-101
  ```

- 刪除分支
  ```
  git branch -d dev
  git branch -D dev // 强制删除

  ```

- 在本地创建和远程分支对应的分支
  ```
  git checkout -b dev origin/dev // 创建远程origin的dev分支到本地，用这个命令创建本地dev分支（本地和远程分支的名称最好一致）
  ```

- 建立本地分支和远程分支的关联
  ```
  git branch --set-upstream-to=origin/dev dev // 指定本地dev分支与远程origin/dev分支的链接
  ```

- 保存当前没有add的工作记录，并切换分支进行其他工作
  ```
  git stash	
  git stash list
  git stash pop
  ```

- 合并单个提交
  ```
  git cherry-pick <commit> // 在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，把bug提交的修改“复制”到当前分支，避免重复劳动
  eg: git cherry-pick 4c805e2
  ```

- 撤销修改
  ```
  git checkout -- <file> <file>
  ```

- 推送到远程仓库的某个分支
  ```
  git push origin master
  git push -u origin master // 第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
  ```

- 拉取代码
  ```
  git pull origin master
  ```

- 合并分支
  ```
  git merge --no-ff -m "merge with no-ff" dev // 合并dev分支，请注意--no-ff参数，表示禁用Fast forward，因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去
  ```
- 查看logs
  ```
  git log --graph --pretty=oneline --abbrev-commit
  git reflog
  ```

- 使Git的提交历史变成一条干净的直线
  ```
  git rebase // rebase操作可以把本地未push的分叉提交历史整理成直线
  ```
- Fetch all branches from all remotes
  ```
  git fetch --all
  ```

#### FAQ
- **1. 在GIT中创建一个空分支**
  ```
  git checkout --orphan doc // First step, 该命令会创建一个名为doc的分支，并且该分支下有前一个分支下的所有文件,新的分支不会指向任何以前的提交，就是它没有历史，如果你提交当前内容，那么这次提交就是这个分支的首次提交.
  git rm -rf  // Second step, 删除所有内容 .
  git commit -am "new branch for documentation" // Last step
  ```
- **2. 如何永久性地移除远程分支的commit**
  ```
  git reset --hard // first step
  git push --force // last step
  ```
- **3. 何如创建所有与远程库分支对应、关联的本地分支**
  ```
  git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
  git fetch --all
  git pull --all
  ```
- **4. 重命名本地和远程Branch，并关联**
  ```
  git branch -m oldName newName // 1. 本地分支重命名(未推送到远程)
  git branch -m oldName newName // 2. a. 远程分支重命名 (已经推送远程-假设本地分支和远程对应分支名称相同)
  git push --delete origin oldName // 2. b. 删除远程分支
  git push origin newName // 2. c. 上传新命名的本地分支
  git branch --set-upstream-to origin/newName 2. d.把修改后的本地分支与远程分支关联
  ```

###### 参阅：
- [https://www.liaoxuefeng.com/wiki/896043488029600](https://www.liaoxuefeng.com/wiki/896043488029600)
- [https://www.atlassian.com/git/tutorials/learn-git-with-bitbucket-cloud](https://www.atlassian.com/git/tutorials/learn-git-with-bitbucket-cloud)
- [How to fetch all Git branches](https://stackoverflow.com/questions/10312521/how-to-fetch-all-git-branches)