### git标签操作

###### 1.查看当前库标签

git tags

###### 2.查看git提交简要信息

git log --oneline

![gittag](https://i.loli.net/2019/06/15/5d046ed9cdeb856886.jpg)

###### 3.根据简要id新增tab，红色的根据表示推送到指定的commitid后

 git tag -a v1.0.1 73c8043 -m "v1.0.1版本说明"

提交本地分支
git tab xxx

###### 4.提交标签信息

git push --tag

![push](https://i.loli.net/2019/06/15/5d046eda0f4d597601.jpg)

###### 5.效果

![github效果](https://i.loli.net/2019/06/15/5d046eda49f8640681.jpg)

###### 6.删除&修改

本地删除tag
git tag -d

![删除tag](https://i.loli.net/2019/06/15/5d046eda7882e33886.jpg)

远程 tag 的删除
git push Dubbo --delete v1.0.0
或者
git push Dubbo :refs/tags/v1.0.1