title: "Git's Life Cycle-add"
date: 2015-08-23 22:44:34
alias: 
tags: 
- 笔记 
- 工具
categories: 总结与分享

---

继续之前的笔记。

## git add语法

``` 
'git add' [--verbose | -v] [--dry-run | -n] [--force | -f] [--interactive | -i] [--patch | -p]
	  [--edit | -e] [--[no-]all | --[no-]ignore-removal | [--update | -u]]
	  [--intent-to-add | -N] [--refresh] [--ignore-errors] [--ignore-missing]
	  [--] [<pathspec>...]
```
**`git add` 添加文件到本地索引**

` <pathspec> ` - 定要添加文件或目录，但不是提交，只会对文件做一个状态的记录，记录文件或目录状态是new
## git add 语法示例：
```
git add .        /*添加当前目录下的所有新文件或新修改的文件（用文件指代，下同），但不包括目录*/
git add /dir/*   /*添加dir目录下的所有文件，或子目录及子目录下的文件*/
git add *.java   /*添加当前目录下所有的java文件，子目录下的java文件会不会添加有待实验*/
git add -A       /*添加本地仓库下所有的文件*/
git add --no-all /*若修改一个文件后你删除了它，但不想让这个删除被记录（此后提交将忽略删除，不常用）*/
```
## 其他可选的选项（options）
``` java

$ git add <pathspec> -n                /*不会真正地添加记录，只是帮助查看有此次有哪些文件将会被添加或被忽略*/
$ git add <pathspec> -f                /*强制添加，即使文件已经在.gitignore中被标记为忽略*/
$ git add <pathspec> -i                /*在Interactive mode中添加文件到仓库的index中*/
$ git add <pathspec> -e                /*将会打开一个编辑器对比文件（新旧对比），在用户关闭编辑器后添加文件记录*/
$ git add <pathspec> -u                /*只会更新现有的已经修改的文件，不会添加文件*/
$ git add <pathspec> --refresh         /*只会更新文件状态，不会新添加文件*/
$ git add <pathspec> ---ignore-missing /*添加过程出错时忽略当前文件，当前文件将添加索引并继续当前操作*/
$ git add <pathspec> --                /*如果添加的文件名是git保留字关键字，则用--来加以区分*/

```
## 参考文档
{% blockquote Git-SCM http://git-scm.com/docs/git-add git-add官方文档%}
{% endblockquote %}
