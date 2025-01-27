本章节内容涵盖你在使用 Git 完成各种工作中将要使用的各种基本命令。 在学习完本章之后，你应该能够配置并初始化一个仓库（repository）、开始或停止跟踪（track）文件、暂存（stage）或提交（commit)更改。 本章节也将向你演示如何配置 Git 来忽略指定的文件和文件模式、如何迅速而简单地撤销错误操作、如何浏览你的项目的历史版本以及不同提交（commits）间的差异、如何向你的远程仓库推送（push）以及如何从你的远程仓库拉取（pull）文件。


# 一、获取 Git 仓库

有两种取得 Git 项目仓库的方法。 第一种是在现有项目或目录下导入所有文件到 Git 中； 第二种是从一个服务器克隆一个现有的 Git 仓库。

## 1. 在现有目录中初始化仓库
***

如果你打算使用 Git 来对现有的项目进行管理，你只需要进入该项目目录并输入：


    $ git init


该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。 但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。

如果你是在一个已经存在文件的文件夹（而不是空文件夹）中初始化 Git 仓库来进行版本控制的话，你应该开始跟踪这些文件并提交。 你可通过 `git add` 命令来实现对指定文件的跟踪，然后执行 `git commit` 提交：


    $ git add *.c
    $ git add LICENSE
    $ git commit -m 'initial project version'


现在，你已经得到了一个实际维护（或者说是跟踪）着若干个文件的 Git 仓库。


## 2. 克隆现有的仓库
***

如果你想获得一份已经存在了的 Git 仓库的拷贝，比如说，你想为某个开源项目贡献自己的一份力，这时就要用到 `git clone` 命令。 

Git 克隆的是该 Git 仓库服务器上的几乎所有数据，而不是仅仅复制完成你的工作所需要文件。 当你执行 `git clone` 命令的时候，默认配置下远程 Git 仓库中的每一个文件的每一个版本都将被拉取下来。

事实上，如果你的服务器的磁盘坏掉了，你通常可以使用任何一个克隆下来的用户端来重建服务器上的仓库（虽然可能会丢失某些服务器端的挂钩设置，但是所有版本的数据仍在）。

克隆仓库的命令格式是 `git clone [url]` 。比如，要克隆 Git 的可链接库 libgit2，可以用下面的命令：


    $ git clone https://github.com/libgit2/libgit2


这会在当前目录下创建一个名为 “libgit2” 的目录，并在这个目录下初始化一个 `.git` 文件夹，从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。 如果你进入到这个新建的 libgit2 文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。 

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以使用如下命令：


    $ git clone https://github.com/libgit2/libgit2 mylibgit


这将执行与上一个命令相同的操作，不过在本地创建的仓库名字变为 mylibgit。

Git 支持多种数据传输协议。 上面的例子使用的是 https:// 协议，不过你也可以使用 git:// 协议或者使用 SSH 传输协议，比如user@server:path/to/repo.git 。


# 二、记录每次更新到仓库

现在我们手上有了一个真实项目的 Git 仓库，并从这个仓库中取出了所有文件的工作拷贝。 接下来，对这些文件做些修改，在完成了一个阶段的目标之后，提交本次更新到仓库。

请记住，你工作目录下的每一个文件都不外乎这两种状态：**已跟踪** 或 **未跟踪** 。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于 **未修改**，**已修改** 或 **已放入暂存区**。 

工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 我们逐步将这些修改过的文件放入暂存区，然后提交所有暂存了的修改，如此反复。所以使用 Git 时文件的生命周期如下：

![文件的状态变化周期](image/10.png)

## 1. 检查当前文件状态
***

要查看哪些文件处于什么状态，可以用 `git status` 命令。

如果在克隆仓库后立即使用此命令，会看到类似这样的输出：


    $ git status
    On branch master
    nothing to commit, working directory clean


这说明你现在的工作目录相当干净。换句话说，所有已跟踪文件在上次提交后都未被更改过。 此外，上面的信息还表明，当前目录下没有出现任何处于未跟踪状态的新文件，否则 Git 会在这里列出来。 最后，该命令还显示了当前所在分支，并告诉你这个分支同远程服务器上对应的分支没有偏离。 现在，分支名是 “master”,这是默认的分支名。 

现在，让我们在项目下创建一个新的 README 文件。 如果之前并不存在这个文件，使用 git status 命令，你将看到一个新的未跟踪文件：


    $ echo 'My Project' > README
    $ git status
    On branch master
    Untracked files:
        (use "git add <file>..." to include in what will be committed)

            README

    nothing added to commit but untracked files present (use "git add" to track)


