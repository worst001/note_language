# Activiti

## Maven 包
配置依赖包
```xml
<!-- pom.xml -->

<properties>
    <activiti.version>5.23.0</activiti.version>
    <mysql.version>8.0.31</mysql.version>
</properties>

<dependencies>
    <!-- activiti 引擎 -->
    <dependency>
        <groupId>org.activiti</groupId>
        <artifactId>activiti-engine</artifactId>
        <version>${activiti.version}</version>
    </dependency>
    <!-- 整合 spring 与 activiti -->
    <dependency>
        <groupId>org.activiti</groupId>
        <artifactId>activiti-spring</artifactId>
        <version>${activiti.version}</version>
    </dependency>
    <!-- 将 bpmn model 对象类 -->
    <dependency>
        <groupId>org.activiti</groupId>
        <artifactId>activiti-bpmn-model</artifactId>
        <version>${activiti.version}</version>
    </dependency>
    <!-- bpmn 转化为 model 工具类 -->
    <dependency>
        <groupId>org.activiti</groupId>
        <artifactId>activiti-bpmn-converter</artifactId>
        <version>${activiti.version}</version>
    </dependency>
    <!-- 连接 mysql -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
    </dependency>
    <!-- mybatis 插件 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.0</version>
    </dependency>
    <!-- 日志插件 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.25</version>
        <scope>test</scope>
    </dependency>
    <!-- 单元测试插件 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

配置log4j日志组件
```properties
<!-- log4j.properties -->

log4j.rootLogger=INFO,Console
#Console
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=%-5p - %m%n
```

## 数据库配置
配置activiti库地址
```xml
<!-- activiti.cfg.xml -->

<bean id="processEngineConfiguration" class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/activiti"/>
    <property name="jdbcDriver" value="com.mysql.cj.jdbc.Driver"/>
    <property name="jdbcUsername" value="root" />
    <property name="jdbcPassword" value="" />
    <!--数据库更新模式，会自动创建所需表-->
    <property name="databaseSchemaUpdate" value="true" />
</bean>
```

## 初始化
初始化activiti数据库
```java
@Test
public void testInitActiviti(){
    //获得流程引擎，自动读取activiti.cfg.xml中的配置
    ProcessEngine engine = ProcessEngines.getDefaultProcessEngine();
}
```

初始化bpmn
将流程图装载入库
```java
@Test
public void testDeployment() {
    // 1. 创建ProcessEngine
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
    // 2. 得到RepositoryService实例
    RepositoryService repositoryService = processEngine.getRepositoryService();
    // 3. 使用RepositoryService进行部署
    Deployment deployment = repositoryService.createDeployment()
        .addClasspathResource("bpmn/helloBpmn.bpmn20.xml") // 添加bpmn资源
        .addClasspathResource("bpmn/helloBpmn.png")        // 添加png资源
        .name("请假申请流程")
        .deploy();
    // 4. 输出部署信息
    System.out.println("流程部署id: " + deployment.getId());
    System.out.println("流程部署名称: " + deployment.getName());
}
```

## 流程案例
启动(激活)某个流程
```java
@Test
public void testStartProcess() {
    // 1. 创建ProcessEngine
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
    // 2. 获取RuntimeService
    RuntimeService runtimeService = processEngine.getRuntimeService();
    // 3. 根据流程定义Id启动流程
    ProcessInstance processInstance = runtimeService
        .startProcessInstanceByKey("helloBpmn");
    // 4. 输出流程信息
    System.out.println("流程定义id: " + processInstance.getProcessDefinitionId());
    System.out.println("流程实例id: " + processInstance.getId());
    System.out.println("当前活动id: " + processInstance.getActivityId());
}

```

基于某个激活的流程查询某个负责人负责的任务
```java
@Test
public void testFindPersonalTaskList() {
    // 1. 任务负责人
    String assignee = "worker";
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
    // 2. 创建TaskService
    TaskService taskService = processEngine.getTaskService();
    // 3. 根据流程key 和 任务负责人 查询任务
    List<Task> list = taskService.createTaskQuery()
        .processDefinitionKey("helloBpmn") // 流程key
        .taskAssignee(assignee)            // 只查询该负责人的任务
        .list();

    // 4. 输出任务信息
    for (Task task : list) {
        System.out.println("流程实例id:" + task.getProcessInstanceId());
        System.out.println("任务id:" + task.getId());
        System.out.println("任务负责人:" + task.getAssignee());
        System.out.println("任务名称:" + task.getName());
    }
}

