## IDEA中使用Debug

###### 断点调试 

Debug方式启动程序，Shift+F9
单步进入方法：`F7`
单步运行(不入方法)，`F8`，Step Over
智能跳入多个方法的断点，提示选择  Shift+F7
跳出函数 `Shift+F8`	
resume（重新开始，继续），F9。会跳转到下一个断点，如果没有断点了，直接结束程序运行

![debug列表参数](https://i.loli.net/2019/06/15/5d046ef74316845767.jpg)

###### 断点处右键菜单

![特定循环条件](https://i.loli.net/2019/06/15/5d046ef7a0e1336492.jpg)

###### 模拟异常

![模拟异常](https://i.loli.net/2019/06/15/5d046ef82d02128971.jpg)



###### 异常断点，通过设置异常断点

面对报错后出现的异常，不需要去找到对应的代码，智能断点。

![智能断点](https://i.loli.net/2019/06/15/5d046ef8b718e17460.jpg)

###### 回退断点

所谓的断点回退，其实就是回退到上一个方法调用的开始处，在[IDEA](http://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247488006&idx=1&sn=d5c66d84724b1deebac6604749d04bf5&chksm=ebd62d2adca1a43cb136b5740621e25854537054b9b3cac7451fd21ea55c0fc247e07a49d8cd&scene=21#wechat_redirect)里测试无法一行一行地回退或回到到上一个断点处，而是回到上一个方法。Drop Frame 

![Drop Frame](https://i.loli.net/2019/06/15/5d046ef90a7d484403.jpg)



或则在线程栈里面右键

![线程栈](https://i.loli.net/2019/06/15/5d046ef94779153497.jpg)



###### 多线程调试

一般情况下我们调试的时候是在一个线程中的，一步一步往下走。但有时候你会发现在Debug的时候，想发起另外一个请求都无法进行了？

那是因为IDEA在Debug时默认阻塞级别是ALL，会阻塞其它线程，只有在当前调试线程走完时才会走其它线程。可以在View Breakpoints里选择Thread，然后点击Make Default设置为默认选项。

![多线程调试](https://i.loli.net/2019/06/15/5d046ef9daa6132297.jpg)