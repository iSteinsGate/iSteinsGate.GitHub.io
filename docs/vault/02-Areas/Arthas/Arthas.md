
#Java/Arthas



[官网文档](https://arthas.aliyun.com/doc/)
[结构图.canvas](结构图.canvas.md)

## arthas
1. 独立使用
```
curl -O https://arthas.aliyun.com/arthas-boot.jar
java -jar arthas-boot.jar
```



## springboot集成

```xml
<dependency>  
    <groupId>com.taobao.arthas</groupId>  
    <artifactId>arthas-agent-attach</artifactId>  
    <version>${arthas.version}</version>  
    <scope>runtime</scope>  
</dependency>
```



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
  tunnel-server: ws://10.168.1.108:7777/ws
```




## arthas-tunnle

```

手动连接

```shell
java -jar arthas-boot.jar --target-ip 10.168.1.158 --tunnel-server 'ws://10.168.1.158:7777/ws' --app-name nghis-biz
```

后台启动
```shell
nohup java -jar arthas-tunnel-server-3.6.7-fatjar.jar >start.log 2>&1 &

# 指定端口
nohup java -jar -Dserver.port=22200 arthas-tunnel-server-3.6.7-fatjar.jar > start.log 2>&1 &
```





如果要开启页面
-Darthas.enable-detail-pages=true 
```
nohup java -jar -Dserver.port=22200 -Darthas.enable-detail-pages=true arthas-tunnel-server-3.6.7-fatjar.jar > start.log 2>&1 &

```


http://10.168.1.102:22200/apps.html