```

完成某个阶段的任务查看结果
```java
@Test
public void testCompleteTask() {
    // 1. 获取引擎
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
    // 2. 创建TaskService
    TaskService taskService = processEngine.getTaskService();
    // 3. 根据流程key 和 任务负责人 查询任务 (只会返回单个任务对象 多个对象会报错)
    Task task = taskService.createTaskQuery()
        .processDefinitionKey("helloBpmn") // 流程key
        .taskAssignee("worker")            // 只查询该负责人的任务
        .singleResult();

    // 4. 完成任务
    taskService.complete(task.getId());
}

```

查看当前正在跑的流程
```java
@Test
public void testQueryProcessInstance() {
    // 1. 流程定义的key
    String        processDefinitionKey = "helloBpmn";
    ProcessEngine processEngine        = ProcessEngines.getDefaultProcessEngine();

    // 2. 获取runtimeService
    RuntimeService runtimeService = processEngine.getRuntimeService();
    List<ProcessInstance> list = runtimeService
        .createProcessInstanceQuery()               // 创建查询
        .processDefinitionKey(processDefinitionKey) // 用来查询的key
        .list();                                    // 实例列表

    // 3. 输出实例信息
    for (ProcessInstance processInstance : list) {
        System.out.println("流程定义id: " + processInstance.getProcessDefinitionId());
        System.out.println("流程实例id: " + processInstance.getId());
        System.out.println("是否完成: " + processInstance.isEnded());
        System.out.println("是否暂停: " + processInstance.isSuspended());
        System.out.println("当前活动id: " + processInstance.getActivityId());
        System.out.println("业务关键字: " + processInstance.getBusinessKey());
    }
}

```

删除部署下的所有流程
```java
@Test
public void testDeleteDeployment() {
    // 1. 流程部署id
    String deploymentId = "1";
    ProcessEngine processEngine        = ProcessEngines.getDefaultProcessEngine();
    // 2. 得到RepositoryService实例
    RepositoryService repositoryService = processEngine.getRepositoryService();
    // 3. 删除流程定义
    repositoryService.deleteDeployment(deploymentId);

    // 强制删除
//        repositoryService.deleteDeployment(deploymentId, true);
}

```


剩余详情见 helloActiviti 项目

## 挂起与激活

以自定义businessKey启动一个流程
```java
@Test
public void testAddBusinessKey() {
    // 1. 获取流程引擎
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
    // 2. 获取RuntimeService
    RuntimeService runtimeService = processEngine.getRuntimeService();
    // 3. 启动流程的过程中添加 businessKey
    ProcessInstance instance = runtimeService.startProcessInstanceByKey("helloBpmn", "businessKey1001");
    // 4. 输出
    System.out.println("businessKey==" + instance.getBusinessKey());
}

```

挂起/(激活)某个流程实例
```java
@Test
public void testSuspendSingleProcessInstance() {
    // 1. 获取流程引擎
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
    // 2. 获取runtimeService
    RuntimeService runtimeService = processEngine.getRuntimeService();
    // 3. 获取查询器
    ProcessInstance processInstance = runtimeService.createProcessInstanceQuery()
        .processInstanceBusinessKey("businessKey1001")
        .singleResult();

    // 4. 挂起状态
    boolean suspended = processInstance.isSuspended();
    // 5. 获取流程实例id
    String processInstanceId = processInstance.getId();
    // 6. 如果是挂起状态就改为激活状态
    if (suspended) {
        // 设为激活
        runtimeService.activateProcessInstanceById(processInstanceId);
    } else {
        // 设为挂起
        runtimeService.suspendProcessInstanceById(processInstanceId);
    }
    System.out.println("流程业务id:" + processInstanceId + ", 激活状态:" + processInstance.isSuspended());
}

```


## 流程变量
用流程变量启动流程

## 网关
流程变量控制网关

## 任务分配