在状态报告中可以看到新建的 README 文件出现在 Untracked files 下面。 未跟踪的文件意味着 Git 在之前的快照（提交）中没有这些文件；Git 不会自动将之纳入跟踪范围，除非你明明白白地告诉它“我需要跟踪该文件”， 这样的处理让你不必担心将生成的二进制文件或其它不想被跟踪的文件包含进来。 不过现在的例子中，我们确实想要跟踪管理 README 这个文件。

## 2. 跟踪新文件
***

使用命令 `git add` 开始跟踪一个文件。所以，要跟踪 README 文件，运行：


    $ git add README


此时再运行 git status 命令，会看到 README 文件已被跟踪，并处于暂存状态：


    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            new file:   README



只要在 Changes to be committed 这行下面的，就说明是已暂存状态。

如果此时提交，那么该文件此时此刻的版本将被留存在历史记录中。你可能会想起之前我们使用 `git init` 后就运行了 `git add (files)` 命令，开始跟踪当前目录下的文件。`git add` 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。


## 3. 暂存已修改文件
***

现在我们来修改一个已被跟踪的文件。 如果你修改了一个名为 CONTRIBUTING.md 的已被跟踪的文件，然后运行 `git status` 命令，会看到下面内容：


    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            new file:   README

    Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   CONTRIBUTING.md


文件 CONTRIBUTING.md 出现在 Changes not staged for commit 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。

 要暂存这次更新，需要运行 `git add` 命令。 这是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。将这个命令理解为 “添加内容到下一次提交中” 而不是“将一个文件添加到项目中”要更加合适。

现在让我们运行 `git add` 将"CONTRIBUTING.md"放到暂存区，然后再看看 `git status` 的输出：


    $ git add CONTRIBUTING.md
    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   CONTRIBUTING.md


现在两个文件都已暂存，下次提交时就会一并记录到仓库。 假设此时，你想要在 CONTRIBUTING.md 里再加条注释， 重新编辑存盘后，准备好提交。 不过且慢，再运行 `git status` 看看：


    $ vim CONTRIBUTING.md
    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   CONTRIBUTING.md

    Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   CONTRIBUTING.md  


怎么回事？ 现在 CONTRIBUTING.md 文件同时出现在暂存区和非暂存区。 这怎么可能呢？ 好吧，实际上 Git 只不过暂存了你运行 `git add` 命令时的版本， 如果你现在提交，CONTRIBUTING.md 的版本是你最后一次运行 `git add` 命令时的那个版本，而不是在你运行 `git commit` 时，在工作目录中的当前版本。 所以，运行了 `git add` 之后又作了修订的文件，需要重新运行 `git add` 把最新版本重新暂存起来：


    $ git add CONTRIBUTING.md
    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   CONTRIBUTING.md


## 4. 状态简览
***

`git status` 命令的输出十分详细，但其用语有些繁琐。 如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种更为紧凑的格式输出。 运行 `git status -s` ，状态报告输出如下：


    $ git status -s
     M README
    MM Rakefile
    A  lib/git.rb
    M  lib/simplegit.rb
    ?? LICENSE.txt


新添加的未跟踪文件前面有 `??` 标记，
新添加到暂存区中的文件前面有 `A` 标记，
修改过的文件前面有 `M` 标记。 

你可能注意到了 `M` 有两个可以出现的位置，出现在右边的 `M` 表示该文件被修改了但是还没放入暂存区，出现在靠左边的 `M` 表示该文件被修改了并放入了暂存区。

例如，上面的状态报告显示： 
README 文件在工作区被修改了但是还没有将修改后的文件放入暂存区,
lib/simplegit.rb 文件被修改了并将修改后的文件放入了暂存区, 
Rakefile 在工作区被修改并提交到暂存区后又在工作区中被修改了，所以在暂存区和工作区都有该文件被修改了的记录。

## 5. 忽略文件
***

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件模式。 来看一个实际的例子：


    $ cat .gitignore
    *.[oa]
    *~


第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本。 此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的文档等等。 要养成一开始就设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

**文件 `.gitignore` 的格式规范如下：**

+ 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
+ 可以使用标准的 glob 模式匹配。
+ 匹配模式可以以（/）开头防止递归。
+ 匹配模式可以以（/）结尾指定目录。
+ 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

+ 所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。
+ 星号 `*` 匹配零个或多个任意字符； 
+ `[abc]`  匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；
+ 问号 `?` 只匹配一个任意字符；
+ 如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。 
+ 使用两个星号 `*` 表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

我们再看一个 .gitignore 文件的例子：


    # no .a files
    *.a

    # but do track lib.a, even though you're ignoring .a files above
    !lib.a

    # only ignore the TODO file in the current directory, not subdir/TODO
    /TODO

    # ignore all files in the build/ directory
    build/

    # ignore doc/notes.txt, but not doc/server/arch.txt
    doc/*.txt

    # ignore all .pdf files in the doc/ directory
    doc/**/*.pdf

