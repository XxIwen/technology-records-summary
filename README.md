# Git Summary
#### 一、常用命令
- **SSH Key**
  - ###### [创建SSH Key并添加到 ssh-agent](https://docs.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
    - 生成新 SSH 密钥并添加到 ssh-agent
    ```
    // 打开 Git Bash （使用Linux命令）检查现有 SSH 密钥后，您可以生成新 SSH 密钥以用于身份验证，然后将其添加到 ssh-agent。
    ---
    ll ~/.ssh // 在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件；这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人
    cat ~/.ssh/id_rsa.pub // 读取本机ssh key

    ssh-keygen -t rsa -b 4096 -C "your_email@example.com" // 将创建以所提供的电子邮件地址为标签的新 SSH 密钥
    ssh-keygen -t rsa -C "your_email@example.com" // from Liao https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416
    ssh-keygen -t rsa // for Gerrit setting
    ---
    $ ssh-keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (/c/Users/XXXX/.ssh/id_rsa):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /c/Users/XXXX/.ssh/id_rsa.
    Your public key has been saved in /c/Users/XXXX/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:az9jnpXSmKXgJPeOefUFcC4s/4T6N9Sdhv4ftx4DhVI XXXX@DESKTOP-XDFAAAF
    The key's randomart image is:
    +---[RSA 3072]----+
    |             E   |
    |            o o  |
    |           o = . |
    |          . + +  |
    |      . S  o.+.oo|
    |       = + *+o+o+|
    |        + *o==.=.|
    |       . =*+ .= *|
    |        o+=+..o=o|
    +----[SHA256]-----+
    ---
    ```
  - ###### [用SSH Agent 管理 SSH Key](https://docs.github.com/cn/github/authenticating-to-github/working-with-ssh-key-passphrases)
    ```
    eval "$(ssh-agent -s)" // 检查你的ssh-agent是否是活跃的
    ssh-add ~/.ssh/id_rsa // 将私匙添加到id_rsa
    ssh -T git@github.com // 测试是否能够连接到github.com
    ---
    $ ssh -T git@github.com
    The authenticity of host 'github.com (52.74.223.119)' can't be established.
    RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no/[fingerprint])? y
    Please type 'yes', 'no' or the fingerprint: no
    Host key verification failed.
    ---
    ```
- [git clone (克隆一个仓库)]()
  ```
  git clone git@github.com:XxIwen/gitskills.git // 从远程库 git@server-name:path/repo-name.git 克隆一个本地仓库
  ```
- [git remote (远程仓库)](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)
  - git remote add (添加一个远程仓库)
    ```
    git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
    ---
    git remote add origin git@server-name:path/repo-name.git // 添加一个名为origin的远程仓库（默认为origin，可以修改为其他名字：origin1, origin2或者origin3）
    ---
    ```
  - git remote（查看远程仓库信息）
    ```
    git remote // 列出你指定的每一个远程服务器的简写. 如果你已经克隆了自己的仓库，那么至少应该能看到 origin ——这是 Git 给你克隆的仓库服务器的默认名字
    git remote -v // 显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL; 例如，与几个协作者合作的，拥有多个远程仓库的仓库看起来像下面这样：
    ---
    $ git remote -v
    bakkdoor  https://github.com/bakkdoor/grit (fetch)
    bakkdoor  https://github.com/bakkdoor/grit (push)
    cho45     https://github.com/cho45/grit (fetch)
    cho45     https://github.com/cho45/grit (push)
    defunkt   https://github.com/defunkt/grit (fetch)
    defunkt   https://github.com/defunkt/grit (push)
    koke      git://github.com/koke/grit.git (fetch)
    koke      git://github.com/koke/grit.git (push)
    origin    git@github.com:mojombo/grit.git (fetch)
    origin    git@github.com:mojombo/grit.git (push)
    ---
    ```
  - git remote show
    ```
    git remote show <remote>
    ---
    $ git remote show origin
    * remote origin
      Fetch URL: https://github.com/schacon/ticgit
      Push  URL: https://github.com/schacon/ticgit
      HEAD branch: master
      Remote branches:
        master                               tracked
        dev-branch                           tracked
      Local branch configured for 'git pull':
        master merges with remote master
      Local ref configured for 'git push':
        master pushes to master (up to date)
    ---
    ```
    - 这个命令列出了当你在特定的分支上执行 git push 会自动地推送到哪一个远程分支。 它也同样地列出了哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了， 还有当你执行 git pull 时哪些本地分支可以与它跟踪的远程分支自动合并。
  - git remote rename
    ```
    git remote rename <old> <new> // Rename the remote named <old> to <new>.
    ```
  - git remote remove
    ```
    git remote remove <name> // Remove the remote named <name>. 
    ```
