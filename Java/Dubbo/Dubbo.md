# ***Dubbo***

--------------
[文档](https://dubbo.apache.org/zh/docs/v2.7/)

## 基础知识

### 背景
> 网站的演变催生了分布式

+ 单一应用架构
    + 简单应用
+ 垂直应用架构
    + 拆分应用(MVC)
+ 分布式服务架构
    + 业务间会进行交互
+ 流动计算架构
    + 合理分配资源
    + 调度中心

![网站演变](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-architecture-roadmap.jpg)

### 解决的需求
> 错综复杂的机器之间的调度

![架构图](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-service-governance.jpg)

### 架构

![架构图](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-architecture.jpg)

![架构图](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-architecture-future.jpg)

### 用法

```xml
<!-- remote-provider.xml: -->

<!-- 和本地服务一样实现远程服务 -->
<bean id=“xxxService” class=“com.xxx.XxxServiceImpl” />
<!-- 增加暴露远程服务配置 -->
<dubbo:service interface=“com.xxx.XxxService” ref=“xxxService” />


<!-- remote-consumer.xml: -->

<!-- 增加引用远程服务配置 -->
<dubbo:reference id=“xxxService” interface=“com.xxx.XxxService” />
<!-- 和本地服务一样使用远程服务 -->
<bean id=“xxxAction” class=“com.xxx.XxxAction”>
    <property name=“xxxService” ref=“xxxService” />
</bean>
```

### 接口

#### 服务提供者


1. 定义服务接口

```java
// DemoService.java ：

package org.apache.dubbo.demo;

public interface DemoService {
    String sayHello(String name);
}

```

2. 在服务提供方实现接口

```java
// DemoServiceImpl.java ：

package org.apache.dubbo.demo.provider;

import org.apache.dubbo.demo.DemoService;

public class DemoServiceImpl implements DemoService {
    public String sayHello(String name) {
        return "Hello " + name;
    }
}
```

3. 用 Spring 配置声明暴露服务

```xml
<!-- provider.xml： -->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="hello-world-app"  />

    <!-- 使用multicast广播注册中心暴露服务地址 -->
    <dubbo:registry address="multicast://224.5.6.7:1234" />

    <!-- 用dubbo协议在20880端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20880" />

    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="org.apache.dubbo.demo.DemoService" ref="demoService" />

    <!-- 和本地bean一样实现服务 -->
    <bean id="demoService" class="org.apache.dubbo.demo.provider.DemoServiceImpl" />
</beans>
```

4. 加载 Spring 配置

```java
// Provider.java：

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Provider {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[]{"META-INF/spring/dubbo-demo-provider.xml"});
        context.start();
        System.in.read(); // 按任意键退出
    }
}
```


#### 服务消费者

1. 通过 Spring 配置引用远程服务

```xml
<!-- consumer.xml： -->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
    <dubbo:application name="consumer-of-helloworld-app"  />

    <!-- 使用multicast广播注册中心暴露发现服务地址 -->
    <dubbo:registry address="multicast://224.5.6.7:1234" />

    <!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
    <dubbo:reference id="demoService" interface="org.apache.dubbo.demo.DemoService" />
</beans>
```

2. 加载Spring配置，并调用远程服务

```java
// Consumer.java ：

import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.apache.dubbo.demo.DemoService;

public class Consumer {
    public static void main(String[] args) throws Exception {
       ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"META-INF/spring/dubbo-demo-consumer.xml"});
        context.start();
        DemoService demoService = (DemoService)context.getBean("demoService"); // 获取远程服务代理
        String hello = demoService.sayHello("world"); // 执行远程方法
        System.out.println( hello ); // 显示调用结果
    }
}
```


## 配置

### XML配置

#### provider.xml 示例

```xml
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
    <dubbo:application name="demo-provider"/>
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    <dubbo:protocol name="dubbo" port="20890"/>
    <bean id="demoService" class="org.apache.dubbo.samples.basic.impl.DemoServiceImpl"/>
    <dubbo:service interface="org.apache.dubbo.samples.basic.api.DemoService" ref="demoService"/>
</beans>
```

#### consumer.xml示例

```xml
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
    <dubbo:application name="demo-consumer"/>
    <dubbo:registry group="aaa" address="zookeeper://127.0.0.1:2181"/>
    <dubbo:reference id="demoService" check="false" interface="org.apache.dubbo.samples.basic.api.DemoService"/>
</beans>
```

#### 配置之间的关系

![关系图](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-config.jpeg)

#### 不同粒度配置的覆盖关系

![优先级关系](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-config-override.jpeg)


### 动态配置中心

#### 中心配置Jk

```xml
<dubbo:config-center address="zookeeper://127.0.0.1:2181"/>
```

#### 外部化配置

```properties
# dubbo.properties
# 将注册中心地址、元数据中心地址等配置集中管理，可以做到统一环境、减少开发侧感知。
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.registry.simplified=true

dubbo.metadata-report.address=zookeeper://127.0.0.1:2181

dubbo.protocol.name=dubbo
dubbo.protocol.port=20880

dubbo.application.qos.port=33333
```

```xml

<dubbo:config-center address="zookeeper://127.0.0.1:2181"/>

<dubbo:config-center protocol="apollo" address="127.0.0.1:2181"/>

```

### 属性配置

```properties
dubbo.application.name=foo
dubbo.application.owner=bar
dubbo.registry.address=10.20.153.10:9090
```

![优先级](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-properties-override.jpeg)

### 自动加载环境变量

```properties
# JVM
-Ddubbo.labels = "tag1=value1; tag2=value2"
# 环境变量
DUBBO_LABELS = "tag1=value1; tag2=value2"

# JVM
-Ddubbo.env.keys = "DUBBO_TAG1, DUBBO_TAG2"
# 环境变量
DUBBO_ENV_KEYS = "DUBBO_TAG1, DUBBO_TAG2"
```

### API 配置

#### 服务提供者

```java
import org.apache.dubbo.rpc.config.ApplicationConfig;
import org.apache.dubbo.rpc.config.RegistryConfig;
import org.apache.dubbo.rpc.config.ProviderConfig;
import org.apache.dubbo.rpc.config.ServiceConfig;
import com.xxx.XxxService;
import com.xxx.XxxServiceImpl;

// 服务实现
XxxService xxxService = new XxxServiceImpl();

// 当前应用配置
ApplicationConfig application = new ApplicationConfig();
application.setName("xxx");

// 连接注册中心配置
RegistryConfig registry = new RegistryConfig();
registry.setAddress("10.20.130.230:9090");
registry.setUsername("aaa");
registry.setPassword("bbb");

// 服务提供者协议配置
ProtocolConfig protocol = new ProtocolConfig();
protocol.setName("dubbo");
protocol.setPort(12345);
protocol.setThreads(200);

// 注意：ServiceConfig为重对象，内部封装了与注册中心的连接，以及开启服务端口

// 服务提供者暴露服务配置
ServiceConfig<XxxService> service = new ServiceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接，请自行缓存，否则可能造成内存和连接泄漏
service.setApplication(application);
service.setRegistry(registry); // 多个注册中心可以用setRegistries()
service.setProtocol(protocol); // 多个协议可以用setProtocols()
service.setInterface(XxxService.class);
service.setRef(xxxService);
service.setVersion("1.0.0");

// 暴露及注册服务
service.export();
```

#### 服务消费者

```java
import org.apache.dubbo.rpc.config.ApplicationConfig;
import org.apache.dubbo.rpc.config.RegistryConfig;
import org.apache.dubbo.rpc.config.ConsumerConfig;
import org.apache.dubbo.rpc.config.ReferenceConfig;
import com.xxx.XxxService;

// 当前应用配置
ApplicationConfig application = new ApplicationConfig();
application.setName("yyy");

// 连接注册中心配置
RegistryConfig registry = new RegistryConfig();
registry.setAddress("10.20.130.230:9090");
registry.setUsername("aaa");
registry.setPassword("bbb");

// 注意：ReferenceConfig为重对象，内部封装了与注册中心的连接，以及与服务提供方的连接

// 引用远程服务
ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
reference.setApplication(application);
reference.setRegistry(registry); // 多个注册中心可以用setRegistries()
reference.setInterface(XxxService.class);
reference.setVersion("1.0.0");

// 和本地bean一样使用xxxService
XxxService xxxService = reference.get(); // 注意：此代理对象内部封装了所有通讯细节，对象较重，请缓存复用

```

#### 其它设置

```java

// 方法级配置
List<MethodConfig> methods = new ArrayList<MethodConfig>();
MethodConfig method = new MethodConfig();
method.setName("createXxx");
method.setTimeout(10000);
method.setRetries(0);
methods.add(method);

// 引用远程服务
ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
...
reference.setMethods(methods); // 设置方法级配置

// 点对点直连
eferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>(); // 此实例很重，封装了与注册中心的连接以及与提供者的连接，请自行缓存，否则可能造成内存和连接泄漏
// 如果点对点直连，可以用reference.setUrl()指定目标地址，设置url后将绕过注册中心，
// 其中，协议对应provider.setProtocol()的值，端口对应provider.setPort()的值，
// 路径对应service.setPath()的值，如果未设置path，缺省path为接口名
reference.setUrl("dubbo://10.20.130.230:20880/com.xxx.XxxService");

```

### 注解配置

#### 服务提供方


1. Service注解暴露服务

```java
@Service
public class AnnotationServiceImpl implements AnnotationService {
    @Override
    public String sayHello(String name) {
        return "annotation: hello, " + name;
    }
}
```

2. 增加应用共享配置

```properties
# dubbo-provider.properties
dubbo.application.name=annotation-provider
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
```

3. 指定Spring扫描路径

```java
@Configuration
@EnableDubbo(scanBasePackages = "org.apache.dubbo.samples.simple.annotation.impl")
@PropertySource("classpath:/spring/dubbo-provider.properties")
static public class ProviderConfiguration {

}
```

#### 服务消费方

1. Reference注解引用服务

```java
@Component("annotationAction")
public class AnnotationAction {

    @Reference
    private AnnotationService annotationService;

    public String doSayHello(String name) {
        return annotationService.sayHello(name);
    }
}
```

2. 增加应用共享配置

```properties
# dubbo-consumer.properties
dubbo.application.name=annotation-consumer
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.consumer.timeout=3000
```

3. 指定Spring扫描路径

```java
@Configuration
@EnableDubbo(scanBasePackages = "org.apache.dubbo.samples.simple.annotation.action")
@PropertySource("classpath:/spring/dubbo-consumer.properties")
@ComponentScan(value = {"org.apache.dubbo.samples.simple.annotation.action"})
static public class ConsumerConfiguration {

}
```

4. 调用服务

```java
public static void main(String[] args) throws Exception {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ConsumerConfiguration.class);
    context.start();
    final AnnotationAction annotationAction = (AnnotationAction) context.getBean("annotationAction");
    String hello = annotationAction.doSayHello("world");
}
```

## 功能介绍

### 集群配置

![集群结构](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/cluster.jpeg)

### 负载均衡

#### Random LoadBalance
    + 随机，按权重设置随机概率。
    + 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。
#### RoundRobin LoadBalance
    + 轮询，按公约后的权重设置轮询比率。
    + 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。
#### LeastActive LoadBalance
    + 最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。
    + 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。
#### ConsistentHash LoadBalance
    + 一致性 Hash，相同参数的请求总是发到同一提供者。
    + 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。
    + 算法参见：http://en.wikipedia.org/wiki/Consistent_hashing
    + 缺省只对第一个参数 Hash，如果要修改，请配置 <dubbo:parameter key="hash.arguments" value="0,1" />
    + 缺省用 160 份虚拟节点，如果要修改，请配置 <dubbo:parameter key="hash.nodes" value="320" />

### 线程模型

![模型图](file:///Users/hanwenhao/Cloud/笔记/Java/Dubbo/dubbo-protocol.jpeg)

1. Dispatcher
+ all
    + 所有消息都派发到线程池
    + 包括请求，响应，连接事件，断开事件，心跳等。
+ direct
    + 所有消息都不派发到线程池，全部在 IO 线程上直接执行。
+ message
    + 只有请求响应消息派发到线程池，
    + 其它连接断开事件，心跳等消息，直接在 IO 线程上执行。
+ execution
    + 只有请求消息派发到线程池，不含响应，
    + 响应和其它连接断开事件，心跳等消息，直接在 IO 线程上执行。
+ connection
    + 在 IO 线程上，将连接断开事件放入队列，有序逐个执行，其它消息派发到线程池。

2. ThreadPool
+ fixed
    + 固定大小线程池，启动时建立线程，不关闭，一直持有。(缺省)
+ cached
    + 缓存线程池，空闲一分钟自动删除，需要时重建。
+ limited
    + 可伸缩线程池，但池中的线程数只会增长不会收缩。
    + 只增长不收缩的目的是为了避免收缩时突然来了大流量引起的性能问题。
+ eager
    + 优先创建Worker线程池。
    + 在任务数量大于corePoolSize但是小于maximumPoolSize时，优先创建Worker来处理任务。
    + 当任务数量大于maximumPoolSize时，将任务放入阻塞队列中。阻塞队列充满时抛出RejectedExecutionException。
    + (相比于cached:cached在任务数量超过maximumPoolSize时直接抛出异常而不是将任务放入阻塞队列)

### 多协议
> 可以设置不同的RPC协议

### 多注册中心
> 可以设置多个注册中心

### 服务分组与聚合

```xml
<dubbo:reference interface="com.xxx.MenuService" group="*" merger="true">
    <dubbo:method name="getMenuItems" merger="false" />
</dubbo:reference>
```

### 参数验证

```java
import java.io.Serializable;
import java.util.Date;

import javax.validation.constraints.Future;
import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Past;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;

public class ValidationParameter implements Serializable {
    private static final long serialVersionUID = 7158911668568000392L;

    @NotNull // 不允许为空
    @Size(min = 1, max = 20) // 长度或大小范围
    private String name;

    @NotNull(groups = ValidationService.Save.class) // 保存时不允许为空，更新时允许为空 ，表示不更新该字段
    @Pattern(regexp = "^\\s*\\w+(?:\\.{0,1}[\\w-]+)*@[a-zA-Z0-9]+(?:[-.][a-zA-Z0-9]+)*\\.[a-zA-Z]+\\s*$")
    private String email;

    @Min(18) // 最小值
    @Max(100) // 最大值
    private int age;

    @Past // 必须为一个过去的时间
    private Date loginDate;

    @Future // 必须为一个未来的时间
    private Date expiryDate;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Date getLoginDate() {
        return loginDate;
    }

    public void setLoginDate(Date loginDate) {
        this.loginDate = loginDate;
    }

    public Date getExpiryDate() {
        return expiryDate;
    }

    public void setExpiryDate(Date expiryDate) {
        this.expiryDate = expiryDate;
    }
}

import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;

public interface ValidationService {
    void save(@NotNull ValidationParameter parameter); // 验证参数不为空
    void delete(@Min(1) int id); // 直接对基本类型参数验证
}

import javax.validation.ConstraintViolationException;
import javax.validation.ConstraintViolationException;

import org.springframework.context.support.ClassPathXmlApplicationContext;

import org.apache.dubbo.examples.validation.api.ValidationParameter;
import org.apache.dubbo.examples.validation.api.ValidationService;
import org.apache.dubbo.rpc.RpcException;

public class ValidationConsumer {
    public static void main(String[] args) throws Exception {
        String config = ValidationConsumer.class.getPackage().getName().replace('.', '/') + "/validation-consumer.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);
        context.start();
        ValidationService validationService = (ValidationService)context.getBean("validationService");
        // Error
        try {
            parameter = new ValidationParameter();
            validationService.save(parameter);
            System.out.println("Validation ERROR");
        } catch (RpcException e) { // 抛出的是RpcException
            ConstraintViolationException ve = (ConstraintViolationException) e.getCause(); // 里面嵌了一个ConstraintViolationException
            Set<ConstraintViolation<?>> violations = ve.getConstraintViolations(); // 可以拿到一个验证错误详细信息的集合
            System.out.println(violations);
        }
    }
}
```

### 结果缓存
> 高频结果缓存

```xml
<dubbo:reference interface="com.foo.BarService">
    <dubbo:method name="findBar" cache="lru" />
</dubbo:reference>
```

### 泛化调用

```java
import org.apache.dubbo.rpc.service.GenericService;

// 引用远程服务
// 该实例很重量，里面封装了所有与注册中心及服务提供方连接，请缓存
ReferenceConfig<GenericService> reference = new ReferenceConfig<GenericService>();
// 弱类型接口名
reference.setInterface("com.xxx.XxxService");
reference.setVersion("1.0.0");
// 声明为泛化接口
reference.setGeneric(true);

// 用org.apache.dubbo.rpc.service.GenericService可以替代所有接口引用
GenericService genericService = reference.get();

// 基本类型以及Date,List,Map等不需要转换，直接调用
Object result = genericService.$invoke("sayHello", new String[] {"java.lang.String"}, new Object[] {"world"});

// 用Map表示POJO参数，如果返回值为POJO也将自动转成Map
Map<String, Object> person = new HashMap<String, Object>();
person.put("name", "xxx");
person.put("password", "yyy");
// 如果返回POJO将自动转成Map
Object result = genericService.$invoke("findPerson", new String[]
{"com.xxx.Person"}, new Object[]{person});

```

### 上下文信息

#### 服务消费方

```java
// 远程调用
xxxService.xxx();
// 本端是否为消费端，这里会返回true
boolean isConsumerSide = RpcContext.getContext().isConsumerSide();
// 获取最后一次调用的提供方IP地址
String serverIP = RpcContext.getContext().getRemoteHost();
// 获取当前服务配置信息，所有配置信息都将转换为URL的参数
String application = RpcContext.getContext().getUrl().getParameter("application");
// 注意：每发起RPC调用，上下文状态会变化
yyyService.yyy();
```

#### 服务提供方

```java
public class XxxServiceImpl implements XxxService {

    public void xxx() {
        // 本端是否为提供端，这里会返回true
        boolean isProviderSide = RpcContext.getContext().isProviderSide();
        // 获取调用方IP地址
        String clientIP = RpcContext.getContext().getRemoteHost();
        // 获取当前服务配置信息，所有配置信息都将转换为URL的参数
        String application = RpcContext.getContext().getUrl().getParameter("application");
        // 注意：每发起RPC调用，上下文状态会变化
        yyyService.yyy();
        // 此时本端变成消费端，这里会返回false
        boolean isProviderSide = RpcContext.getContext().isProviderSide();
    }
}
```

#### 异步执行

```java
public class AsyncServiceImpl implements AsyncService {
    @Override
    public CompletableFuture<String> sayHello(String name) {
        RpcContext savedContext = RpcContext.getContext();
        // 建议为supplyAsync提供自定义线程池，避免使用JDK公用线程池
        return CompletableFuture.supplyAsync(() -> {
            System.out.println(savedContext.getAttachment("consumer-key1"));
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "async response from provider.";
        });
    }
}
```

#### 参数回调

```java
package com.callback.impl;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

import com.callback.CallbackListener;
import com.callback.CallbackService;

public class CallbackServiceImpl implements CallbackService {

    private final Map<String, CallbackListener> listeners = new ConcurrentHashMap<String, CallbackListener>();

    public CallbackServiceImpl() {
        Thread t = new Thread(new Runnable() {
            public void run() {
                while(true) {
                    try {
                        for(Map.Entry<String, CallbackListener> entry : listeners.entrySet()){
                           try {
                               entry.getValue().changed(getChanged(entry.getKey()));
                           } catch (Throwable t) {
                               listeners.remove(entry.getKey());
                           }
                        }
                        Thread.sleep(5000); // 定时触发变更通知
                    } catch (Throwable t) { // 防御容错
                        t.printStackTrace();
                    }
                }
            }
        });
        t.setDaemon(true);
        t.start();
    }

    public void addListener(String key, CallbackListener listener) {
        listeners.put(key, listener);
        listener.changed(getChanged(key)); // 发送变更通知
    }

    private String getChanged(String key) {
        return "Changed: " + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date());
    }
}
```

#### 事件通知

```java
class NotifyImpl implements Notify {
    public Map<Integer, Person>    ret    = new HashMap<Integer, Person>();
    public Map<Integer, Throwable> errors = new HashMap<Integer, Throwable>();

    public void onreturn(Person msg, Integer id) {
        System.out.println("onreturn:" + msg);
        ret.put(id, msg);
    }

    public void onthrow(Throwable ex, Integer id) {
        errors.put(id, ex);
    }
}
```

#### 并发控制

```xml
<dubbo:service interface="com.foo.BarService">
    <dubbo:method name="sayHello" executes="10" />
</dubbo:service>
```

#### 连接控制

```xml
<dubbo:provider protocol="dubbo" accepts="10" />

<dubbo:reference interface="com.foo.BarService" connections="10" />
```

#### 通过 TLS 保证传输安全

#### 令牌验证

#### 路由规则

```yaml

# app1的消费者只能消费所有端口为20880的服务实例
# app2的消费者只能消费所有端口为20881的服务实例
---
scope: application
force: true
runtime: true
enabled: true
key: governance-conditionrouter-consumer
conditions:
  - application=app1 => address=*:20880
  - application=app2 => address=*:20881

# DemoService的sayHello方法只能消费所有端口为20880的服务实例
# DemoService的sayHi方法只能消费所有端口为20881的服务实例
---
scope: service
force: true
runtime: true
enabled: true
key: org.apache.dubbo.samples.governance.api.DemoService
conditions:
  - method=sayHello => address=*:20880
  - method=sayHi => address=*:20881

```

#### 访问日志

```xml
<dubbo:protocol accesslog="http://10.20.160.198/wiki/display/dubbo/foo/bar.log" />

```


#### ReferenceConfig 缓存

```java
ReferenceConfig<XxxService> reference = new ReferenceConfig<XxxService>();
reference.setInterface(XxxService.class);
reference.setVersion("1.0.0");
......
ReferenceConfigCache cache = ReferenceConfigCache.getCache();
// cache.get方法中会缓存 Reference对象，并且调用ReferenceConfig.get方法启动ReferenceConfig
XxxService xxxService = cache.get(reference);
// 注意！ Cache会持有ReferenceConfig，不要在外部再调用ReferenceConfig的destroy方法，导致Cache内的ReferenceConfig失效！
// 使用xxxService对象
xxxService.sayHello();
```
