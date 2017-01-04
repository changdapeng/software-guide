# 五、远程仓库的使用

为了能在任意 Git 项目上协作，你需要知道如何管理自己的远程仓库。 远程仓库是指托管在因特网或其他网络中的你的项目的版本库。 你可以有好几个远程仓库，通常有些仓库对你只读，有些则可以读写。 与他人协作涉及管理远程仓库以及根据需要推送或拉取数据。 管理远程仓库包括了解如何添加远程仓库、移除无效的远程仓库、管理不同的远程分支并定义它们是否被跟踪等等。 


## 1. 查看远程仓库
***

如果想查看你已经配置的远程仓库服务器，可以运行 `git remote` 命令。
它会列出你指定的每一个远程服务器的简写。
如果你已经克隆了自己的仓库，那么至少应该能看到 "origin -" 这是 Git 给你克隆的仓库服务器的默认名字：


    $ git clone https://github.com/schacon/ticgit
    Cloning into 'ticgit'...
    remote: Reusing existing pack: 1857, done.
    remote: Total 1857 (delta 0), reused 0 (delta 0)
    Receiving objects: 100% (1857/1857), 374.35 KiB | 268.00 KiB/s, done.
    Resolving deltas: 100% (772/772), done.
    Checking connectivity... done.
    $ cd ticgit
    $ git remote
    origin


你也可以指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。


    $ git remote -v
    origin  https://github.com/schacon/ticgit (fetch)
    origin  https://github.com/schacon/ticgit (push)


如果你的远程仓库不止一个，该命令会将它们全部列出。 例如，与几个协作者合作的，拥有多个远程仓库的仓库看起来像下面这样：


    $ cd grit
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


这样我们可以轻松拉取其中任何一个用户的贡献。 此外，我们大概还会有某些远程仓库的推送权限。

注意这些远程仓库使用了不同的协议。

## 2. 添加远程仓库
***

我在之前的章节中已经提到并展示了如何添加远程仓库的示例，不过这里将告诉你如何明确地做到这一点。

