---
publish: true
created: 2026-08-27T13:26:09.620Z
modified: 2026-09-14T11:33:42.612Z
---

commit：应该知道每个git commit做了什么
【plumbing】管道：真正实现功能要干的
【porcelain】洗脸盆：给用户看的东西
可以用一些更加底层的管道功能来实现自己的需求
图解git
【stash】
【workspace】
【index】暂存区，告知git可能会提交
快照，当前tree哪些文件，哪些sha
【local repository】
【upstream repository】类似于线上的仓库这种
听起来好棒，但是好像不太实用，还是先听xg的？

object：最重要的一个目录

- commit
- 目录/tree
- 文件
  【blob】二进制文件
  git把文件存给object目录，同时里面记录了sha1sum的结果；内容是类型+大小+具体的内容
  【pack】压缩优化处理，不然文件太多了
  把前两个数字提取出来作为文件夹的名字，这样比较方便索引
  sha 一个套一个？文件名存在另外一个文件里面，所以一个sha是由前一个sha和另外一个东西sha出来的
  git存的都是快照
  更改作为patch发给另一个人，如果要用这个patch就使用apply patch
  precommit：提交之前检查一下格式是正确的？
  push的时候如果远程没有那么可能就会被打回
  但是如果设置了autoSetupRemote=True就会自动set远程
  有当前的config和用户的config
  别的feature是什么
  worktree：在一个新的地方clone一下同一个仓库
  分支：一个指向commit的sha
  heads：可以处于实际存在的分支中也可以处于分离状态，不在任何分支中
  detached head：有时被称作临时分支
  origin：默认的remote名字
  pull=fetch+一些本地操作
  switch：切换分支
  restore修复更改，把当前仓库恢复到上一个分支/状态
  .gitignore：放一些不需要上传到git的文件
  但是如果已经被track过的话就没招
  \--skip-worktree如果已经track到了但是不希望上传git
  info/exclude：本地的gitignore：不会被推送上去
  log：一些记录发生过了什么的日志 史官；也不会被push上去
  git push --force-with-lease：提交的时候没那么force
  检查本地以为的remote和真的remote之间的区别