- [git fetch](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)
  - 访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看
  - git fetch [remote name]
    ```
    git fetch origin // 会抓取克隆（或上一次抓取）后新推送的所有工作
    git fetch --all // 默认从远程仓库origin抓取克隆（或上一次抓取）后新推送的所有工作
    ```
    - 必须注意 git fetch 命令*只会将数据下载到你的本地仓库*——**它并不会自动合并或修改你当前的工作。 当准备好时,你必须手动将其合并入你的工作。**
- [git pull]()
  - 拉取远程仓库某个分支的更新，再与本地的指定分支合并。（相当于git fetch 和 git merge）
    - 英文释义
      - Incorporates changes from a remote repository into the current branch. In its default mode, git pull is shorthand for git fetch followed by git merge FETCH_HEAD.
      - More precisely, git pull runs git fetch with the given parameters and calls git merge to merge the retrieved branch heads into the current branch. With --rebase, it runs git rebase instead of git merge.
  - git pull [remote name] [remote branch]:[local branch] (完整写法)
    ```
    git pull origin next:master // 拉取origin远程库的next分支，与本地库的master分支合并
    git pull origin next // 如果远程分支是与当前分支合并，则冒号后面的部分可以省略。（表示：拉取origin/next分支，再与当前分支合并。等同于先做git fetch，再做git merge。）.
    git pull origin // 如果当前分支与对应的origin远程分支存在追踪(remote-tracking)关系，git pull就可以省略远程分支名。（表示：本地的当前分支自动与对应的origin远程仓库的“追踪分支”(remote-tracking branch)进行合并）
    git pull // 完全省略表示：本地的当前分支自动与已建立追踪(remote-tracking)关系的对应的远程仓库的“追踪分支”进行合并。
    ```
