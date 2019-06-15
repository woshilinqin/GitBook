### git命令

在当前目录新建一个Git代码库
`git init`
新建一个目录，将其初始化为Git代码库
`​git init [project-name]`
下载一个项目和它的整个代码历史

`git clone [url]`

Git的工作流程

![图片来源网络](https://i.loli.net/2019/06/15/5d046ed9363b121722.jpg)

Git add
添加到缓存里面

- `git add .` 

 不加参数默认为将修改操作的文件和未跟踪新添加的文件添加到git系统的暂存区，注意不包括删除

- `git add -u .`

 -u  == --update ，表示将已跟踪文件中的修改和删除的文件添加到暂存区，不包括新增加的文件，注意这些被删除的文件被加入到暂存区再被提交并推送到服务器的版本库之后这个文件就会从git系统中消失了。

-  `git add -A .`

 -A == --all  ， 表示将所有的已跟踪的文件的修改与删除和新增的未跟踪的文件都添加到暂存区。

-  `git add -i .`

 日常工作中了解前三个命令已足够能满足我们的使用需求了，但是了解一下这个交互式拣选操作也是不错的。这个命令它也是作用于版本库中已被跟踪的文件中的执行过修改与删除操作的文件。

###### 查看git远程仓库

```shell
git remote -v
```



###### 清空远程仓库

```
git rm -r --cached .
```

```
git add .
```

```
git commit -m ".gitignore is now working"
```

```
git push -u origin master
```