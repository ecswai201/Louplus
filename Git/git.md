学习分支：https://learngitbranching.js.org/?locale=zh_CN

基础操作：https://backlog.com/git-tutorial/cn/stepup/stepup6_3.html

Progit：https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8

# 1基本操作

## 1.初始配置

Git 自带一个 `git config` 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

1. `/etc/gitconfig` 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 `git config` 时带上 `--system` 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）
2. `~/.gitconfig` 或 `~/.config/git/config` 文件：只针对当前用户。 你可以传递 `--global` 选项让 Git 读写此文件，这会对你系统上 **所有** 的仓库生效。
3. 当前使用仓库的 Git 目录中的 `config` 文件（即 `.git/config`）：针对该仓库。 你可以传递 `--local` 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

配置用户和邮箱

`git config --global user.name "ying.xue"`

`git config --global user.email "ying.xue@unisoc.com"`

配置编辑器 

`git config --global core.editor "'D:\\Program\\Notepad++\\notepad++.exe' -multiInst -notabbar -nosession -noPlugin"`

查看配置

`git config --list` 

配置提交信息模板

`git config --global commit.template C:\\Users\\ying.xue\\commit.template`

如图

![image-20220410224819289](C:/Users/ying.xue/AppData/Roaming/Typora/typora-user-images/image-20220410224819289.png)

## 2.获取 Git 仓库

通常有两种获取 Git 项目仓库的方式：

1. **将尚未进行版本控制的本地目录转换为 Git 仓库**；进入想要进行版本控制的目录，git init

   该命令将创建一个名为 `.git` 的子目录

   

2. **从其它服务器 克隆 一个已存在的 Git 仓库**

