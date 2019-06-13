## 1.node.js

#### 1.node.js

- `npm`: Nodejs下的包管理器。

- webpack: 它主要的用途是通过CommonJS的语法把所有浏览器端需要发布的静态资源做相应的准备，比如资源的合并和打包。
- vue-cli: 用户生成Vue工程模板。（帮你快速开始一个vue的项目，也就是给你一套vue的结构，包含基础的依赖库，只需要 npm install就可以安装）



#### 2.下载

[下载链接](https://nodejs.org/en/)

下载**左边**的即可。

![下载地址](https://i.loli.net/2019/06/10/5cfe1b270896085298.jpg)

#### 3.安装

> 双击安装，选择安装位置。
>
> 我装d盘：D:\Program Files\nodejs\

默认会添加环境变量 D:\Program Files\nodejs\

```powershell
node -v 
```

查看 node 版本，安装成功。

现在安装包默认都是带npm包管理的。

```powershell
npm -v 
```

查看 npm 版本，安装成功。



#### 4.修改缓存路径

##### 修改方式一：



> 默认缓存路径：C:\Users\用户名\AppData\Roaming\npm

**打开安装目录列表**

![nodejs安装目录](https://i.loli.net/2019/06/10/5cfe1b277f52263878.jpg)

新增文件夹：`node_global` 及 `node_cache`

**设置路径**

```powershell
npm config set prefix "D:\Program Files\nodejs\node_global"
npm config set cache "D:\Program Files\nodejs\node_cache"
```

**查看配置**

> npm config ls

![查看配置](https://i.loli.net/2019/06/10/5cfe1b27b6d4794206.jpg)



npm就是类似一个远程仓库的东西，可以灵活的下载各种插件。



**修改下载源**

> npm config set registry=http://registry.npm.taobao.org



**修改环境变量 `node_global**`

![修改环境变量](https://i.loli.net/2019/06/10/5cfe1b27f386866125.jpg)

查看是否修改环境变量成功：

> npm root -g

如果这里没有修改成功。那么再执行命令的时候会报错，安装的npm模块命令会提示命令不存在。



##### 修改方式二：

直接修改配置文件，用户目录下隐藏文件.npmrc，对应就是`‪C:\Users\用户名\.npmrc`

```properties
prefix=D:\Program Files\nodejs\node_global
cache=D:\Program Files\nodejs\node_cache
registry=http://registry.npm.taobao.org
```

然后其他的查看配置，修改环境变量同上。