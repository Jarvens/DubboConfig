## Dubbo配置详解
### 三大类
- ###### 服务发现： 表示用于服务发现以及注册，目的是让消费者找到提供者
- ###### 服务治理： 表示该配置项用于治理服务间的关系，或为开发测试提供便利条件
- ###### 性能调优： 表示该配置项用于调优性能，不同的选项会对性能产生不同的影响

### 四种配置方式
###### XML配置
```java
<!--应用名称-->
<dubbo:application name="dubbo-consume"/>

<!--注册地址-->
<dubbo:registry address="zookeeper://127.0.0.1:2181"/>

<!--暴露端口-->
<dubbo:protocol name="dubbo" port="3000"/>

<!--暴露的服务-->
<dubbo:service interface="com.example.Service" ref="service"/>
<bean id="service" class="com.example.ServiceImpl"/>
```
###### 标签介绍

<table>
<tr>
<td>标签</td>
<td>用途</td>
<td>解释</td>
</tr>
<tr>
<td>dubbo:service</td>
<td>服务配置</td>
<td>用于暴露一个服务，定义服务的元信息，一个服务可以用多个协议暴露，一个服务也可以注册到多个注册中心</td>
</tr>

<tr>
<td>dubbo:reference</td>
<td>引用配置</td>
<td>用于创建一个远程服务代理，一个引用可以指向多个注册中心</td>
</tr>

<tr>
<td>dubbo:protocol</td>
<td>协议配置</td>
<td>用于配置提供服务的协议信息，协议由提供者指定，消费者被动接收</td>
</tr>

<tr>
<td>dubbo:application</td>
<td>应用配置</td>
<td>用于配置当前的应用信息，不管该应用是提供者还是消费者</td>
</tr>

<tr>
<td>dubbo:module</td>
<td>模块配置</td>
<td>用于配置当前的模块信息，可选</td>
</tr>

<tr>
<td>dubbo:registry</td>
<td>注册中心配置</td>
<td>用于连接注册中心的相关信息</td>
</tr>

<tr>
<td>dubbo:monitor</td>
<td>应用中心监控</td>
<td>用于配置连接监控中心相关的信息，可选</td>
</tr>

<tr>
<td>dubbo:provider</td>
<td>提供者配置</td>
<td>当ProtocolConfig和ServiceConfig的某属性没有配置时，采用此默认值，可选</td>
</tr>

<tr>
<td>dubbo:consumer</td>
<td>消费者配置</td>
<td>当ReferenceConfig的某属性没有配置时,采用此默认值，可选</td>
</tr>

<tr>
<td>dubbo:method</td>
<td>方法配置</td>
<td>用于ServiceConfig和ReferenceConfig指定方法级别的配置信息</td>
</tr>

<tr>
<td>dubbo:argument</td>
<td>参数配置</td>
<td>用于指定方法参数的配置信息</td>
</tr>
</table>


###### 配置信息的优先级顺序

- <font color=#6a6d>方法级优先，接口次之，全局配置再次之</font>
- <font color=#6a6d>如果级别一样，则消费者优先，提供者次之</font>

>提示：建议由服务提供者设置超时，因为服务提供者更清楚一个方法需要执行多久，如果一个消费者同时引用了多个服务，就不需要关心每个服务的超时设置，
理论上，ReferenceConfig的非服务标识的配置，都可以在ConsumerConfig，ServiceConfig和ProviderConfig中进行默认配置

------
###### 属性配置
> 提示：我们还可以对Dubbo使用properties文件进行配置，比如，如果存在公共配置很简单，又没有多注册中心和多协议等情况，
或者想让多个spring容器共享配置，就可以使用dubbo.properties作为默认的配置。Dubbo会自动加载classpath根目录下的dubbo.properties
文件。也可以通过JVM启动参数 -Ddubbo.propertie.file=example.properties来指定配置文件


- <font color=#6a6 size=3px>属性配置遵循以下规则</font>

 - 将XML配置的标签名加属性名，用点分割，将多个属性拆成多行
 - dubbo.application.name=dubbo-consume 等价于 <dubbo:application name="dubbo-consume">
 - dubbo.protocol.name=dubbo和dubbo.protocol.port=3000 等价于 <dubbo:protocol name="dubbo" port="3000">
 - 如果有多行同名标签配置，则可以用id号区分，如果没有，则对所有同名标签生效
 - dubbo.protocol.rmi.port=1000 等价于 <dubbo:protocol id="rmi" name="rmi" port=1000>

> 提示：配置信息的优先级
- JVM 启动-D 参数有限，这样可以使用户在部署和启动时进行参数重写，比如在启动时需要改变协议端口
- XML 次之，如果在XML中有配置，则dubbo.properties中的相应配置无效
- properties最后，相当于默认值，只有XML没有配置时，properties的相应配置才会生效，通常用于共享配置
----
###### API配置 硬编码方式进行配置，生产环境不推荐使用所以不做详解

###### 注解配置
> 我们还可以通过注解的方式对dubbo进行配置。该方式是dubbo在2.5.7版本之后新增的。可以节省XML配置和属性配置，本人比较倾向于注解配置

