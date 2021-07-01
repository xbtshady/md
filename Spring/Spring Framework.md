# Spring Framework



## 核心特性

- **IoC容器(IOC Container)**

- **Spring事件(Events)**

​	基于Java的标准事件来进行扩展的，有一些区别

- **资源管理(Resources)**

​	继承于Java的资源管理，API借鉴了Java核心的底层API的一个实现

- **国际化(i18n)**

​	基于JavaAPI

- **校验(Validation)**

​	Spring自己做的API设置，可以借鉴Bean Validation（Spring做了Bean Validation的整合）

- **数据绑定(Data Binding)**

​	Spring特有的，外面的属性和Java的JOPO来进行setter、getter的一个数据上的转化和绑定

- **类型转换(Type Conversion)**

- **Spring表达式(Spring Express Language)**

- **面向切面编程(AOP)**



## 数据存储

- **Jdbc**

  关系型数据

  JdbcTemplate

- **事务抽象(Reansactions)**

- **Dao支持(Dao Support)**

  例如SqlExceptiond的封装

- **O/R映射(O/R Mapping)**

  jpa

- **XML编列(XML Marshalling)**

  XML序列化

## Web技术

- **Web Servlet**

  - Spring MVC
  - WebSocket

  - SockJS	

- **Web Reactive技术栈(Spring 5开始引入)**

  通常底层是Netty的Web Server 但也可以是Spring MVC

  - Spring WebFlux
  - WebClient
  - WebSocket



## 技术整合

- **远程调用（Remoting）**

  - RMI Java标准的远程方法调用

  - Hessian 社区开源的方案

  - Dubbo 可以基于Hessian 来做

  - Thrift协议

    Spring有一个统一的封装，远程调用通常是采用同步的模式

-  **Java 消息服务（JMS）**

  ​	传统的JMS规范实现

- **Java连接架构（JCA）**

- **Java管理架构（JMX）**

- **Java邮件客户端**

- **本地任务（Tasks）**

  单机版的多线程实现的

- **本地调度(Scheduling)**

- **缓存抽象(Caching)**

- **Spring 测试(Testing)**

  - 模拟对象（Mock）
  - TestContext框架
  - Spring MVC测试
  - Web 测试客户端