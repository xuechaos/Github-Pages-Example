#微服务服务发现
根据Martin Fowler对微服务架构的描述中，虽然该架构相较于单体架构有模块化解耦、可独立部署、技术多样性等诸多优点，但是由于分布式环境下解耦，也带出了不少测试与运维复杂度。
https://martinfowler.com/articles/microservices.html

##Netflix Eureka 服务发现

Eureka包含两个组件：Eureka Server和Eureka Client。

Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

Eureka Client是一个java客户端，用于简化与Eureka Server的交互，客户端同时也就别一个内置的、使用轮询(round-robin)负载算法的负载均衡器。

在应用启动后，将会向Eureka Server发送心跳,默认周期为**30**秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认**90**秒)。

Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端**缓存**机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。综上，Eureka通过心跳检查、客户端缓存等机制，确保了系统的高可用性、灵活性和可伸缩性。

##Netflix Ribbon 动态路由和负载平衡器

Ribbon主要提供客户侧的软件负载均衡算法。和其他构成我们NIWS内部进程通信栈的组件一起，该算法在Netflix经历了严峻考验。未来几个月，我们会继续开源其他的NIWS组件。请注意：上面提到的负载均衡器是内部的客户侧负载均衡器，与Eureka一起使用，Eureka主要用来平衡到中间层服务的请求。我们面向公共的边缘服务，继续使用Amazon的ELB服务。

在Netflix，典型的部署架构都是多地区、多区域的部署。Eureka为所有Netflix服务提供服务注册。Ribbon客户端的创建和配置为每个目标服务执行。Ribbon客户端组件提供一系列完善的配置选项，比如连接超时、重试、重试算法等。Ribbon内置可插拔、可定制的负载均衡组件。下面是用到的一些负载均衡策略：

简单轮询负载均衡
加权响应时间负载均衡
区域感知轮询负载均衡
随机负载均衡
文章重点介绍了Ribbon一个久经考验的功能：区域感知负载均衡器（Zone Aware Load Balancer）。
http://www.infoq.com/cn/news/2013/01/netflix-annouced-ribbon



##Netflix Zuul 边缘服务器

Mikey Cohen

http://www.infoq.com/cn/news/2016/10/netflix-zuul-asynch-nonblocking


Netflix使用了细粒度的SOA架构，这是我们基于云的部署模式的根基。目前，我们运行着上百个细粒度服务，使用诸如Netflix API Services这样的“边缘服务（Edge Service）”，共同负责处理面向客户的请求。轻量级的、基于REST的协议，是在这些服务之间进行内部通信的必然之选。

Netflix Internal Web Service Framework（简称NIWS）构成了该架构的基础。我们之前发布的Eureka，起到服务发现的作用。与Eureka一起，NIWS提供执行REST调用需要的所有组件。

NIWS由REST客户端和服务器端框架构成，基于Java的JSR-311RESTful API规范。我们的服务使用多种负载数据序列化格式，比如Avro、XML、JSON、Thrift和Google Protocol Buffers。NIWS提供序列化和反序列化机制。

eureka 通过类似长连接的机制，定时来put订阅，

netflix eureka
已知应用方:雷卷


这次预研的要求比较高，举个例子，像上次敦谷简单的分享consul时就是没有讲透的，代码研究是要的，但止于架构层面了解透彻，不要拘泥于细枝末节，而且对外延要求比较高，比如其依附的整体生态要能讲得清楚，市场上谁在用，用的实际情况如何，预计市场规模有多大 



#Spring 
##Spring boot

Spring Boot是由Pivotal团队提供的全新框架，其设计目的是用来简化新Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。通过这种方式，Boot致力于在蓬勃发展的快速应用开发领域（rapid application development）成为领导者。

##Spring IO 
Let's build a better Enterprise.

Spring helps development teams everywhere build simple, portable,  fast and flexible JVM-based systems and applications.

Spring IO平台
Spring IO将核心Spring API集成到一个用于现代应用程序的内聚和版本化基础平台。 在此基础之上，它还提供针对所选应用程序类型优化的特定于域的运行时环境（DSR）。 Spring IO由Spring IO Foundation和Spring IO Execution层组成。
Spring IO是100％开源，精益和模块化。 您可以部署所需的部件，并且只部署您需要的部件。 单击此处可查看Spring IO平台发行版中包含的模块和版本的详细列表。
Spring IO Foundation层是一组内聚的API和嵌入式运行时组件，使您能够构建满足各种企业需求的应用程序。 对于任何给定的应用程序，你当然只需要包括你的应用程序实际需要的基础的那些部分。 基础使得易于识别一起工作的组件; 它不强迫你包括你不会需要的组件。

##Spring Cloud

对比项目   	|	Dubbo   | Spring Cloud
------|-------------|-------------
服务注册中心|Zookeeper | 	Spring Cloud Netflix Eureka
服务调用方式|RPC	    |REST API
服务网关	|无	 |Spring Cloud Netflix Zuul
断路器	    |不完善	|Spring Cloud Netflix Hystrix
分布式配置	|无	|Spring Cloud Config
服务跟踪	|无	|Spring Cloud Sleuth
消息总线	|无	|Spring Cloud Bus
数据流	|无	|Spring Cloud Stream
批量任务	|无	|Spring Cloud Task


