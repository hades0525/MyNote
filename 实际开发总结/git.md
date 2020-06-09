## 1.分支管理
1. 查看所有远程分支  `git branch -a`
2. 查看本地当前分支 `git branch`
3. 创建分支 `git branch name`
4. 把分支推到远程库  `git push orgin name`
5. 切换分支  `git checkout name`
6. 删除本地分支  `git branch -d name`




## 2.本地项目上传到github
* 1. 在github上新建一个repository
* 2. 选择本地的一个目录进入，右键选择git bash here
* 3. 命令：
```
git clone 新建仓库的url
```
* 4. 完成3后，在目录下就会多一个仓库名的文件夹，将需要上传的项目的所有文件放到那个文件夹里
* 5. `cd 仓库的文件夹名`
* 6. 输入命令
```
git add .
git commit -m "提交的信息"
git push -u origin master

```
* 7. 需要github输入用户名和密码



## 3.提交修改的代码到新建分支（原来的分支代码不变）
1. git checkout -b newbrabch

2. git checkout oldbranch

3. git fetch origin

4. git reset --hard origin/oldbranch

## 4.暂时保存和恢复进度
正在dev分支开发新功能，做到一半时有人过来反馈一个bug，让马上解决，但是新功能做到了一半你又不想提交，这时就可以使用git stash命令先把当前进度保存起来，然后切换到另一个分支去修改bug，修改完提交后，再切回dev分支，使用git stash pop来恢复之前的进度继续开发新功能。
1. git stash save 'msg'

2. git stash list  查看