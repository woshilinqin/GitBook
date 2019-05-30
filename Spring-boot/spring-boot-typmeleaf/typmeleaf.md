### thymeleaf

###### 1.简介

简单说， Thymeleaf 是一个跟 Velocity、FreeMarker 类似的模板引擎，它可以完全替代 JSP 、html。

###### 2.springboot 整合 

```
<!-- Thymeleaf需要的jar -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

###### 3.配置

```properties
#不缓存页面
spring.thymeleaf.cache = false

#默认配置（不写就默认这个）
#spring.thymeleaf.prefix = classpath:/templates/  
#spring.thymeleaf.suffix = .html  
```

###### 4.控制器

```java
@Controller
public class HelloController {
    @RequestMapping("/hello")
    public String hello(Model model, @RequestParam(value="name", required=false, defaultValue="World") String name) {
    	//单个属性
    	model.addAttribute("name", name);
    	model.addAttribute("yes", "yes");
    	//对象
    	User user=new User("linqin","22");
    	model.addAttribute(user);
    	//list
    	User user1=new User("linqin1", "22");
    	List<User> list=new ArrayList<>();
    	list.add(user);
    	list.add(user1);
    	model.addAttribute("list",list);
        return "hello";
    }
    @RequestMapping("/index")
    public String index() {
        return "index";
    }
}
```

###### 5.对应 html 页面

位于resource 目录 template 文件夹下

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8" />
<title>Hello Thymeleaf!</title>
</head>
<body>
	<!-- 单个属性使用变量表达式${} -->
	<!-- 字符串拼接，字符串要用单引号分隔开 ，或者使用||包围替换,th:text替换文本-->
	<p th:text="'Hello, ' + ${name} + '!'" />
	<p th:text="'我的名字年龄：'+ ${user.name}" />
	<p th:text="|替换+${user.name}|"></p>
	<hr />
	<h4>th:object替换对象，下面的可以使用表达式获取，或者选择表达式（*）：节省头部，需要预先指定对象</h4>
	<div th:object="${user}">
		<span th:text="${user.name}">名字</span><br /> <span th:text="*{age}">年龄
		</span><br />
	</div>

	<hr />
	<h4>条件判断</h4>
	<a th:if="${user.name=='linqin'}">th:if判断通过 </a>
	<br />
	<a th:unless="${user.name != 'linqin'}" th:href="@{www.baidu.com}">th:unless除非判断</a>
	<br />
	<a th:text="'if/else'+${user.name=='linqind' ? 'linqin' : ' lin'}"></a>
	<br />
	<a th:text="|算术运算：${!(5*5==25) and not (5-5==0)}|"></a>
	<br />

	<hr />
	<h4>for循环,userStat是状态变量，有
		index,count,size,current,even,odd,first,last等属性，如果没有显示设置状态变量，thymeleaf会默
		认给个“变量名+Stat"的状态变量。</h4>
	<table>
		<thead>
			<tr>
				<th>Description</th>
				<th>Price</th>
				<th th:text="${name}"></th>
				<th th:text="${name}"></th>
			</tr>
		</thead>
		<tbody>
			<tr th:each="user,userStat:${list}">
				<td th:text="${user.name}">Red Chair</td>
				<td th:text="${'$' + #numbers.formatDecimal(user.age, 1, 2)}">$123</td>
				<td th:text="*{age}">2014-12-01</td>
				<td th:text="${userStat.index}">当前位置，从0开始算</td>
				<td th:text="${userStat.count}">当前位置，从1开始算</td>
				<td th:text="${userStat.size}">size</td>
				<td th:text="${userStat.current}">显示这个user对象，</td>
				<td th:text="${userStat.even}">even布尔值，当前循环是否是偶数/奇数（从0开始计算）</td>
				<td th:text="${userStat.first}">first布尔值，是否第一个</td>
				<td th:text="${userStat.last}">last布尔值，是否最后一个</td>
			</tr>
		</tbody>
	</table>

	<br />
	<h4>url自带,连接符号，@等于前缀;括号自动识别加问号或者与号</h4>
	<a th:href="@{/hello?name=linqin(name=${user.name})}">view</a>
	<a th:href="@{/order/details(id=${orderId})} ">view2</a>
	<br/>
	
	<h5>为了模板更加易用，Thymeleaf还提供了一系列Utility对象（内置于Context中），可以通过#直接访问： -
		#dates - #calendars - #numbers - #strings - arrays - lists - sets -
		maps</h5>

	<!--  Format date with the specified pattern * Also works with arrays,lists or sets  -->
	${#dates.format(date, 'dd/MMM/yyyy HH:mm')}
	${#dates.arrayFormat(datesArray, 'dd/MMM/yyyy HH:mm')}
	${#dates.listFormat(datesList, 'dd/MMM/yyyy HH:mm')}
	${#dates.setFormat(datesSet, 'dd/MMM/yyyy HH:mm')}
	<hr />
	<!--  Create a date	(java.util.Date) object for the current date and time  -->
	<span th:text="${#dates.createNow()}"></span>
	<br />
	<!--  Create a date (java.util.Date) object for	the current date (time set to 00:00)  -->
	<span th:text="${#dates.createToday()}"></span>
	<hr />

	<!--  Check whether a String is empty (or null). Performs a trim() operation before check
      Also works with arrays, lists or sets  -->

	<span th:text="${#strings.isEmpty(name)}">false</span>
	<br />${#strings.isEmpty(name)}
	<br /> ${#strings.arrayIsEmpty(nameArr)}
	<br /> ${#strings.listIsEmpty(nameList)}
	<br />${#strings.setIsEmpty(nameSet)}
	<br />
	<span th:text="${#strings.startsWith(name,'W')}">true</span>
	<br />
	<span th:text="${#strings.endsWith(name,'d')}">true</span>
	<br />
	<span th:text="${#strings.length(name)}">5</span>
	<br />
	<span th:text="${#strings.equals(name,'linqin')}">true</span>
	<br />
	<span th:text="${#strings.equalsIgnoreCase(name,'LinQIn')}">true</span>
	<br />
	<span th:text="${#strings.concat(name,'dsd')}">true</span>
	<br />
	<span th:text="${#strings.concatReplaceNulls(name,'dd')}">true</span>
	<br />
	<!-- 直接使用#号调用，默认的国际化文件，messages.properties，messages_zh_CN.properties（中文），messages_en_US.properties（英文） -->
	<span th:text="#{welcome}"></span>
	<br />
</body>
</html>
```

页面内嵌

父页面：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8"></meta>
<title>index</title>
</head>
<body >
	<div th:include="top :: top"></div>
</body>
</html>
```

子页面

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8"></meta>
<title>index</title>
</head>
<body >
	<top th:fragment="top">我是头部 </top>
</body>
</html>
```

资源文件 messages.properties

```properties
#（默认，当找不到语言的配置的时候，使用该文件进行展示）
welcome = 欢迎你登录form1.cn网站（default）
```