如果你想获得一份已经存在了的 Git 仓库的拷贝，要用 `git clone` 命令。 ，Git 克隆的是该 Git 仓库服务器上的几乎所有数据，而不是仅仅复制完成你的工作所需要文件。 当你执行 `git clone` 命令的时候，默认配置下远程 Git 仓库中的每一个文件的每一个版本都将被拉取下来。 事实上，如果你的服务器的磁盘坏掉了，你通常可以使用任何一个克隆下来的用户端来重建服务器上的仓库 （虽然可能会丢失某些服务器端的钩子（hook）设置，但是所有版本的数据仍在，详见 [在服务器上搭建 Git](https://git-scm.com/book/zh/v2/ch00/_getting_git_on_a_server) ）。

克隆仓库的命令是 `git clone <url>` 。 比如，要克隆 Git 的链接库 `libgit2`，可以用下面的命令：

```console
$ git clone https://github.com/libgit2/libgit2
```

这会在当前目录下创建一个名为 “libgit2” 的目录，并在这个目录下初始化一个 `.git` 文件夹， 从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。 如果你进入到这个新建的 `libgit2` 文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：

```console
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

这会执行与上一条命令相同的操作，但目标目录名变为了 `mylibgit`。

Git 支持多种数据传输协议。 上面的例子使用的是 `https://` 协议，不过你也可以使用 `git://` 协议或者使用 SSH 传输协议，比如 `user@server:path/to/repo.git` 。 [在服务器上搭建 Git](https://git-scm.com/book/zh/v2/ch00/_getting_git_on_a_server) 将会介绍所有这些协议在服务器端如何配置使用，以及各种方式之间的利弊。

![image-20220411110047019](C:/Users/ying.xue/AppData/Roaming/Typora/typora-user-images/image-20220411110047019.png)

## 3.版本管理



工作目录下的每一个文件都不外乎这两种状态：**已跟踪** 或 **未跟踪**。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是未修改，已修改或已放入暂存区。简而言之，已跟踪的文件就是 Git 已经知道的文件。

工作目录中除已跟踪文件外的其它所有文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有被放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态，因为 Git 刚刚检出了它们， 而你尚未编辑过它们。

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 在工作时，你可以选择性地将这些修改过的文件放入暂存区，然后提交所有已暂存的修改，如此反复。

![image-20220411110345468](C:/Users/ying.xue/AppData/Roaming/Typora/typora-user-images/image-20220411110345468.png)

**==git status== ==查看哪些文件处于什么状态==**

git diff -查看工作树和 暂存区的区别

git diff  HEAD 查看工作树和最新提交的区别

 **==git add== ==开始跟踪一==个文件**。`git add` 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

使用git add . 来暂存所有的更改

如果 `git status` 命令的输出对于你来说过于简略，而你想知道具体修改了什么地方，可以用 `git diff` 命令。

**==git commit== ==提交更新==**。 在此之前，请务必确认还有什么已修改或新建的文件没有 `git add` 过， 否则提交的时候不会记录这些尚未暂存的变化。 这些已修改但未暂存的文件只会保留在本地磁盘。 所以，每次准备提交前，先用 `git status` 看下，你所需要的文件是不是都已暂存起来了， 然后再运行提交命令 `git commit`：

也可以在 `commit` 命令后添加 `-m` 选项，将提交信息与命令放在同一行，如下所示：

```console
$ git commit -m "哈哈哈哈哈"
```

git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add

**==git rm== 删除文件** ，从暂存区域移除并连带从工作目录中删除指定的文件、

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 `--cached` 选项

git rm --cached  <file>

```console
git rm log/\*.log
```

命令删除 `log/` 目录下扩展名为 `.log` 的所有文件

```console
 git rm \*~
```

该命令会删除所有名字以 `~` 结尾的文件。

**==git mv== ==移动文件或重命名文件**==

```console
 git mv file_from file_to
```

其实，运行 `git mv 111.TXT 123`  就相当于运行了下面三条命令：

```console
$ mv 111.txt 123
$ git rm 111.txt
$ git add 123/111.txt
```

**==git log 查看提交记录==**

不传入任何参数的默认情况下，`git log` 会按时间先后顺序列出所有的提交，最近的更新排在最上面。 正如你所看到的，这个命令会列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

--stat 查看 每次提交的简略统计信息

 `--pretty`。 这个选项可以使用不同于默认格式的方式展示提交历史。 这个选项有一些内建的子选项供你使用。 比如 `oneline` 会将每个提交放在一行显示，在浏览大量的提交时非常有用。 另外还有 `short`，`full` 和 `fuller` 选项，它们展示信息的格式基本一致，但是详尽程度不一

[`git log --pretty=format` 常用的选项](https://git-scm.com/book/zh/v2/ch00/pretty_format) 列出了 `format` 接受的常用格式占位符的写法及其代表的意义。

| 选项  | 说明                                          |
| :---- | :-------------------------------------------- |
| `%H`  | 提交的完整哈希值                              |
| `%h`  | 提交的简写哈希值                              |
| `%T`  | 树的完整哈希值                                |
| `%t`  | 树的简写哈希值                                |
| `%P`  | 父提交的完整哈希值                            |
| `%p`  | 父提交的简写哈希值                            |
| `%an` | 作者名字                                      |
| `%ae` | 作者的电子邮件地址                            |
| `%ad` | 作者修订日期（可以用 --date=选项 来定制格式） |
| `%ar` | 作者修订日期，按多久以前的方式显示            |
| `%cn` | 提交者的名字                                  |
| `%ce` | 提交者的电子邮件地址                          |
| `%cd` | 提交日期                                      |
| `%cr` | 提交日期（距今多长时间）                      |
| `%s`  | 提交说明                                      |

 *作者* 和 *提交者* 之间究竟有何差别， 其实作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。 所以，当你为某个项目发布补丁，然后某个核心成员将你的补丁并入项目时，你就是作者，而那个核心成员就是提交者。 

| 选项              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| `-p`或--patch     | 按补丁格式显示每个提交引入的差异。                           |
| `--stat`          | 显示每次提交的文件修改统计信息。                             |
| `--shortstat`     | 只显示 --stat 中最后的行数修改添加移除统计。                 |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单。                         |
| `--name-status`   | 显示新增、修改、删除的文件清单。                             |
| `--abbrev-commit` | 仅显示 SHA-1 校验和所有 40 个字符中的前几个字符。            |
| `--relative-date` | 使用较短的相对时间而不是完整格式显示日期（比如“2 weeks ago”）。 |
| `--graph`         | 在日志旁以 ASCII 图形显示分支与合并历史。                    |
| `--pretty`        | 使用其他格式显示历史提交信息。可用的选项包括 oneline、short、full、fuller 和 format（用来定义自己的格式）。 |
| `--oneline`       | `--pretty=oneline --abbrev-commit` 合用的简写。              |





| `-<n>`                | 仅显示最近的 n 条提交。                    |
| --------------------- | ------------------------------------------ |
| `--since`, `--after`  | 仅显示指定时间之后的提交。                 |
| `--until`, `--before` | 仅显示指定时间之前的提交。                 |
| `--author`            | 仅显示作者匹配指定字符串的提交。           |
| `--committer`         | 仅显示提交者匹配指定字符串的提交。         |
| `--grep`              | 仅显示提交说明中包含指定字符串的提交。     |
| `-S`                  | 仅显示添加或删除内容匹配指定字符串的提交。 |

```console
git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" \
   --before="2008-11-01" --no-merges -- t/
```

举例：

```console
git log --pretty=format:"%h %s" --graph
```

**==git commit --amend撤销提交==**

如果需要修改提交信息

git commit --amend 会弹出文本编辑器启，可以看到之前的提交信息。 编辑后保存会覆盖原来的提交信息

如果提交后发现忘了暂存某个文件的修改

```console
$ git add forgotten_file
$ git commit --amend
```

最终你只会有一个提交——第二次提交将代替第一次提交的结果。

**取消暂存的文件**（即如何操作暂存区和工作目录中已修改的文件）

```console
git reset HEAD <file>
```

<!--NOTE :HEAD 是什么-->

例如，你已经修改了两个文件并且想要将它们作为两次独立的修改提交， 但是却意外地输入 `git add *` 暂存了它们两个。如何只取消暂存两个中的一个呢？

~<u>`git reset` 是个危险的命令，如果加上了 `--hard` 选项则更是如此。</u>~

**撤消对文件的修改**

将它还原成上次提交时的样子

```console
git checkout -- <file>
```

在介绍分支时再细讲reset 和checkout 具体做了什么

**远程仓库的使用**

git remote  查看远程仓库

想要查看某一个远程仓库的更多信息，可以使用 `git remote show <remote>`

origin ——这是 Git 给你克隆的仓库服务器的默认名字

`-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL

`git clone` 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。 所以，`git fetch origin` 会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意 `git fetch` 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

如何使用git remove 自行添加远程仓库

 运行 `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库

```console
git remote add pb https://github.com/paulboone/ticgit
```

**.gitignore文件**

* 空格不匹配任意文件，可作为分隔符，可用反斜杠转义
* 开头的文件标识注释，可以使用反斜杠进行转义
* ! 开头的模式标识否定，该文件将会再次被包含，如果排除了该文件的父级目录，则使用 ! 也不会再次被包含。可以使用反斜杠进行转义
* / 结束的模式只匹配文件夹以及在该文件夹路径下的内容，但是不匹配该文件
* / 开始的模式匹配项目跟目录
* 如果一个模式不包含斜杠，则它匹配相对于当前 .gitignore 文件路径的内容，如果该模式不在 .gitignore 文件中，则相对于项目根目录
* ** 匹配多级目录，可在开始，中间，结束
* ? 通用匹配单个字符
* * 通用匹配零个或多个字符
* [] 通用匹配单个字符列表

## 常用匹配示例

```.gitignore
bin/: 忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
/bin: 忽略根目录下的bin文件
/*.c: 忽略 cat.c，不忽略 build/cat.c
debug/*.obj: 忽略 debug/io.obj，不忽略 debug/common/io.obj 和 tools/debug/io.obj
**/foo: 忽略/foo, a/foo, a/b/foo等
a/**/b: 忽略a/b, a/x/b, a/x/y/b等
!/bin/run.sh: 不忽略 bin 目录下的 run.sh 文件
*.log: 忽略所有 .log 文件
config.php: 忽略当前路径的 config.php 文件
```

.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。

解决方法就是先把本地缓存删除（改变成未track状态），然后再提交:

git rm -r --cached .
git add .
git commit -m 'update .gitignore'

$ git add App.class
The following paths are ignored by one of your .gitignore files:
App.class
==Use -f if you really want to add them.==

仅在个人系统中忽略文件
.gitignore文件被提交并推送之后，就会在团队共享。若只想在你的系统上排除文件，请编辑本地仓库中的.git/info/exclude文件， 修改这个文件不会共享给其他人，这个动作只对此本地仓库有效。

在个人系统中忽略所有仓库的特定文件
利用git config命令建立全局.gitignore文件：

git config core.excludesfile C:\Users\frank\.gitignore_global


## 4.推送到远程仓库

git push <remote> <branch>当你想分享你的项目时，必须将其推送到上游

```console
git push origin master
```

认情况下，`git push` 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。 这个过程就像共享远程分支一样——你可以运行 `git push origin <tagname>`

标签 tag 

别名 

# 2 git 分支

**Git 保存的不是文件的变化或者差异，而是一系列不同时刻的 快照 。**

在进行提交操作时，Git 会保存一个提交对象（commit object）。 该提交对象会包含一个指向暂存内容快照的指针。 还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。 首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象， 而由多个分支合并产生的提交对象有多个父对象

 假设现在有一个工作目录，里面包含了三个将要被暂存和提交的文件暂存操作会为每一个文件计算校验和（使用我们在 [起步](https://git-scm.com/book/zh/v2/ch00/ch01-getting-started) 中提到的 SHA-1 哈希算法），然后会把当前版本的文件快照保存到 Git 仓库中 （Git 使用 *blob* 对象来保存它们），最终将校验和加入到暂存区域等待提交：

当使用 `git commit` 进行提交操作时，Git 会先计算每一个子目录（本例中只有项目根目录）的校验和， 然后在 Git 仓库中这些校验和保存为树对象。随后，Git 便会创建一个提交对象， 它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。

则这个Git 仓库中有5个对象：3个*blob* 对象（保存着3个文件快照）、一个 **树** 对象 （记录着目录结构和 blob 对象索引）以及一个 **提交** 对象（包含着指向前述树对象的指针和所有提交信息

Git 的分支，其实本质上仅仅是指向提交对象的可变指针或引用（默认指向某一系列提交之首）。 Git 的默认分支名字是 `master`  。Git 的 `master` 分支并不是一个特殊分支。 它就跟其它分支完全没有区别。 之所以几乎每一个仓库都有 master 分支，是因为 `git init` 命令默认创建它，并且大多数人都懒得去改动它。

多次提交是之后的提交对象都会有一个指向其父提交的指针

![image-20220411194510341](C:/Users/ying.xue/AppData/Roaming/Typora/typora-user-images/image-20220411194510341.png)

git branch 会在当前提交上创建一个分支

git checkout -b testing 会创建并切换分支

```console
git branch testing
```

![image-20220411195111858](C:/Users/ying.xue/AppData/Roaming/Typora/typora-user-images/image-20220411195111858.png)

testing 和master指向同一个提交

HEAD 这个指针指向当前所在的本地分支，giit branch 只会创建分支二不会切换分支

切换分支也就是移动HEAD指针

由于 Git 的分支实质上仅是包含所指对象校验和（长度为 40 的 SHA-1 值字符串）的文件，所以它的创建和销毁都异常高效。 创建一个新分支就相当于往一个文件中写入 41 个字节（40 个字符和 1 个换行符）

远程分支

远程引用是对远程仓库的引用（指针），包括分支、标签等等

远程跟踪分支是远程分支状态的引用