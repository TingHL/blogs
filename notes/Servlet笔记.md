# Servlet

**运行在Web服务器或应用服务器上的程序，作为Web浏览器或其他HTTP客户端的请求和HTTP服务器上的数据库或应用程序之间的中间层。**

相较于CGI（Common Gateway Interface，公共网关接口），Servlet有以下几点优势：

- 性能明显更好
- Servlet在Web服务器地址空间内执行，这样它就没有必要再创建一个独单的进程来处理每个客户端请求
- Servlet是独立于平台的
- 服务器上的 Java 安全管理器执行了一系列限制，以保护服务器计算机上的资源。因此，Servlet 是可信的。
- Java 类库的全部功能对 Servlet 来说都是可用的。它可以通过 sockets 和 RMI 机制与 applets、数据库或其他软件进行交互。

![Servlet 架构](images/servlet-arch.jpg)

# Servlet主要任务

执行以下主要任务：

- 读取客户端（浏览器）发送的显式的数据。这包括网页上的 HTML 表单，或者也可以是来自 applet 或自定义的 HTTP 客户端程序的表单。
- 读取客户端（浏览器）发送的隐式的 HTTP 请求数据。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
- 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应。
- 发送显式的数据（即文档）到客户端（浏览器）。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
- 发送隐式的 HTTP 响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

# Servlet生命周期

生命周期可以被定义为从创建到毁灭的整个过程。以下是其遵循的过程：

- Servlet 初始化后调用`init()`方法
- Servlet调用`service()`方法处理客户端的请求
- Servlet 销毁前调用`destroy()`方法
- 最后，`Servlet`由`JVM`的垃圾回收器进行垃圾回收

## init()

- 只调用一次，只在第一次创建Servlet 时调用，在后续每次用户请求时不再调用

- Servlet 创建于用户第一次调用对应于该Servlet 的URL时，也可以指定Servlet在服务器第一次启动时被加载

- 当用户调用一个 Servlet 时，就会创建一个 Servlet 实例，每一个用户请求都会产生一个新的线程，适当的时候移交给 doGet 或 doPost 方法。

- init() 方法简单地创建或加载一些数据，这些数据将被用于 Servlet 的整个生命周期。init 方法的定义如下：

  ```java
  public void init() throws ServletException{
      //初始化代码
  }
  ```

-    

## service()

**执行实际任务的主要方法**。Servlet容器（即web服务器）调用service() 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。

每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。

下面是该方法的特征：

```java
public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException{
    //
}
```

service() 方法由容器调用，service 方法在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。所以，您不用对 service() 方法做任何动作，您只需要根据来自客户端的请求类型来重写 doGet() 或 doPost() 即可。

doGet() 和 doPost() 方法是每次服务请求中最常用的方法。下面是这两种方法的特征。

### doGet()

GET 请求来自于一个 URL 的正常请求，或者来自于一个未指定 METHOD 的 HTML 表单，它由 doGet() 方法处理。

```java
public void doGet(HttpServletRequest request,
                  HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
```

### doPost()

POST 请求来自于一个特别指定了 METHOD 为 POST 的 HTML 表单，它由 doPost() 方法处理。

```java
public void doPost(HttpServletRequest request,
                   HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
```

## destroy()

只会调用一次，在servlet 生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。

在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。destroy 方法定义如下所示：

```java
  public void destroy() {
    // 终止化代码...
  }
```

## 架构图

典型的 Servlet 生命周期方案。

- 第一个到达服务器的 HTTP 请求被委派到 Servlet 容器
- Servlet 容器在调用 service() 方法之前加载 Servlet
- 然后 Servlet 容器处理由多个线程产生的多个请求，每个线程执行一个单一的 Servlet 实例的 service() 方法

![Servlet 生命周期](images/Servlet-LifeCycle.jpg)

# Servlet表单数据



























