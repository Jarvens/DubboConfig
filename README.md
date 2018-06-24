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
