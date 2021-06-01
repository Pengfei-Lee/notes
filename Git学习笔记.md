Git抽象模型



工作区（working directory）：本地添加、修改文件的区域

暂存区（stage/index）：是一个包含了文件索引的目录树，记录了文件名，修改时间等信息，但不存放文件内容。文件内容存在对象库（.git/objects）中。

本地仓库（history）：用于接受暂存区commit过来的文件，存放了所有版本的数据。但也没有直接存放文件内容。

远程仓库：远程托管代码服务器。

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gq0empmqtxj30rw0s8grz.jpg" alt="image-20210429102727054" style="zoom:67%;" />

![image-20210429102946123](https://tva1.sinaimg.cn/large/008i3skNly1gq0ep2pn1tj31ag0sykav.jpg)

> master指向最新的目录树，而HEAD是一个游标指针, 初始指向master分支，随着分支的commit而移动，可以通过checkout命令，分离出来，指向特定的一个提交记录。

branch ：分支操作，相当于对一个版本进行复制，也可以用（branch -f）来移动分支的位置。

> git branch 查看当前分支状况
>
> git branch -d 分支名 删除分支

checkout：切换到指定分支或提交记录（哈希值）。（checkout -b，可以实现创建并切换到新分支）

merge：将不同分支上的修改记录合并在一起。(是将指定的分支，合并到当前分支，合并后指定分支还在原来位置)

绝对引用：每一个提交记录对应一个40位长的hash值，可以用前面若干位能唯一标识一个记录的字符串来表示一个提交记录。

相对引用：`[分支名/提交记录名/HEAD]^`表示上一个，`[分支名/提交记录名/HEAD]~num`表示以上若干个。

撤销commit：

- git revert是用一次新的commit来回滚之前的commit，此次提交之前的commit都会被保留；
- git reset是回到某次提交，提交及之前的commit都会被保留，但是此commit id之后的修改都会被删除



关联远程仓库：

`git remote add origin ssh://git@git.sankuai.com/~lipengfei49/testremoterepository.git`

远程仓库默认为origin，`origin/master`表示远程仓库为origin，分支为master

本地的git文件夹里面对应也存储了git本地仓库master分支的commit ID 和 跟踪的远程分支orign/master的commit ID（可以有多个远程仓库）。打开git文件夹可以看到如下文件：

- .git/refs/head/[本地分支]
- .git/refs/remotes/[正在跟踪的分支]



在本地直接操纵远程分支但没有push的时候。

clone相当于在本地克隆远程仓库

fetch从远程仓库下载本地缺失的提交记录，并更新本地的远程分支指针。但没有改变本地的master分支和磁盘上的文件

> 本地的远程分支保留了最后一次与远程仓库通信时的状态

![image-20210429114704114](https://tva1.sinaimg.cn/large/008i3skNly1gq0gxilqgqj312a0mc0uw.jpg)

pull从远程仓库下载本地缺失的提交记录，并将本地的master和远程分支都更新。`pull = fetch + merge`

> 使用--rebase参数，如`git pull --rebase`就是fetch+merge

push就是将本地仓库的修改记录推到远程仓库，并将本地的远程分支指针和远程仓库的分支都更新。

push之前必须将本地的修改记录更新到远程仓库最新的情况，然后才能push。

> 相当于push是对远程仓库进行写操作，必须是基于最新的



status 可以查看**工作区**的状态，若有文件修改了，还没commit就会显示哪些文件修改了。若全部commit了，就会显示clean。

diff可以查看某个文件被修改后的区别

restore 可以撤回某个文件最近的一次修改

log 可以查看从近到远的commit日志（包括commitID）

`git checkout -- filename`可以撤销工作区的文件至最近一次commit或add后的状态

`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区。

> 以上两条配合使用，可以将已经add了的文件给撤回到上上次的状态。



图形化查看

`git log  --graph --pretty=oneline --abbrev-commit`



git merge冲突：

如果两个分支修改了同一行，且修改内容不同，就判定为合并冲突。

`git tag`可以给最新的commit打标签，

**拉取远程分支到本地分支**

```git
git fetch origin dev
git merge origin/dev # 将本地的远程origin/dev分支合并到本地dev分支上
```

