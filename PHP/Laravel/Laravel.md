# Laravel

## 配置

1. Laravel 创建appkey
`php artisan key:generate`

2. 前后端对接

+ 软连接是一种桥接思想

+ 前后端整合的时候需要将前端的静态文本统一部署在public路径下

+ 可将前端编译后的dist包软链到public路径下 

```sh
<!-- 假设前后端项目在同一级路径 -->
cd 后端项目/public
ln -s `git rev-parse --show-toplevel`/../前端项目/dist dist
```
因为是不同项目的软链，修改 `.gitignore` ，使git不追踪dist。

+ 根据项目路径结构在public路径下部署自身的软连接
```sh
cd 后端项目/public
ln -s dist/favicon.ico favicon.ico
ln -s dist/js js
...
```

+ 将index.html转为模板入口统一放在dist中
```sh
cd 前端项目/dist

<!-- 可写到build.sh里方便一键部署 -->
cp index.html index.blade.php

cd 后端项目/resources/views
ln -sf ../../public/dist/index.blade.php index.blade.php
```

+ 部署流程
    + 后端分为develop、test、master三个分支
    + 前端分为develop、master两个分支
    + 前端 `npm run build` `./build.sh`
    + 建立前后端之间的软连接
    + 建立后端内部的软连接


## 一些踩过的坑
1. composer
+ 全局配置文件的name中不能以.开头
+ 抛出异常后可以通过 `composer install -vvv` 查看日志

2. Crontab
+ [检测Mac中的crontab是否开启](https://www.cnblogs.com/pcy0/p/how-to-enable-crontab-on-osx.html)

3. Lavavel 中的Crontab
+ 运行 `php artisan schedule:run` 查看提示的绝对路径
+ 如果不行导出运行的文件
```crontab
* * * * *  /usr/local/bin/php /Users/hanwenhao/MyProjects/easyDivination/artisan schedule:run >> /Users/hanwenhao/Downloads/cron.txt
```
+ 按照提示修改crontab中的php绝对路径 `/usr/local/Cellar/php/7.4.4/bin/php`
+ 修改成如下形式：
```crontab
* * * * *  /usr/local/Cellar/php/7.4.4/bin/php /Users/hanwenhao/MyProjects/easyDivination/artisan schedule:run > /dev/null 2>&1
```
+ 正式环境的坑
Linux环境的php为了安全会关闭php的proc调用组件 要开启它
这样一来Linux也可以使用Composer了 但目前我git中仍然加入了vendor