```java
@Configuration
public class DubboConfiguration{

   @Bean
   public ApplicationConfig applciationConfig(){
     ApplciationConfig config = new ApplciationConfig();
     config.setName("dubbo");
     return config;
   }

   @Bean
   public RegistryConfig registryConfig(){
     RegistryConfig config = new RegistryConfig();
     config.setAddress("zookeeper://127.0.0.1:2181");
     //由于dubbo支持多种注册中心 会在后面讲解
     config.setClient("curator");
     return config;
   }

   //......更多配置请查看com.alibaba.dubbo.config包

}
```
> 暴露服务

```java
//此处的service是dubbo提供的注解 不要和spring提供的弄混淆了
@Service(timeout=5000)
public Class HelloServiceImpl implements HelloService{
  @Override
  public String sayHello(){
    return "Hello";
  }
  
}

```

> 配置消费者公共模块

```java
@Configuration
public class DubboConfiguration{
  @Bean
  public ApplicationConfig applicationConfig(){
   ApplciationConfig config = new ApplicationConfig();
   config.setName("dubbo-consume");
   return config;
  }

  @Bean
  public ConsumerConfig consumerConfig(){
   ConsumerConfig config = new ConsumerConfig();
   config.settimeOut(5000);
   return config;
  }
}
```

> 使用Reference 注解引用服务
```java
 @Controller
 public class HelloController{


   @Reference
   public HelloService  helloService;

   @GetMapping("/hello")
   public String hello(){
     return helloService.sayHello();
   }


 
 }

```


###### 服务注册与发现
> Dubbo不仅支持多种注册中心 还支持同时向多个注册中心注册

- Multicast注册中心：该注册中心不需要启动任何中心节点，只要广播地址一样，就可以相互发现，组播受网络结构限制，只适合小规模应用，组播地址段为224.0.0.0~239.255.255.255

- Zookeeper注册中心：Zookeeper是Apache Hadoop的子项目，是一个树形的目录服务，它以Fast Paxos算法为基础，为分布式应用提供一致性服务，还支持变更推送服务

- Redis注册中心：基于redis实现的注册中心，使用redis的Key/Map结构存储服务的URL地址以及过期时间，同时使用redis的发布订阅事件通知数据变更

- Simple注册中心：它本身就是一个普通的dubbo服务，可以减少第三方依赖，使整体通信方式一致

> 提示：dubbo目前支持zkclient和curator这两种Zookeeper客户端实现，默认情况下使用zkclient。如果想使用curator客户端则可以通过<dubbo:registry address="zookeeper://127.0.0.1:2181" client="curator">

```xml

//进行zookeeper分组
<dubbo:registry id="hangzhouRegistry" protocol="zookeeper" address="127.0.0.1:2181" group="hangzhouGroup"/>
<dubbo:registry id="xvzhouRegistry" protocol="zookeeper" address="127.0.0.1:2181" group="xvzhouGroup"/>


//zookeeper集群配置
<dubbo:registry address="zookeeper://127.0.0.1:2181?backup=192.168.1.11:2181,192.168.1.12:2181"/>
<dubbo:registry protocol="zookeeper" address="192.168.1.10:2181,192.168.1.11:2181,192.168.1.12:2181"/>


//多注册中心配置
<dubbo:registry id="hangzhouRegistry" address="127.0.0.1:2181"/>
<dubbo:registry id="xvzhouRegistry" address="127.0.0.1:2182" default="false"/>

//向多个注册中心注册
<dubbo:service interface="com.example.HelloService" version="1.0.0" ref="helloService" registry="hangzhouRegistry,xvzhouRegistry"/>

```

> 延迟暴露服务

```xml

//延迟5秒
<dubbo:service delay=5000/>

//等spring容器初始化完毕后暴露
<dubbo:service delay="-1"/>
```
> 如果一个服务并发量过大，想对它进行并发数控制，可以通过如下设置进行 服务端控制
```xml

<dubbo:service interface="com.example.HelloService" executes="10"/>

//限制接口中指定方法
<dubbo:service interface="com.example.HelloService">
  <dubbo:method name="sayHello" executes="10"/>
</dubbo:service>

```

> 客户端控制并发
```xml
<dubbo:service interface="com.example.HelloService" actives="10"/>

//不推荐在客户端进行并发数控制，应该在服务提供方进行控制
<dubbo:reference interface="com.example.HelloService" actives="10"/>
```


> 提示：如果线程数超过了给定的值，则会报类似以下的异常信息

```java

Caused by:java.util.concurrent.RejectedExecutionException:Thread pool is EXHAUSTED! 
Thread Name:DubboServerHandler-127.0.0.1:2181,pool size:200 (active:200,core:200,max:200,largest:200)
```

> 提示：为了保障服务的稳定性还可以限制服务端的连接数
```xml
<dubbo:provider protocol="dubbo" accepts="10"/>

//或者
<dubbo:protocol name="dubbo" accepts="10"/>


//限制客户端的连接数
<dubbo:reference interface="com.example.HelloService" connections="10"/>
<dubbo:service interface="com.example.HelloService" connections="10"/>

如果<dubbo:service> 和<dubbo:reference>都配置了connections 则 <dubbo:reference>优先
```

> 提示：在服务暴露的过程中，除了常用的控制并发、控制连接数、服务隔离也是非常重要的一项措施，服务隔离是为了在系统发生故障时限定传播范围和影响范围，从而保证
只有出问题的服务不可用，其它服务还是正常的，隔离一般有线程隔离、进程隔离、读写隔离、集群隔离和机房隔离，Dubbo同时还提供了分组隔离






