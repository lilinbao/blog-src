title: "Git's Life Cycle-push"
date: 2015-08-23 22:44:59
alias: 
tags: 
- 笔记 
- 工具
categories: 总结与分享

---
终于到push了,比较复杂 ==！

## git push语法

``` 
'git push' [--all | --mirror | --tags] [--follow-tags] [--atomic] [-n | --dry-run] [--receive-pack=<git-receive-pack>]
	   [--repo=<repository>] [-f | --force] [--prune] [-v | --verbose]
	   [-u | --set-upstream] [--signed]
	   [--force-with-lease[=<refname>[:<expect>]]]
	   [--no-verify] [<repository> [<refspec>...]]
```
**`git push` - 是提交本地已提交的代码到远程仓库**
提交之前可以使用：
- `git status`查看当前工作在哪个分支上
- `git config user.name "test"` 设置全局提交用户名
- `git config user.mail "test@gmail.com"` 设置全局提交帐号邮箱
- `git push <远程主机名> <本地分支名>:<远程分支名>` 提交到远程指定分支


## git push 语法示例：
```
git push                  /*如果只有一个分支，则可以省略本地/远程分支名*/
git push origin           /*将当前分支推送到origin主机的对应分支*/
git push origin master    /*将本地的master分支推送到origin主机的master分支。如果后者不存在，则会被新建。如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支*/
git push -u origin master /*将本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了*/
git push --all origin     /*将所有本地分支都推送到origin主机*/
git push --force origin   /*使用–force选项，结果导致在远程主机产生一个”非直进式”的合并(non-fast-forward merge)。除非你很确定要这样做，否则应该尽量避免使用–force选项*/
git push origin --tags /*push的时候将tag一起push到远程主机*/
```

## 注意事项
```
$ git push origin :master /*将删除origin主机的master分支，等同于*/
$ git push origin --delete master
```
需要说明的是，默认情况下 这条语句等价于提交 本地的master分支，到远程仓库，并作为远程的master分支。
如果想把 本地的某个分支test 提交到 远程仓库，并作为远程仓库的master分支，或者 作为另外一个名叫test的分支，那么可以这么做。

$ git push origin test:master         // 提交本地test分支 作为 远程的master分支
$ git push origin test:test              // 提交本地test分支作为远程的test分支

如果想删除远程的分支呢？类似于上面，如果:左边的分支为空，那么将删除:右边的远程的分支。

$ git push origin :test              // 刚提交到远程的test将被删除，但是本地还会保存的，不用担心。
## 参考文档
{% blockquote Git-SCM http://git-scm.com/docs/git-push git-push官方文档%}
{% endblockquote %}