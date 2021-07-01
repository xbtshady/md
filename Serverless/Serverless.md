## Serverless

​	Serverless 是指构建和运行不需要服务器管理的应用程序的概念，无服务器计算是一种**按需提供后端服务**的方法。无服务器提供程序允许用户编写和部署代码，而不必担心底层基础结构。从无服务器供应商处获得后端服务的公司将根据其计算费用，而不必保留和支付固定数量的带宽或服务器数量，因为该服务是**自动扩展**的。请注意，尽管称为无服务器，但仍使用物理服务器，但开发人员无需了解它们。

Serverless 的主要特点有：

- 事件驱动
- 函数在 FaaS 平台中，需要通过一系列的事件来驱动函数执行。
- 无状态
- 因为每次函数执行，可能使用的都是不同的容器，无法进行内存或数据共享。如果要共享数据，则只能通过第三方服务，比如 Redis 等。
- 无运维
- 使用 Serverless 我们不需要关心服务器，不需要关心运维。这也是 Serverless 思想的核心。
- 低成本
- 使用 Serverless 成本很低，因为我们只需要为每次函数的运行付费。函数不运行，则不花钱，也不会浪费服务器资源



## Serverless = FaaS + BaaS

Serverless 则可以理解为运行在 FaaS 中的，使用了 BaaS 的函数。

### FaaS

简单来讲，FaaS（Function as a Service） 就是一些运行函数的平台，比如阿里云的函数计算、AWS 的 Lambda 等。

FaaS本质上是事件驱动的途径或者事件流，这就包含两层意思，一层是事件，说到事件，自然联想到各种MQ组件，用MQ来解耦事件的生产者（事件源）和消费者（服务实例或者function），以及一些相关事件类型、触发规则、通知等概念，在这一点上确实和消息中间件有很大关系；另一层是驱动，也就是说是有流量属性的，MQ里的事件要被消费肯定是要经过网络传给消费者的。看起来就是一个用消息队列来解耦生产者消费者的通用模型，根据事先配置好的触发规则拿到想要的事件，执行预先写好的函数。他的关注点在于对用户屏蔽服务器，用户只需要告诉平台触发条件及触发后的逻辑即可，极大的减轻用户的工作量，提升开发效率。

### BaaS

BaaS（Backend as a Service）则是一些后端云服务，比如云数据库、对象存储、消息队列等。利用 BaaS，可以极大简化我们的应用开发难度。



## Knative

**Kubernetes-based platform to build, deploy, and manage modern serverless workloads，即基于k8s的，集构建、部署和管理serverless工作负载的平台**。它包含三个模块，Build、Serving、Eventing，其中Build已经归档并被Tekton替代，其分别实现容器构建、工作负载管理、事件模型的功能，且分别对应不同的github项目，可以按需独立部署使用。

**Knative 是通过整合容器构建(或者函数)、工作负载管理(和动态扩缩)以及事件模型这三者来实现的一套Serverless标准**。