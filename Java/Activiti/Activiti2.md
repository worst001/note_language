# [Activiti]() 整理

-----


## 结构

### Connection
+ SequenceFlow
+ Association

### Event
+ StartEvent
+ TimerStartEvent
+ MessageStartEvent
+ ErrorStartEvent
+ EndEvent
+ ErrorEndEvent
+ TerminateEndEvent

### Task
+ UserTask
+ ScriptTask
+ ServiceTask
+ MailTask
+ ManualTask
+ ReceiveTask
+ BusinessRuleTask
+ CallActivity

### Container
+ EventSubProcess
+ SubProcess
+ Pool
+ Lane

### Gateway
+ ParallelGateway
并行网关(并发、异步)
+ ExclusiveGateway
排他网关
+ InclusiveGateway
包容 并行和排他
+ EventGateway
条件触发

### Boundary event
边界事件(顾名思义该事件若触发则流程结束)
+ TimerBoundaryEvent
+ ErrorBoundaryEvent
+ MessageBoundaryEvent
+ SignalBoundaryEvent

### Intermediate event
等待被触发 看是否满足触发条件
+ TimerCatchingEvent
+ SignalCatchingEvent
+ MessageCatchingEvent
+ SignalThrowingEvent
+ NoneThrowingEvent

### Artifacts
+ Annotation

### Alfresco
+ AlfrescoStartEvent
+ AlfrescoUserTask
+ AlfrescoScriptTask
+ AlfrescoMailTask


## 配置

### bean

+ databaseShemaUpdate
    + false 生产环境
    + true 开发环境
    + create_drop 测试时使用
    + drop-create 启动时删除旧表

### group

+ candidate group
    bpmn 图中设置(xml 配置更新)
    对应数据库中的group信息

### process

+ processRuntime
    + [自动部署](https://www.bilibili.com/video/BV1H54y167gf?p=96)
+ startProcess
    + [启动流程](https://www.bilibili.com/video/BV1H54y167gf?p=101)
+ 流程实例为单例
+ 所有hook都服务于这个单例

### taskRuntime

+ 定位任务