- [git push](https://git-scm.com/docs/git-push)
  - git push [--all | --mirror | --tags] [--follow-tags] [--atomic] [-n | --dry-run] [--receive-pack=<git-receive-pack>]
  - git push的一般形式为 git push [remote name] [local branch] [remote branch]
    ```
    // 一个本地库的本地分支可以关联多个远程仓库的远程分支
    git push // 如果当前分支只有一个远程分支，那么主机名都可以省略；表示：把当前分支推送到已建立关联的远程分支
    git push origin // 如果当前分支与远程分支存在追踪关系，则本地分支和远程分支都可以省略，将当前分支推送到origin主机的对应分支
    git push origin master // 如果远程分支被省略, 表示：把本地的分支(比如：master)的最新修改推送至与之存在追踪关系的远程仓库origin的master分支（通常两者同名，如果该远程分支不存在，则会被新建）
    git push origin ：refs/for/master // 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，等同于 git push origin --delete master
    git push -u origin master // 第一次推送master分支时，可以加上参数-u。表示：Git不但会把本地的分支(比如：master)内容推送到远程仓库origin上的master分支，还会把本地的master分支和远程仓库origin的master分支关联起来
    ```
  - 常用的git push
    ```
    git push
    git push -u origin master // 关联本地的master分支和远程仓库origin的master分支
    git push origin local_branch_name // 新建远程分支
    git push origin --delete remote_branch_name // 删除远程分支
    ```
  - git push 新建远程分支
    ```
    git push origin local_branch_name // 在远程仓库origin创建新的分支（相当于将local_branch上传到远程仓库，前提是该远程分支不存在）
    ```
  - git push 删除远程分支
    ```
    git push origin ：refs/for/master // 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支
    git push origin --delete remote_branch_name // 删除远程分支
    ```
  - [扩展用法](https://www.cnblogs.com/qianqiannian/p/6008140.html)
    - 1.git push -u origin master
      ```
      git push -u origin master // 如果当前分支与多个主机存在追踪关系，则可以使用 -u 参数指定一个默认主机，这样后面就可以不加任何参数使用git push
      // 不带任何参数的git push，默认只推送当前分支，这叫做simple方式，还有一种matching方式，会推送所有有对应的远程分支的本地分支， Git 2.0之前默认使用matching，现在改为simple方式.
      // 如果想更改设置，可以使用git config命令。git config --global push.default matching OR git config --global push.default simple；可以使用git config -l 查看配置
      ```
    - 2.git push --all origin
      ```
      git push --all origin // 当遇到这种情况就是不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机，这时需要 -all 选项
      ```
    - 3.git push --force origin
      ```
      git push --force origin // git push的时候需要本地先git pull更新到跟服务器版本一致，如果本地版本库比远程服务器上的低，那么一般会提示你git pull更新，如果一定要提交，那么可以使用这个命令。
      ```
    - 4.git push origin --tags
      ```
      git push origin --tags // git push 的时候不会推送分支，如果一定要推送标签的话那么可以使用这个命令
      ```
- [git branch]()
  ```
  git branch -a // 查看本地仓库和远程仓库的信息
  git branch -r // 查看所有远程库的分支名
  ```
- [git merge](https://www.liaoxuefeng.com/wiki/896043488029600/900005860592480)
  ```
  git merge // 不会显示 feature，只保留单条分支记录
  git merge –no-ff // 可以保存你之前的分支历史。能够更好的查看 merge历史，以及branch 状态。eg： git merge --no-ff -m "merged bug fix 101" dev
  git merge dev
  git merge origin/dev
  ```
- [git commit]()
- [git checkout]()
- [git stash]()
- [git rebase]()
---
#### 小结
- [Fast forward模式](https://www.liaoxuefeng.com/wiki/896043488029600/900005860592480)
  - git merge --no-ff 合并分支 (禁用Fast forward)
    ```
    // 通常，合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
    // 合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并
    git checkout -b dev
    git merge --no-ff -m "merged bug fix 101" dev // 合并分支时， --no-ff参数，表示禁用Fast forward
    git branch -d dev
    ```
- 刪除本地分支
  ```
  git branch -d dev
  git branch -D dev // 强制删除

  ```
- 删除远程分支
  ```
  git push origin ：refs/for/master // 如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支
  git push origin --delete remote_branch_name // 删除远程分支
  ```
- 新建本地分支
  ```
  git checkout -b dev // 基于本地分支
  git branch dev  // 基于本地分支
  git checkout -b dev origin/dev // 创建远程origin的dev分支到本地（在本地创建和远程分支对应的分支），用这个命令创建本地dev分支（本地和远程分支的名称最好一致）
  ```
- 新建远程分支
  ```
  git push origin local_branch_name // 在远程仓库origin创建新的分支（相当于将local_branch上传到远程仓库，前提是该远程分支不存在）
  ```
- 关联本地分支和远程分支(本地分支已存在)
  ```
  git branch --set-upstream-to=origin/dev dev // 指定本地dev分支与远程origin/dev分支的链接
  git branch --set-upstream-to origin/dev // 本地分支与远程分支dev的关联
  git push -u origin master // 第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
  ```
- 关联本地分支和远程分支(本地分支不存在, 需要新建)
  ```
  git checkout -b dev origin/dev // 创建远程origin的dev分支到本地（在本地创建和远程分支对应的分支），用这个命令创建本地dev分支（本地和远程分支的名称最好一致）
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
- **5. 先有本地库，后有远程库的时候，如何关联远程库**
  ```
  git remote add origin git@server-name:path/repo-name.git // 1. 添加一个名为origin的远程仓库（默认为origin，可以修改为其他名字：origin1, origin2或者origin3）
  // 以下三种，任选一个
  git branch --set-upstream-to=origin/dev dev // 2. a. 指定本地dev分支与远程origin/dev分支的链接
  git branch --set-upstream-to origin/dev // 2. b. 本地分支与远程分支dev的关联
  git push -u origin master // 2. c. 第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
  ```

###### 参阅：
- [Git教程-廖雪峰](https://www.liaoxuefeng.com/wiki/896043488029600)
- [Learn Git with Bitbucket Cloud](https://www.atlassian.com/git/tutorials/learn-git-with-bitbucket-cloud)
- [How to fetch all Git branches](https://stackoverflow.com/questions/10312521/how-to-fetch-all-git-branches)
- [Git Documentation - Chinese](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)
- [Git Documentation - English](https://git-scm.com/docs/git-remote)
- [GitHub Documentation - Chinese](https://docs.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
---
# Gerrit Summary
##### [代码检视工具Gerrit的日常使用](https://www.jianshu.com/p/b77fd16894b6)
- Gerrit实际上是一个Git服务器，它为在其服务器上托管的Git仓库提供一系列权限控制，但是其主要功能就是用来做Code Review。
- 关于 refs/for
  - refs/for 的意义在于我们提交代码到服务器之后是需要经过code review 之后才能进行merge的，而refs/heads 不需要