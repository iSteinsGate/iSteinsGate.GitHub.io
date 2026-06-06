---
type: entity
category: Java
created: 2026-06-06
---

# Arthas
[官方文档](https://arthas.aliyun.com/doc/)

# 单独使用

[📎arthas-boot.jar](https://guigug.yuque.com/attachments/yuque/0/2023/jar/23001819/1678687097673-ae6dc27d-427f-41e0-b726-c9493a9a3fc0.jar)

curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar

# 配合arthas-tunnel-server

[📎arthas-tunnel-server-3.6.7-fatjar.jar](https://guigug.yuque.com/attachments/yuque/0/2023/jar/23001819/1678687173518-bca23fd3-0212-4b57-a151-5994bf6cc941.jar)

下载地址：[https://github.com/alibaba/arthas/releases](https://github.com/alibaba/arthas/releases)

Arthas tunnel server 是一个fat jar引用，直接启动

java -jar arthas-tunnel-server-3.6.7-fatjar.jar 

默认tunnel-serverweb端口8080，arthas agent连接端口7777

启动之后，可以访问 [http://10.108.1.102:8080](http://10.168.1.102:22200/) ，再通过agentId连接到已注册的 arthas agent 上。

![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678341624906-0fc72cb7-07c2-4d61-87a5-9511df677eb5.png)

如果开启app列表列表需要配置参数：-Darthas.enable-detail-pages=true

java -jar -Dserver.port=22200 -Darthas.enable-detail-pages=true arthas-tunnel-server-3.6.7-fatjar.jar 

-- nohup后台启动
nohup java -jar -Dserver.port=22200 -Darthas.enable-detail-pages=true arthas-tunnel-server-3.6.7-fatjar.jar > start.log 2>&1 &

启动arthas连接tunnel-server

-   指定agent-id　(这种方式只能启动一个nghis-biz)

java -jar arthas-boot.jar --tunnel-server 'ws://10.168.1.102:7777/ws' 
--agent-id nghis-biz

-   指定app-name启动 　(这种方式agentId会自动生成)

java -jar arthas-boot.jar --tunnel-server 'ws://10.168.1.102:7777/ws' 
--app-name nghis-biz

![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678332340739-48246c08-5ffd-464f-8923-cdce133e4f00.png)

![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678347712051-2470b380-0afa-4094-8a27-111f70cdf7da.png)
# SpringBoot项目集成
引入依赖

```xml
<dependency>  
    <groupId>com.taobao.arthas</groupId>  
    <artifactId>arthas-agent-attach</artifactId>  
    <version>3.6.7</version>  
    <scope>runtime</scope>  
</dependency>
```

增加Spring配置

```yml
arthas:
  # telnetPort、httpPort为 -1 ，则不listen telnet端口，为 0 ，则随机telnet端口
  # 如果是防止一个机器上启动多个 arthas端口冲突。可以配置为随机端口，或者配置为 -1，并且通过tunnel server来使用arthas。
  # ~/logs/arthas/arthas.log (用户目录下面)里可以找到具体端口日志
  telnetPort: -1
  httpPort: -1
  # 127.0.0.1只能本地访问，0.0.0.0则可网络访问，但是存在安全问题
  # ip: 127.0.0.1
  appName: ${spring.application.name}
  # 默认情况下，会生成随机ID，如果 arthas agent配置了 appName，则生成的agentId会带上appName的前缀。
  # agent-id: hsehdfsfghhwertyfad
  # tunnel-server地址
  tunnel-server: ws://10.168.1.102:7777/ws
```

结构图

![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678333094797-9240ef43-f458-4e70-a0e9-b113069ba59c.png)

  

# 常用命令

## dashboard

仪表板可以命令查看当前系统的实时数据面板。可以查看到 CPU、内存、GC、运行环境等信息。

![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678343588695-60877059-d62d-4588-9d67-a8f7e934d997.png)

输入 q 或者 Ctrl+C 可以退出仪表板命令。

## SC(查找JVM 中已加载的类 sc/sm)

sc -d *IpmOrderWillService![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678343440698-91da1cba-2237-495c-bc6f-cf33bed8f94f.png)

查询类全名

sc *IpmOrderWillService*  
![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678343533753-37fa49e5-f041-4744-988e-0df96f1e7b47.png)

## jad(反编译)

jad 类名　方法名

-   反编译类IpmDiagnoseServiceImpl

jad com.guigug.clinical.ipm.order.service.impl.IpmDiagnoseServiceImpl

-   反编译类IpmDiagnoseServiceImpl 的save方法

jad com.guigug.clinical.ipm.order.service.impl.IpmDiagnoseServiceImpl save

![](https://cdn.nlark.com/yuque/0/2023/png/23001819/1678344331719-81730669-ce7e-4eb1-b46c-a1ca58860c9b.png)

## watch

能观察到的范围为：返回值、抛出异常、入参，通过编写 OGNL 表达式进行对应变量的查看。

-   参数说明

参数名称

参数说明

_class-pattern_

类名表达式匹配

_method-pattern_

函数名表达式匹配

_express_

观察表达式，默认值：{params, target, returnObj}

_condition-express_

条件表达式

[b]

在**函数调用之前**观察

[e]

在**函数异常之后**观察

[s]

在**函数返回之后**观察

[f]

在**函数结束之后**(正常返回和异常返回)观察

[E]

开启正则表达式匹配，默认为通配符匹配

[x:]

指定输出结果的属性遍历深度，默认为 1，最大值是 4

这里重点要说明的是观察表达式，观察表达式的构成主要由 ognl 表达式组成，所以你可以这样写"{params,returnObj}"，只要是一个合法的 ognl 表达式，都能被正常支持。

**特别说明**：

-   watch 命令定义了 4 个观察事件点，即 -b 函数调用前，-e 函数异常后，-s 函数返回后，-f 函数结束后
-   4 个观察事件点 -b、-e、-s 默认关闭，-f 默认打开，当指定观察点被打开后，在相应事件点会对观察表达式进行求值并输出
-   这里要注意函数入参和函数出参的区别，有可能在中间被修改导致前后不一致，除了 -b 事件点 params 代表函数入参外，其余事件都代表函数出参
-   当使用 -b 时，由于观察事件点是在函数调用前，此时返回值或异常均不存在
-   在 watch 命令的结果里，会打印出location信息。location有三种可能值：AtEnter，AtExit，AtExceptionExit。对应函数入口，函数正常 return，函数抛出异常。

语法：watch 类名 方法名 {params,target,returnobj} -x 深度

-   观察函数调用返回时的参数、this 对象和返回值. 观察表达式默认值: {params,target,returnobj}
-   观察函数调用入口的参数和返回值 -b:表示执行前

fwatch com....IpmDiagnoseServiceImpl save {params,returnobj} -x 2 -b

-   同时观察函数调用前和函数返回后 -s:表示函数返回后　　-n 2，表示只执行两次

watch com....IpmDiagnoseServiceImpl save {params,target,returnobj} -x 2 -b -s -n 2

-   条件表达式

watch com....IpmDiagnoseServiceImpl save {params[0],target,returnobj} -x 2

-   观察异常信息 -e:表示抛出异常才触发　　表示异常信息的变量是throwExp

watch com....IpmDiagnoseServiceImpl save {params[0],throwExp} -x 2 -e

-   观察当前对象中属性

如果想查看函数运行前后，当前对象中的属性，可以使用target关键字，代表当前对象

watch com....IpmDiagnoseServiceImpl save 'target'

## trace

方法内部调用路径，并输出方法路径上的每个节点上耗时

语法：trace 类名　方法名

-   跟踪IpmDiagnoseServiceImpl save方法

trace com....IpmDiagnoseServiceImpl save 

-   根据调用耗时过滤 只会展示耗时大于 10ms 的调用路径

trace com....IpmDiagnoseServiceImpl save '#codt > 10'

-   次数限制 如果方法调用的次数很多，那么可以用-n参数指定捕捉结果的次数

trace com....IpmDiagnoseServiceImpl save -n 1

-   trace多个类或者多个函数

trace -E  com.guigug.ClassA|com.guigug.ClassB saveA|saveB|saveC

-   排除掉指定的类 使用 --exclude-class-pattern 参数可以排除掉指定的类，比如

trace com....IpmDiagnoseServiceImpl * --exclude-class-pattern com.demo.TestFilter

# 所有命令列表

 [#](https://arthas.aliyun.com/doc/commands.html#jvm-%E7%9B%B8%E5%85%B3)jvm 相关

-   [dashboard](https://arthas.aliyun.com/doc/dashboard.html) - 当前系统的实时数据面板
-   [getstatic](https://arthas.aliyun.com/doc/getstatic.html) - 查看类的静态属性
-   [heapdump](https://arthas.aliyun.com/doc/heapdump.html) - dump java heap, 类似 jmap 命令的 heap dump 功能
-   [jvm](https://arthas.aliyun.com/doc/jvm.html) - 查看当前 JVM 的信息
-   [logger](https://arthas.aliyun.com/doc/logger.html) - 查看和修改 logger
-   [mbean](https://arthas.aliyun.com/doc/mbean.html) - 查看 Mbean 的信息
-   [memory](https://arthas.aliyun.com/doc/memory.html) - 查看 JVM 的内存信息
-   [ognl](https://arthas.aliyun.com/doc/ognl.html) - 执行 ognl 表达式
-   [perfcounter](https://arthas.aliyun.com/doc/perfcounter.html) - 查看当前 JVM 的 Perf Counter 信息
-   [sysenv](https://arthas.aliyun.com/doc/sysenv.html) - 查看 JVM 的环境变量
-   [sysprop](https://arthas.aliyun.com/doc/sysprop.html) - 查看和修改 JVM 的系统属性
-   [thread](https://arthas.aliyun.com/doc/thread.html) - 查看当前 JVM 的线程堆栈信息
-   [vmoption](https://arthas.aliyun.com/doc/vmoption.html) - 查看和修改 JVM 里诊断相关的 option
-   [vmtool](https://arthas.aliyun.com/doc/vmtool.html) - 从 jvm 里查询对象，执行 forceGc

 [#](https://arthas.aliyun.com/doc/commands.html#class-classloader-%E7%9B%B8%E5%85%B3)class/classloader 相关

-   [classloader](https://arthas.aliyun.com/doc/classloader.html) - 查看 classloader 的继承树，urls，类加载信息，使用 classloader 去 getResource
-   [dump](https://arthas.aliyun.com/doc/dump.html) - dump 已加载类的 byte code 到特定目录
-   [jad](https://arthas.aliyun.com/doc/jad.html) - 反编译指定已加载类的源码
-   [mc](https://arthas.aliyun.com/doc/mc.html) - 内存编译器，内存编译.java文件为.class文件
-   [redefine](https://arthas.aliyun.com/doc/redefine.html) - 加载外部的.class文件，redefine 到 JVM 里
-   [retransform](https://arthas.aliyun.com/doc/retransform.html) - 加载外部的.class文件，retransform 到 JVM 里
-   [sc](https://arthas.aliyun.com/doc/sc.html) - 查看 JVM 已加载的类信息
-   [sm](https://arthas.aliyun.com/doc/sm.html) - 查看已加载类的方法信息

 [#](https://arthas.aliyun.com/doc/commands.html#monitor-watch-trace-%E7%9B%B8%E5%85%B3)monitor/watch/trace 相关

**注意**

请注意，这些命令，都通过字节码增强技术来实现的，会在指定类的方法中插入一些切面来实现数据统计和观测，因此在线上、预发使用时，请尽量明确需要观测的类、方法以及条件，诊断结束要执行 stop 或将增强过的类执行 reset 命令。

-   [monitor](https://arthas.aliyun.com/doc/monitor.html) - 方法执行监控
-   [stack](https://arthas.aliyun.com/doc/stack.html) - 输出当前方法被调用的调用路径
-   [trace](https://arthas.aliyun.com/doc/trace.html) - 方法内部调用路径，并输出方法路径上的每个节点上耗时
-   [tt](https://arthas.aliyun.com/doc/tt.html) - 方法执行数据的时空隧道，记录下指定方法每次调用的入参和返回信息，并能对这些不同的时间下调用进行观测
-   [watch](https://arthas.aliyun.com/doc/watch.html) - 方法执行数据观测

 [#](https://arthas.aliyun.com/doc/commands.html#profiler-%E7%81%AB%E7%84%B0%E5%9B%BE)profiler/火焰图

-   [profiler](https://arthas.aliyun.com/doc/profiler.html) - 使用[async-profiler在新窗口打开](https://github.com/jvm-profiling-tools/async-profiler)对应用采样，生成火焰图
-   [jfr](https://arthas.aliyun.com/doc/jfr.html) - 动态开启关闭 JFR 记录

 [#](https://arthas.aliyun.com/doc/commands.html#%E9%89%B4%E6%9D%83)鉴权

-   [auth](https://arthas.aliyun.com/doc/auth.html) - 鉴权

 [#](https://arthas.aliyun.com/doc/commands.html#options)options

-   [options](https://arthas.aliyun.com/doc/options.html) - 查看或设置 Arthas 全局开关

 [#](https://arthas.aliyun.com/doc/commands.html#%E7%AE%A1%E9%81%93)管道

Arthas 支持使用管道对上述命令的结果进行进一步的处理，如sm java.lang.String * | grep 'index'

-   [grep](https://arthas.aliyun.com/doc/grep.html) - 搜索满足条件的结果
-   plaintext - 将命令的结果去除 ANSI 颜色
-   wc - 按行统计输出结果

 [#](https://arthas.aliyun.com/doc/commands.html#%E5%90%8E%E5%8F%B0%E5%BC%82%E6%AD%A5%E4%BB%BB%E5%8A%A1)后台异步任务

当线上出现偶发的问题，比如需要 watch 某个条件，而这个条件一天可能才会出现一次时，异步后台任务就派上用场了，详情请参考[这里](https://arthas.aliyun.com/doc/async.html)

-   使用 > 将结果重写向到日志文件，使用 & 指定命令是后台运行，session 断开不影响任务执行（生命周期默认为 1 天）
-   jobs - 列出所有 job
-   kill - 强制终止任务
-   fg - 将暂停的任务拉到前台执行
-   bg - 将暂停的任务放到后台执行

 [#](https://arthas.aliyun.com/doc/commands.html#%E5%9F%BA%E7%A1%80%E5%91%BD%E4%BB%A4)基础命令

-   [base64](https://arthas.aliyun.com/doc/base64.html) - base64 编码转换，和 linux 里的 base64 命令类似
-   [cat](https://arthas.aliyun.com/doc/cat.html) - 打印文件内容，和 linux 里的 cat 命令类似
-   [cls](https://arthas.aliyun.com/doc/cls.html) - 清空当前屏幕区域
-   [echo](https://arthas.aliyun.com/doc/echo.html) - 打印参数，和 linux 里的 echo 命令类似
-   [grep](https://arthas.aliyun.com/doc/grep.html) - 匹配查找，和 linux 里的 grep 命令类似
-   [help](https://arthas.aliyun.com/doc/help.html) - 查看命令帮助信息
-   [history](https://arthas.aliyun.com/doc/history.html) - 打印命令历史
-   [keymap](https://arthas.aliyun.com/doc/keymap.html) - Arthas 快捷键列表及自定义快捷键
-   [pwd](https://arthas.aliyun.com/doc/pwd.html) - 返回当前的工作目录，和 linux 命令类似
-   [quit](https://arthas.aliyun.com/doc/quit.html) - 退出当前 Arthas 客户端，其他 Arthas 客户端不受影响
-   [reset](https://arthas.aliyun.com/doc/reset.html) - 重置增强类，将被 Arthas 增强过的类全部还原，Arthas 服务端关闭时会重置所有增强过的类
-   [session](https://arthas.aliyun.com/doc/session.html) - 查看当前会话的信息
-   [stop](https://arthas.aliyun.com/doc/stop.html) - 关闭 Arthas 服务端，所有 Arthas 客户端全部退出
-   [tee](https://arthas.aliyun.com/doc/tee.html) - 复制标准输入到标准输出和指定的文件，和 linux 里的 tee 命令类似
-   [version](https://arthas.aliyun.com/doc/version.html) - 输出当前目标 Java 进程所加载的 Arthas 版本号

---

## 相关文章

- [[Java工具类汇总]]

---

## 相关页面

- [[Java工具类汇总]]
