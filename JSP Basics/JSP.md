# 第一章 `JSP` 入门

## 第一节 程序结构

互联网发展到现在出现过两种结构：C / S结构 和 B / S结构

![](./img/互联网结构1-1.png)

### 1. C / S 结构介绍

​	C / S 是 Client / Server 的简称，C / S 在技能上非常成熟，它的重要特征就是交互性强、拥有安全的存取形式，网络通信数量低、响应速度快、利于处置大量数据。

- 优点：
  1. 优秀的处理能力，很多工作能够在客户端处理后再提交给服务器，减少了服务器端的开销，因此，C / S结构的客户端响应速度快
  2. 操作界面漂亮、形式多样，能够足够满足客户自己的个性化要求
  3. 安全性能能够非常容易确保，能够对权限实行多层次校验，对信息安全的控制能力非常强
- 缺点：
  1. 需要安装客户端程序，分布功能弱
  2. 兼容性差

### 2. B / S 结构介绍

​	B / S 是 Browser / Server 的简称，就是只安装维护一个服务器，而客户端选用浏览器运行软件。B / S 结构应用程序相对于传统的C / S结构应用程序就是一个特别大的进步。B / S 结构的重要特征就是分步性强、维护方便、开发简单并且共享性强、总体拥有费用低

- 优点：
  1. 分布性强，只需有网络、浏览器，能够随时随地实行查询、浏览等业务处理
  2. 业务拓展简单便利，通过添加网页就可以添加服务器功能
  3. 维护简单便利，只需要更改网页，就可以完成全部用户的同步更新
  4. 开发简单，共享性强
- 缺点：
  1. 个性化特征明显减少，没办法完成拥有个性化的功能要求
  2. 在跨浏览器上，B / S 结构不尽如人意
  3. 在速度与安全性上需要话覅超大的设计费用



## 第二节 Web服务器

### 1. Web 服务器概念

Web 服务器是可以向发出请求的浏览器提供文档的<font color=red>**程序**</font>，主要提供网上的信息浏览服务

### 2. 常见的Web服务器

-  `IIS` (Micro Soft)
- **Tomcat** (Apache) [重点]
- `WebLogic` (Oracle)
- `webSphere` (IBM)
- `Nginx`
- ...



## 第三节 Tomcat 服务器

Tomcat 是 Apache 软件基金会( Apache Software Foundation) 的 Jakarta 项目中的一个核心项目，由 Apache、Sun和其他一些公司及个人共同开发而成。Tomcat 服务器是一个免费的开放源代码的 Web 应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是调试 `JSP` 程序的首选

### 1. Tomcat 目录结构

| 目录       | 说明                                                     |
| ---------- | -------------------------------------------------------- |
| `/bin`     | 存放各种平台下用于启动和停止Tomcat的脚本文件             |
| `/conf`    | 存放Tomcat 服务器的各种配置文件                          |
| `/lib`     | 存放Tomcat 服务器所需的各种 `JAR` 文件                   |
| `/logs`    | 存放Tomcat 的日志文件                                    |
| `/temp`    | Tomcat 运行时用于存放临时文件                            |
| `/webapps` | 当发布Web应用时，默认情况会将Web应用的文件存放于此目录中 |
| `/work`    | Tomcat把由`JSP` 生成的 `Servlet` 存放于此目录下          |

### 2. 部署第一个应用

在 `webapps` 文件夹下创建一个文件夹 `firstApp`，编写一个 `first.html` ，然后放入 `firstApp` 文件夹

`first.html`

```html
<!DOCTYPE html>
<html lang="cn">
	<head>
   		<meta charset="UTF-8">
    	<meta http-equiv="X-UA-Compatible" content="IE=edge">
    	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    	<title>第一个应用</title>
	</head>
	<body>
    	<h1>This Is My First APP!</h1>
	</body>
</html>
```

### 3. 启动 Tomcat 服务器

进入 `bin` 目录，然后双击 `startup.bat` ，启动 Tomcat 服务器

### 4. 访问 Tomcat 服务器

在浏览器地址栏输入网址 http://localhost:8080//firstApp/first.html 进行访问

这个网址有个专业名称，叫做 <font color=red>**统一资源定位符**</font>

统一资源定位符包含了一下三部分

- **协议**

  比如 `http`

- **主机地址**

  localhost 127.0.0.1 都是表示本机，可以用来代替本机的 `IP` 地址

  主机地址包括了主机 `IP` 地址和端口号，比如 `IP` 地址 `localhost`，端口号8080

- **资源地址**

  比如 `/firstApp/first.html`

### 5. 关闭 Tomcat 服务器

进入 `bin` 目录，然后 双击 `shutdown.bat`，关闭 Tomcat 服务器 或者直接关闭  `startup.bat` 启动的终端



## 第四节 Tomcat 配置

### 1. 端口号配置

进入 `conf` 文件夹，找到 `server.xml` 文件，使用文本编辑器打开，找到以下内容

```xml
<Connector port="8080" protocol="HTTP/1.1"
		   connectionTimeout="20000"
		   redirectPort="8443" />
```

<font color=blue>**解释说明:**</font>

- **`port`**

  端口号，默认配置为8080，可修改

- **`protocol`**

  Tomcat 服务器使用协议，默认配置为 `HTTP` 协议，HTTP协议版本为1.1

- **`connectionTimeout`**

  访问 Tomcat 时，连接的超时时间，默认配置为20000毫秒

- **`redirectPort`**

  重定向端口，默认配置8443，主要是针对于访问 Tomcat服务器上的资源时，如果该资源需要使用 `HTTP` 访问，此时，Tomcat 会将这个请求重定向到8443端口

### 2. 资源路径配置

#### 2.1 资源准备

在任一磁盘上 (比如D盘) 创建资源文件夹 actual，然后编写一个 `test.html`，将其放入actual 文件夹中

```xml
<Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
	<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
           prefix="localhost_access_log" suffix=".txt"
           pattern="%h %l %u %t &quot;%r&quot; %s %b" />
</Host>
```

在 `<Host></Host>` 标签对之间添加如下内容

```xml
<Context path="/virtual" docBase="D:/AI/actual" />
```

#### 2.3 访问测试

在浏览器地址栏输入 http://localhost:9000/virtual/test.html 进行访问

> 注：本质是一种映射关系

### 3. web.xml 配置

#### 3.1 会话超时配置

```xml
<session-config>
        <!-- 这里的单位是分钟 -->
	<session-timeout>30</session-timeout>
</session-config>
```

会话指的是用户访问 Tomcat 服务器的有效时间。比如，当用户登录某网站后，30分钟内没，没有进行任何操作，此时，用户与该网站的会话已经超时。如果再进行页面操作，那么服务器将提示用户重新登录

#### 3.2 欢迎页配置

```xml
<welcome-file-list>
	<welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

所谓的欢迎页就是当用户访问 Tomcat 服务器资源时，没有任何资源进行定位，此时，Tomcat 将使用配置的欢迎页进行展示

## 第五节 IDEA 创建 Web 工程



## 第六节 初始 `JSP`