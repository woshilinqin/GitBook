### **阿尔萨斯**

[github](https://github.com/alibaba/arthas)

### 观察方法出参和返回值

```shell
$ watch demo.MathGame primeFactors "{params,returnObj}" -x 3
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 44 ms.
ts=2018-12-03 19:16:51; [cost=1.280502ms] result=@ArrayList[
    @Object[][
        @Integer[535629513],
    ],
    @ArrayList[
        @Integer[3],
        @Integer[19],
        @Integer[191],
        @Integer[49199],
    ],
]
```

### 调整`-x`的值，观察具体的方法参数值

```
$ watch demo.MathGame primeFactors "{params,target}" -x 3
```

- `-x`表示遍历深度，可以调整来打印具体的参数和结果内容，默认值是1。

### 观察异常信息的例子

```shell
$ watch demo.MathGame primeFactors "{params[0],throwExp}" -e -x 2
Press Ctrl+C to abort.
Affect(class-cnt:1 , method-cnt:1) cost in 62 ms.
ts=2018-12-03 19:38:00; [cost=1.414993ms] result=@ArrayList[
    @Integer[-1120397038],
    java.lang.IllegalArgumentException: number is: -1120397038, need >= 2
    at demo.MathGame.primeFactors(MathGame.java:46)
    at demo.MathGame.run(MathGame.java:24)
    at demo.MathGame.main(MathGame.java:16)
,
]
```

- `-e`表示抛出异常时才触发
- express中，表示异常信息的变量是`throwExp`