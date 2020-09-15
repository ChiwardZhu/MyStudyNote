```bash
#列出所有分支
$ git branch 

#列出所有远程分支
$ git branch -r

#新建一个分支，但依旧停留在当前目录
$ git branch branch-name

#新建一个分支，并切换到该分支
$ git checkout -b branch-name

#合并指定分支到当前分支
$ git merge branch-name

#删除分支
$ git branch -d branch-name

#删除远程分支
$ git push origin --delete branch-name
$ git branch -dr 
```



多个分支如果并行执行，就会导致我们代码不冲突，也就是同事存在多个版本

web-api        A

web-admin      B会调用A（修改A的代码）

web-app         C会调用A和B（修改A的代码）

![image-20200321205325436](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200321205325436.png)





master主分支应该非常稳定，用来发布新版本，一般情况下不允许在上面工作，工作一般情况下在新建的dev分支上工作，工作完后，比如要发布或者dev分支代码稳定后可以合并到主分支master上

```bash
#合并指定分支到当前分支
$ git merge branch-name
```



![image-20200321205533857](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200321205533857.png)

![image-20200321205555894](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20200321205555894.png)