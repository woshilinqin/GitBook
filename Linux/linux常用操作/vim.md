## :qvim常用命令

- 翻页

> 翻页直接键盘的pgup/pgdn  ctrl+f/b(before）

- 搜索

> `:/xxx` 回车搜索  n下一个，N上一个
>
> `:?xxx` 回车搜索 从后面开始

- 快速跳到头尾，行末:`$`,行首`0`

> `gg`第一行，`G`最后一行

- 光标行居中

> `zz`选的行居中

- 剪切复制粘贴

`ndd`剪切，复制`nyy`，粘贴`p`，P往上粘贴

- 删除

`nx` delete后面，`nX`删除前面n

- 还原上一个操作`u`

- 设置

设置乱码，其中 `fileencoding`这个会修改本地文件有修改。

> :set encoding=utf-8
>
> :set encoding=utf-8 termencoding=gbk fileencoding=utf-8 

设置行号

> :`set nu`mber
>
> 取消
>
> :`set nonu`

### vim块选择

这个功能可以让我们复制一个矩形区域的内容，十分方便。

1. v:字符选择，会将光标经过的地方反白选择；
2. V:行选择；
3. Ctrl+v：块选择；
4. y：复制反白的地方；
5. d：删除反白的地方。

#### vim乱码解决

 vi ~/.vimrc 

用户目录下

设置tab分隔符为4个空格

set tabstop=4

解决终端显示中文乱码的设置

set termencoding=utf-8

解决终端编辑编码

set encoding=utf-8

文件编码

set fileencoding=utf-8

同时log4j也设置为utf-8就可以了。