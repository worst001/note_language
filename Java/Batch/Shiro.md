# Shiro


### 概述
+ 一种权限框架
    > 核心应用基于访问者模式


### 一些重要的类
+ IniSecurityManagerFactory
    > 管理者都由工厂构建
    + getInstance

+ SecurityManager
    > 工厂中的管理者实例

+ SecurityUtils
    + SecurityUtils.setSecurityManager
        > 管理者保存在静态内存中 基于单例模式

    + SecurityUtils.getSubject
        > 获取对象

+ Subject
    + getSubject

