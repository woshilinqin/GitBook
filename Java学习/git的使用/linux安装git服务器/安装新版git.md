###### 1.yum install git

一般都是比较落后的版本

移除他
  yum remove git

###### 2.从github上下载最新的源码编译后安装git

https://github.com/git/git/release

wget https://github.com/git/git/archive/v2.19.1.tar.gz

解压，进入目录：
安装编译所需要的依赖
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker
编译
make prefix=/usr/local/git all
安装到指定目录
make prefix=/usr/local/git install

配置环境变量
`source`一下
查看环境变量 `echo $PATH`

查看git版本`git --version`  