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