> GitHub 有一个十分详细的针对数十种项目及语言的.gitignore 文件列表，你可以在*https://github.com/github/gitignore* 找到它.


## 6. 查看已暂存和未暂存的修改
***

如果 `git status` 命令的输出对于你来说过于模糊，你想知道具体修改了什么地方，可以用 `git diff` 命令。

稍后我们会详细介绍 `git diff`，你可能通常会用它来回答这两个问题：当前做的哪些更新还没有暂存？ 有哪些更新已经暂存起来准备好了下次提交？ 尽管 `git status` 已经通过在相应栏下列出文件名的方式回答了这个问题，`git diff` 将通过文件补丁的格式显示具体哪些行发生了改变。

假如再次修改 README 文件后暂存，然后编辑 CONTRIBUTING.md 文件后先不暂存， 运行 `status` 命令将会看到：


    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            modified:   README

    Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   CONTRIBUTING.md



要查看尚未暂存的文件更新了哪些部分，不加参数直接输入 `git diff`：


    $ git diff
    diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
    index 8ebb991..643e24f 100644
    --- a/CONTRIBUTING.md
    +++ b/CONTRIBUTING.md
    @@ -65,7 +65,8 @@ branch directly, things can get messy.
     Please include a nice description of your changes when you submit your PR;
     if we have to read the whole diff to figure out why you're contributing
     in the first place, you're less likely to get feedback and have your change
    -merged in.
    +merged in. Also, split your changes into  comprehensive chunks if your patch is
    +longer than a dozen lines.

     If you are starting to work on a particular area, feel free to submit a PR
     that highlights your work in progress (and note in the PR title that it's


此命令比较的是工作目录中当前文件和暂存区域快照之间的差异， 也就是修改之后还没有暂存起来的变化内容。


若要查看已暂存的将要添加到下次提交里的内容，可以用 `git diff --cached` 命令。（Git 1.6.1 及更高版本还允许使用 `git diff --staged`，效果是相同的，但更好记些。）


    $ git diff --staged
    diff --git a/README b/README
    new file mode 100644
    index 0000000..03902a1
    --- /dev/null
    +++ b/README
    @@ -0,0 +1 @@
    +My Project


请注意，`git diff` 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件后，运行 `git diff` 后却什么也没有，就是这个原因。

像之前说的，暂存 CONTRIBUTING.md 后再编辑，运行 `git status` 会看到暂存前后的两个版本。 如果我们的环境（终端输出）看起来如下：


    $ git add CONTRIBUTING.md
    $ echo '# test line' >> CONTRIBUTING.md
    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            modified:   CONTRIBUTING.md

    Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   CONTRIBUTING.md


现在运行 `git diff` 看暂存前后的变化：


    $ git diff
    diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
    index 643e24f..87f08c8 100644
    --- a/CONTRIBUTING.md
    +++ b/CONTRIBUTING.md
    @@ -119,3 +119,4 @@ at the
     ## Starter Projects

     See our [projects list](https://github.com/libgit2/libgit2/blob/development/PROJECTS.md).
    +# test line


然后用 `git diff --cached` 查看已经暂存起来的变化：（--staged 和 --cached 是同义词）


    $ git diff --cached
    diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
    index 8ebb991..643e24f 100644
    --- a/CONTRIBUTING.md
    +++ b/CONTRIBUTING.md
    @@ -65,7 +65,8 @@ branch directly, things can get messy.
     Please include a nice description of your changes when you submit your PR;
     if we have to read the whole diff to figure out why you're contributing
     in the first place, you're less likely to get feedback and have your change
    -merged in.
    +merged in. Also, split your changes into comprehensive chunks if your patch is
    +longer than a dozen lines.

     If you are starting to work on a particular area, feel free to submit a PR
     that highlights your work in progress (and note in the PR title that it's

> **Git Diff 的插件版本**
> 在本书中，我们使用 git diff 来分析文件差异。 但是，如果你喜欢通过图形化的方式或其它格式输出方式的话，可以使用 git difftool 命令来用 Araxis ，emerge 或 vimdiff 等软件输出 diff 分析结果。 使用 git difftool --tool-help 命令来看你的系统支持哪些 Git Diff 插件。


## 7. 提交更新
***

现在的暂存区域已经准备妥当可以提交了。在此之前，请一定要确认还有什么修改过的或新建的文件还没有 `git add` 过，否则提交的时候不会记录这些还没暂存起来的变化。这些修改过的文件只保留在本地磁盘。所以，每次准备提交前，先用 `git status` 看下，是不是都已暂存起来了，然后再运行提交命令 `git commit` ：


    $ git commit


这种方式会启动文本编辑器以便输入本次提交的说明。(默认会启用 shell 的环境变量$EDITOR
所指定的软件，一般都是 vim 或 emacs。当然也可以按照 起步 介绍的方式，使用 `git config --global core.editor` 命令设定你喜欢的编辑软件。）

编辑器会显示类似下面的文本信息（本例选用 Vim 的屏显方式展示）：


    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    # On branch master
    # Changes to be committed:
    #   new file:   README
    #   modified:   CONTRIBUTING.md
    #
    ~
    ~
    ~
    ".git/COMMIT_EDITMSG" 9L, 283C


可以看到，默认的提交消息包含最后一次运行 `git status` 的输出，放在注释行里，另外开头还有一空行，供你输入提交说明。 你完全可以去掉这些注释行，不过留着也没关系，多少能帮你回想起这次更新的内容有哪些。 (如果想要更详细的对修改了哪些内容的提示，可以用 `-v` 选项，这会将你所做的改变的 `diff` 输出放到编辑器中从而使你知道本次提交具体做了哪些修改。） 退出编辑器时，Git 会丢掉注释行，用你输入提交附带信息生成一次提交。

另外，你也可以在 `commit` 命令后添加 `-m` 选项，将提交信息与命令放在同一行，如下所示：


    $ git commit -m "Story 182: Fix benchmarks for speed"
    [master 463dc4f] Story 182: Fix benchmarks for speed
     2 files changed, 2 insertions(+)
     create mode 100644 README


好，现在你已经创建了第一个提交！ 可以看到，提交后它会告诉你，当前是在哪个分支（master）提交的，本次提交的完整 SHA-1 校验和是什么（463dc4f），以及在本次提交中，有多少文件修订过，多少行添加和删改过。

请记住，提交时记录的是放在暂存区域的快照。 任何还未暂存的仍然保持已修改状态，可以在下次提交时纳入版本管理。 每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。

## 8. 跳过使用暂存区域
***

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤：


    $ git status
    On branch master
    Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   CONTRIBUTING.md

    no changes added to commit (use "git add" and/or "git commit -a")
    $ git commit -a -m 'added new benchmarks'
    [master 83e38c7] added new benchmarks
     1 file changed, 5 insertions(+), 0 deletions(-)


看到了吗？提交之前不再需要 `git add` 文件“CONTRIBUTING.md”了。


## 9. 移除文件
***

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分（也就是 *未暂存清单*）看到：


    $ rm PROJECTS.md
    $ git status
    On branch master
    Your branch is up-to-date with 'origin/master'.
    Changes not staged for commit:
        (use "git add/rm <file>..." to update what will be committed)
        (use "git checkout -- <file>..." to discard changes in working directory)

            deleted:    PROJECTS.md

    no changes added to commit (use "git add" and/or "git commit -a")


然后再运行 `git rm` 记录此次移除文件的操作：


    $ git rm PROJECTS.md
    rm 'PROJECTS.md'
    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            deleted:    PROJECTS.md


下一次提交时，该文件就不再纳入版本管理了。 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 `-f`（译注：即 force 的首字母）。 这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 .a 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 `--cached` 选项：


    $ git rm --cached README


`git rm` 命令后面可以列出文件或者目录的名字，也可以使用 glob 模式。 比方说：


    $ git rm log/\*.log


注意到星号 `*` 之前的反斜杠 ` \ `， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。 此命令删除 log/ 目录下扩展名为 .log 的所有文件。 类似的比如：


    $ git rm \*~


该命令为删除以 ~ 结尾的所有文件。


## 10. 移动文件
***

不像其它的 VCS 系统，Git 并不显式跟踪文件移动操作。 如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。 不过 Git 非常聪明，它会推断出究竟发生了什么，至于具体是如何做到的，我们稍后再谈。

既然如此，当你看到 Git 的 `mv` 命令时一定会困惑不已。 要在 Git 中对文件改名，可以这么做：


    $ git mv file_from file_to


它会恰如预期般正常工作。 实际上，即便此时查看状态信息，也会明白无误地看到关于重命名操作的说明：


    $ git mv README.md README
    $ git status
    On branch master
    Changes to be committed:
        (use "git reset HEAD <file>..." to unstage)

            renamed:    README.md -> README


其实，运行 git mv 就相当于运行了下面三条命令：


    $ mv README.md README
    $ git rm README.md
    $ git add README


如此分开操作，Git 也会意识到这是一次改名，所以不管何种方式结果都一样。 两者唯一的区别是，`mv` 是一条命令而另一种方式需要三条命令，直接用 `git mv` 轻便得多。 不过有时候用其他工具批处理改名的话，要记得在提交前删除老的文件名，再添加新的文件名。



> 本教程参照Git的官方文档编写，如有不明与错误之处，敬请指正。