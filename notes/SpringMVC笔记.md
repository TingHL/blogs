SpringMVC笔记参考连接：

- [尚硅谷SpringMVC视频教程](https://www.bilibili.com/video/BV1D7411n7BZ?from=search&seid=7009241333699660986)
- 

# SpringMVC概述

1. 通过一套MVC注解，让POJO (Plain Olde Java Object）成为处理请求的控制器，而无须实现任何接口
2. 支持REST风格的URL请求
3. 采用了松散耦合可插拔组件结构，比其他MVC框架更具有拓展性和灵活性
4. 一种轻量级的、基于MVC的Web层应用框架。偏前端而不是基于业务逻辑层。Spring框架的一个后续产品

## **Spring能够干什么**

1. 与Spring框架集成
2. 支持Restful风格
3. 进行简洁的Web层开发
4. 支持灵活的URL到页面控制器的映射
5. 因为模型数据不存放在特定的API里，而是放在一个Model里（Map数据结构实现，容易被其他框架使用）
6. 灵活的数据验证、格式化和数据绑定机制、能使用任何对象进行数据绑定，不必实现特定框架的API
7. 更加简单、强大的异常处理
8. 对静态资源的支持
9. 支持灵活的本地化、主题等解析

## SpringMVC怎么玩

1. 将Web层进行了职责解耦，基于请求-响应模型
2. 常用的主要组件：
   - `DispatcherServlet`：前端控制器
   - `Controller`：处理器/页面控制器，做的是MVC中C的事情，但控制逻辑转移到前端控制器了，用于对请求进行处理
   - `HandlerMapping`：请求映射到控制器，找谁来处理，如果映射成功返回一个HanlderExecutionChain对象（包含一个Hanlder处理器（页面控制器）对象、多个`HandlerInterceptor`拦截器对象）
   - `View Resolver`：视图解析器，找谁来处理返回的页面。把逻辑视图解析为具体的view，进行这种策略模式，很容易更换其他视图计数；
     - 如`InternalResourceViewReslover`将逻辑视图名映射为JSP视图
   - `LocalResolver`：本地化、国际化
   - `MultipartResolver`：文件上传解析器
   - `HandlerExceptionResolver`：异常处理器

# 示例工程 HelloWorld



# 附录

## MVC

MVC模式代表Model-View-Controller（模型-视图-控制器）模式。

- **Model（模型）** - 模型代表一个存取数据的对象或 JAVA POJO。它也可以带有逻辑，在数据变化时更新控制器。
- **View（视图）** - 视图代表模型包含的数据的可视化。
- **Controller（控制器）** - 控制器作用于模型和视图上。它控制数据流向模型对象，并在数据变化时更新视图。它使视图与模型分离开。

![img](images/1200px-ModelViewControllerDiagram2.svg_.png)



