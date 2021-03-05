# 一次莫名奇妙的线上服务500排查经历


#### 1. 背景：
注册中心：spring eureka
网关：spring gateway
问题服务模块：gbs-cms
部署模式：docker swarm集群，3个manage节点+3个worker节点
问题描述：请求线上cms模块接口有时报错500有时正常，cms模块无报错日志，gateway模块报错cms拒绝连接
报错信息：  
```
Caused by: io.netty.channel.unix.Errors$NativeConnectException: syscall:getsockopt(..) failed:         Connection refused：gbs-cms/10.0.3.121
```
日志截图：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602577986204-ab4e76ae-13c3-44cb-883f-e8ef706b4ab5.png#align=left&display=inline&height=173&margin=%5Bobject%20Object%5D&name=image.png&originHeight=345&originWidth=1802&size=84800&status=done&style=none&width=901)
#### 2. 解决过程：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602578111481-d562314a-f654-4e0e-8859-cb62a8fe4d86.png#align=left&display=inline&height=105&margin=%5Bobject%20Object%5D&name=image.png&originHeight=210&originWidth=1750&size=28273&status=done&style=none&width=875)

##### 2.1 
docker service ps检查cms服务，发现之前cms挂过: cms的service应该起2个task，实际上运行了3个，docker service ps查看实例状态有4个：一个shutdown,一个failure,挂了之后又起了两个success的，运行的这3个就是这一个failure和两个success的；于是重新发布了cms,这时候cms正常起来了两个success的实例，但是问题依然未解决,还是同样的报错:Connection refused：gbs-cms/10.0.3.121  

##### 2.2
于是怀疑是gateway或者eureka缓存的问题，重新发布了gateway和eureka,甚至对gateway进行扩容缩容，问题依然存在，报错还是同样存在，一点儿没变。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602577649387-518bc53a-58ea-4971-8f41-03fb274f5f2b.png#align=left&display=inline&height=60&margin=%5Bobject%20Object%5D&name=image.png&originHeight=119&originWidth=922&size=92583&status=done&style=none&width=461)

##### 2.3
这时注意到10.0.3.121这个ip上，我查看了6台服务器的所有的容器的虚拟ip根本找不到它，肯定是某个地方的缓存导致这个ip还存在，于是我又给每台服务器进行docker system prune,清理docker 无用的容器、镜像、网络等缓存，挨个重启docker,可是问题依然没有解决。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602813460798-c4f13079-cfc5-420d-a33a-6952e33e41a7.png#align=left&display=inline&height=60&margin=%5Bobject%20Object%5D&name=image.png&originHeight=120&originWidth=728&size=123766&status=done&style=none&width=364)

##### 2.4
接下来进入容器中去ping其他服务的名字，发现只有cms有两个ip,其他只有一个ip，查了一下资料，这个ip实际上是swarm service的一个虚拟ip,service之间通过这个ip来找到对应此service下的task容器实例；而且通过docker inspect 服务名，发现service的ip和task容器的ip是连号的，如：gbs-cms的ip是10.0.3.100，而它下面两个容器实例的ip是10.0.3.101和10.0.3.102，综上确定了10.0.3.121就是之前缓存下来的gbs-cms的ip，但是其下已无容器实例，新的gbs-cms是10.0.3.100，所以访问到121时就报错500，访问到100时就正常。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602813809933-775d2afb-071a-427b-9534-29fd396d579a.png#align=left&display=inline&height=184&margin=%5Bobject%20Object%5D&name=image.png&originHeight=367&originWidth=641&size=325242&status=done&style=none&width=320.5)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602813729150-cbc1b9ef-6ec9-493e-8ef7-5cef67a83e2a.png#align=left&display=inline&height=129&margin=%5Bobject%20Object%5D&name=image.png&originHeight=257&originWidth=618&size=154365&status=done&style=none&width=309)

##### 2.5
目前推测就是之前那个failure的cms的task容器实例未清除彻底，进而导致原cms在docker内部的dns缓存未清除掉。于是想办法清除docker 内部的dns缓存，谷歌了全网也没找到什么好办法，无非就是发布时自己定义网络或者指定dns服务器，但是这么多服务我不可能因为这一个导致全部重发。于是我选择曲线救国，改了后端代码和部署脚本，给这个服务换一个hostname，重新发布，果然不报错了，新的服务名时gbs-cms-new,这样网关不会去找gbs-cms了，但是gbs-cms 的dns缓存却留在了docker中，目前我还没找到办法清除掉。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1602813748363-d414db12-7d48-43ba-8252-a27b5d6373d4.png#align=left&display=inline&height=222&margin=%5Bobject%20Object%5D&name=image.png&originHeight=443&originWidth=901&size=406911&status=done&style=none&width=450.5)


#### 3. 总结：  
疑惑(2020/10/16)：到底该如何清除docker容器内部的dns缓存呢，接下来要研究一下docker内部的dns server工作机制了。  

---
后续(2021/02/09)：执行docker system prune,将docker 系统的网络、悬空镜像、容器等彻底清理，再重启docker则缓存清除。