运行 `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，同时指定一个你可以轻松引用的简写：


    $ git remote
    origin
    $ git remote add pb https://github.com/paulboone/ticgit
    $ git remote -v
    origin  https://github.com/schacon/ticgit (fetch)
    origin  https://github.com/schacon/ticgit (push)
    pb      https://github.com/paulboone/ticgit (fetch)
    pb      https://github.com/paulboone/ticgit (push)


现在你可以在命令行中使用字符串 pb 来代替整个 URL。 例如，如果你想拉取 Paul 的仓库中有但你没有的信息，可以运行 `git fetch pb`：


    $ git fetch pb
    remote: Counting objects: 43, done.
    remote: Compressing objects: 100% (36/36), done.
    remote: Total 43 (delta 10), reused 31 (delta 5)
    Unpacking objects: 100% (43/43), done.
    From https://github.com/paulboone/ticgit
     * [new branch]      master     -> pb/master
     * [new branch]      ticgit     -> pb/ticgit


现在 Paul 的 master 分支可以在本地通过pb/master 访问到 - 你可以将它合并到自己的某个分支中，或者如果你想要查看它的话，可以检出一个指向该点的本地分支。

## 3. 从远程仓库中抓取与拉取
***

就如刚才所见，从远程仓库中获得数据，可以执行：


    $ git fetch [remote-name]


这个命令会访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

如果你使用 `clone` 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。 所以，`git fetch origin` 会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意 `git fetch` 命令会将数据拉取到你的本地仓库 - 它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

如果你有一个分支设置为跟踪一个远程分支，可以使用 `git pull` 命令来自动的抓取然后合并远程分支到当前分支。这对你来说可能是一个更简单或更舒服的工作流程；默认情况下，`git clone` 命令会自动设置本地 master 分支跟踪克隆的远程仓库的 master 分支（或不管是什么名字的默认分支）。运行 `git pull` 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。


## 4. 推送到远程仓库
***

当你想分享你的项目时，必须将其推送到上游。
这个命令很简单：`git push [remote-name] [branch-name]` 。

当你想要将 master 分支推送到 `origin` 服务器时（再次说明，克隆时通常会自动帮你设置好那两个名字），那么运行这个命令就可以将你所做的备份到服务器：


    $ git push origin master


只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 你必须先将他们的工作拉取下来并将其合并进你的工作后才能推送。


## 5. 查看远程仓库
***

如果想要查看某一个远程仓库的更多信息，可以使用 `git remote show [remote-name]` 命令。如果想以一个特定的缩写名运行这个命令，例 `origin` ，会得到像下面类似的信息：


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


它同样会列出远程仓库的 URL 与跟踪分支的信息。 这些信息非常有用，它告诉你正处于 master 分支，并且如果运行 `git pull`，就会抓取所有的远程引用，然后将远程 master 分支合并到本地 master 分支。 它也会列出拉取到的所有远程引用。

这是一个经常遇到的简单例子。 如果你是 Git 的重度使用者，那么还可以通过 `git remote show` 看到更多的信息。


    $ git remote show origin
    * remote origin
        URL: https://github.com/my-org/complex-project
        Fetch URL: https://github.com/my-org/complex-project
        Push  URL: https://github.com/my-org/complex-project
        HEAD branch: master
        Remote branches:
            master                           tracked
            dev-branch                       tracked
            markdown-strip                   tracked
            issue-43                         new (next fetch will store in remotes/origin)
            issue-45                         new (next fetch will store in remotes/origin)
            refs/remotes/origin/issue-11     stale (use 'git remote prune' to remove)
        Local branches configured for 'git pull':
            dev-branch merges with remote dev-branch
            master     merges with remote master
        Local refs configured for 'git push':
            dev-branch                     pushes to dev-branch                     (up to date)
            markdown-strip                 pushes to markdown-strip                 (up to date)
            master                         pushes to master                         (up to date)


这个命令列出了当你在特定的分支上执行 `git push` 会自动地推送到哪一个远程分支。 它也同样地列出了哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了，还有当你执行 `git pull` 时哪些分支会自动合并。


## 6. 远程仓库的移除与重命名
***

如果想要重命名引用的名字可以运行 `git remote rename` 去修改一个远程仓库的简写名。
例如，想要将 `pb` 重命名为 `paul` ，可以用 `git remote rename` 这样做：


    $ git remote rename pb paul
    $ git remote
    origin
    paul


值得注意的是这同样也会修改你的远程分支名字。 那些过去引用 `pb/master` 的现在会引用 `paul/master` 。

如果因为一些原因想要移除一个远程仓库 - 你已经从服务器上搬走了或不再想使用某一个特定的镜像了，又或者某一个贡献者不再贡献了 - 可以使用  `git remote rm`  ：


    $ git remote rm paul
    $ git remote
    origin


# 六、 打标签

像其他版本控制系统（VCS）一样，Git 可以给历史中的某一个提交打上标签，以示重要。 比较有代表性的是人们会使用这个功能来标记发布结点（v1.0 等等）。

## 1. 列出标签
***

在 Git 中列出已有的标签是非常简单直观的。 只需要输入 `git tag`：


    $ git tag
    v0.1
    v1.3

这个命令以字母顺序列出标签；但是它们出现的顺序并不重要。

你也可以使用特定的模式查找标签。 例如，Git 自身的源代码仓库包含标签的数量超过 500 个。 如果只对 1.8.5 系列感兴趣，可以运行：


    $ git tag -l 'v1.8.5*'
    v1.8.5
    v1.8.5-rc0
    v1.8.5-rc1
    v1.8.5-rc2
    v1.8.5-rc3
    v1.8.5.1
    v1.8.5.2
    v1.8.5.3
    v1.8.5.4
    v1.8.5.5


## 2. 创建标签
***

Git 使用两种主要类型的标签：**轻量标签（lightweight）** 与 **附注标签（annotated）**。

一个轻量标签很像一个不会改变的分支 - 它只是一个特定提交的引用。

然而，附注标签是存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。 通常建议创建附注标签，这样你可以拥有以上所有信息；但是如果你只是想用一个临时的标签，或者因为某些原因不想要保存那些信息，轻量标签也是可用的。

#### 2.1  附注标签
***

在 Git 中创建一个附注标签是很简单的。 最简单的方式是当你在运行 `tag` 命令时指定 `-a` 选项：


    $ git tag -a v1.4 -m 'my version 1.4'
    $ git tag
    v0.1
    v1.3
    v1.4


`-m` 选项指定了一条将会存储在标签中的信息。 如果没有为附注标签指定一条信息，Git 会运行编辑器要求你输入信息。

通过使用 `git show` 命令可以看到标签信息与对应的提交信息：


    $ git show v1.4
    tag v1.4
    Tagger: Ben Straub <ben@straub.cc>
    Date:   Sat May 3 20:19:12 2014 -0700

    my version 1.4

    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number


输出显示了打标签者的信息、打标签的日期时间、附注信息，然后显示具体的提交信息。

#### 2.2 轻量标签
***

另一种给提交打标签的方式是使用轻量标签。 轻量标签本质上是将提交校验和存储到一个文件中 - 没有保存任何其他信息。 创建轻量标签，不需要使用 `-a`、`-s` 或 `-m` 选项，只需要提供标签名字：


    $ git tag v1.4-lw
    $ git tag
    v0.1
    v1.3
    v1.4
    v1.4-lw
    v1.5


这时，如果在标签上运行git show，你不会看到额外的标签信息。命令只会显示出提交信息：


    $ git show v1.4-lw
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number


## 3. 后期打标签
***

你也可以对过去的提交打标签。 假设提交历史是这样的：


    $ git log --pretty=oneline
    15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'experiment'
    a6b4c97498bd301d84096da251c98a07c7723e65 beginning write support
    0d52aaab4479697da7686c15f77a3d64d9165190 one more thing
    6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
    0b7434d86859cc7b8c3d5e1dddfed66ff742fcbc added a commit function
    4682c3261057305bdd616e23b64b0857d832627b added a todo file
    166ae0c4d3f420721acbb115cc33848dfcc2121a started write support
    9fceb02d0ae598e95dc970b74767f19372d61af8 updated rakefile
    964f16d36dfccde844893cac5b347e7b3d44abbc commit the todo
    8a5cbc430f1a9c3d00faaeffd07798508422908a updated readme


现在，假设在 v1.2 时你忘记给项目打标签，也就是在 “updated rakefile” 提交。 你可以在之后补上标签。 要在那个提交上打标签，你需要在命令的末尾指定提交的校验和（或部分校验和）:


    $ git tag -a v1.2 9fceb02


可以看到你已经在那次提交上打上标签了：


    $ git tag
    v0.1
    v1.2
    v1.3
    v1.4
    v1.4-lw
    v1.5

    $ git show v1.2
    tag v1.2
    Tagger: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Feb 9 15:32:16 2009 -0800

    version 1.2
    commit 9fceb02d0ae598e95dc970b74767f19372d61af8
    Author: Magnus Chacon <mchacon@gee-mail.com>
    Date:   Sun Apr 27 20:43:35 2008 -0700

        updated rakefile
    ...


## 6. 共享标签
***

默认情况下，`git push`命令并不会传送标签到远程仓库服务器上。在创建完标签后你必须显式地推送标签到共享服务器上。这个过程就像共享远程分支一样 - 你可以运行 `git push origin [tagname]` 。


    $ git push origin v1.5
    Counting objects: 14, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (12/12), done.
    Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
    Total 14 (delta 3), reused 0 (delta 0)
    To git@github.com:schacon/simplegit.git
     * [new tag]         v1.5 -> v1.5


如果想要一次性推送很多标签，也可以使用带有 `--tags` 选项的 `git push` 命令。 这将会把所有不在远程仓库服务器上的标签全部传送到那里。


    $ git push origin --tags
    Counting objects: 1, done.
    Writing objects: 100% (1/1), 160 bytes | 0 bytes/s, done.
    Total 1 (delta 0), reused 0 (delta 0)
    To git@github.com:schacon/simplegit.git
     * [new tag]         v1.4 -> v1.4
     * [new tag]         v1.4-lw -> v1.4-lw


现在，当其他人从仓库中克隆或拉取，他们也能得到你的那些标签。


## 7. 检出标签
***

在Git中你并不能真的检出一个标签，因为它们并不能像分支一样来回移动。 如果你想要工作目录与仓库中特定的标签版本完全一样，可以使用 `git checkout -b [branchname] [tagname]` 在特定的标签上创建一个新分支：


    $ git checkout -b version2 v2.0.0
    Switched to a new branch 'version2'


当然，如果在这之后又进行了一次提交，version2 分支会因为改动向前移动了，那么 version2 分支就会和 v2.0.0 标签稍微有些不同，这时就应该当心了。



# 七、 Git 别名

Git 并不会在你输入部分命令时自动推断出你想要的命令。
如果不想每次都输入完整的 Git 命令，可以通过 `git config` 文件来轻松地为每一个命令设置一个别名。
这里有一些例子你可以试试：


    $ git config --global alias.co checkout
    $ git config --global alias.br branch
    $ git config --global alias.ci commit
    $ git config --global alias.st status


这意味着，当要输入 `git commit` 时，只需要输入 git ci。 随着你继续不断地使用 Git，可能也会经常使用其他命令，所以创建别名时不要犹豫。

在创建你认为应该存在的命令时这个技术会很有用。 例如，为了解决取消暂存文件的易用性问题，可以向 Git 中添加你自己的取消暂存别名：


    $ git config --global alias.unstage 'reset HEAD --'


这会使下面的两个命令等价：


    $ git unstage fileA
    $ git reset HEAD -- fileA


这样看起来更清楚一些。 通常也会添加一个 `last` 命令，像这样：


    $ git config --global alias.last 'log -1 HEAD'


这样，可以轻松地看到最后一次提交：


    $ git last
    commit 66938dae3329c7aebe598c2246a8e6af90d04646
    Author: Josh Goebel <dreamer3@example.com>
    Date:   Tue Aug 26 19:48:51 2008 +0800

        test for current head

        Signed-off-by: Scott Chacon <schacon@example.com>


可以看出，Git 只是简单地将别名替换为对应的命令。 然而，你可能想要执行外部命令，而不是一个 Git 子命令。 如果是那样的话，可以在命令前面加入 `!` 符号。 如果你自己要写一些与 Git 仓库协作的工具的话，那会很有用。 我们现在演示将 git visual 定义为 gitk 的别名：


    $ git config --global alias.visual '!gitk'




> 本教程参照Git的官方文档编写，如有不明与错误之处，敬请指正。