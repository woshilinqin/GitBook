**本地安装**: 安装包会被下载到当前所在目录,因此只能在当前目录下使用

**全局安装**: 安装包会被下载到到特定的系统目录下,安装包能够在所有目录下使用。

全局安装 -g
安装的设置的 node_global 目录，命令可以全局使用。没什么空间限制最好都 -g 安装就好了。

查看安装模式：npm get global

设置默认全局安装：npm set global=true

npm 删除模块
【npm uninstall xxx】删除xxx模块； 
【npm uninstall -g xxx】删除全局模块xxx；

查看已经安装过的包
npm list -g --depth 0

-g 表示全局安装的 后面的展开目录的深度

更新
npm outdated 
npm update moduleName

npm uninstall 模块

删除本地模块时你应该思考的问题：是否将在package.json上的相应依赖信息也消除？

npm uninstall 模块：删除模块，但不删除模块留在 package.json 中的对应信息

npm uninstall 模块 --save 删除模块，同时删除模块留在package.json中dependencies下的对应信息

npm uninstall 模块 --save-dev 删除模块，同时删除模块留在package.json中devDependencies下的对应信息

