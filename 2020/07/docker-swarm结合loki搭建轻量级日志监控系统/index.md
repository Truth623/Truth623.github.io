# docker swarm结合loki搭建轻量级日志监控系统


### 一、安装使用简介

#### 1 docker安装loki

```
wget [https://raw.githubusercontent.com/grafana/loki/v1.5.0/cmd/loki/loki-local-config.yaml](https://raw.githubusercontent.com/grafana/loki/v1.5.0/cmd/loki/loki-local-config.yaml) -O loki-config.yaml
docker run -v $(pwd):/mnt/config -p 3100:3100 -d grafana/loki:1.5.0 -config.file=/mnt/config/loki-config.yaml
```


#### 2 docker安装grafana


```
docker pull grafana/grafana:master
docker run -d -p 3000:3000 grafana/grafana:master
```


#### 3 docker安装promtail（可选）

需要收集nginx或者其他单体容器日志时可安装，修改配置文件来收集日志；而dockers swarm可在docker中安装loki插件，暂不需要安装。


```
wget [https://raw.githubusercontent.com/grafana/loki/v1.5.0/cmd/promtail/promtail-docker-config.yaml](https://raw.githubusercontent.com/grafana/loki/v1.5.0/cmd/promtail/promtail-docker-config.yaml) -O promtail-config.yaml

docker run -v $(pwd):/mnt/config -v /var/log:/var/log grafana/promtail:1.5.0 -config.file=/mnt/config/promtail-config.yaml
```


#### 4 添加端口


```
firewall-cmd --zone=public --list-ports
firewall-cmd --add-port=3100/tcp --permanent
firewall-cmd --add-port=3000/tcp --permanent
firewall-cmd --reload
```

---

#### 5 swarm管理节点上,安装loki-docker-driver插件


```
docker plugin install grafana/loki-docker-driver:latest --alias loki
```


#### 6 集群stack部署时的yaml配置文件修改，添加logging配置参数，loki-url指定loki服务地址


```
version: "3.7"
services:
  logger:
    image: grafana/grafana
    logging:
      driver: loki
      options:
        loki-url: "http://10.5.76.54:3100/loki/api/v1/push"
```



---



#### 7 浏览器访问测试


[http://10.5.76.54:3000](http://10.5.76.54:3000)


##### 7.1 安装完成后，访问节点的 3000 端口访问 grafana，默认情况下使用(admin:admin)访问 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1594975960911-af45ec82-b57c-481a-b2df-f75a34b5569d.png#align=left&display=inline&height=426&margin=%5Bobject%20Object%5D&name=image.png&originHeight=851&originWidth=1367&size=395329&status=done&style=none&width=683.5)
##### 7.2 设置>添加数据源，在数据源列表中选择Loki，配置 Loki 源地址：[http://10.5.76.54:3100](http://10.5.76.54:3100)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1594976014516-1244ebde-dbd8-4b4c-8317-ff8c82fed47f.png#align=left&display=inline&height=302&margin=%5Bobject%20Object%5D&name=image.png&originHeight=833&originWidth=1916&size=277148&status=done&style=none&width=694)




##### 7.3 保存完成后，切换到 grafana 左侧区域的Explore，即可进入到Loki的页面，点击Log labels就可以把当前系统采集的日志标签给显示出来，可以根据这些标签进行日志的过滤查询
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1594977028735-621cdb65-2a8a-4dbc-a5e3-b610a5d1fe69.png#align=left&display=inline&height=410&margin=%5Bobject%20Object%5D&name=image.png&originHeight=819&originWidth=1917&size=410287&status=done&style=none&width=958.5)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1594977091470-4f59f6bd-66c4-480a-a466-07c8755ffe77.png#align=left&display=inline&height=415&margin=%5Bobject%20Object%5D&name=image.png&originHeight=829&originWidth=1912&size=446356&status=done&style=none&width=956)
#### 8.日志过滤
编写日志流选择器后，可以通过编写搜索表达式来进一步过滤结果。搜索表达式可以只是文本或正则表达式。
查询示例：
```
{swarm_service="gbs-server_gbs-org"} |= "error"
{swarm_service="gbs-server_gbs-org"} |= "Caused by" 
```
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1594978389822-37993abe-77da-419b-a0cc-7d35c04dd179.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=176093&status=done&style=none&width=960)
过滤已实现以下过滤器类型：

- |= 行包含字符串。
- != 行不包含字符串。
- |~ 行匹配正则表达式。
- !~ 行与正则表达式不匹配。
regex表达式接受RE2语法。默认情况下，匹配项区分大小写，并且可以将regex切换为不区分大小写的前缀(?i)。

更多内容可参考[官方说明](https://github.com/grafana/loki/blob/master/docs/querying.md#grafana)
### 二、 个人总结
Loki是一个水平可扩展，高可用性，多租户的日志聚合系统，受到Prometheus的启发。它的设计非常经济高效且易于操作，因为它不会为日志内容编制索引，而是为每个日志流编制一组标签。官方介绍说到：Like Prometheus, but for logs.
个人觉得，相比elk/efk,loki更加适合中小团队。








> 参考文档：
>
> [https://www.cnblogs.com/ssgeek/p/11584870.html](https://www.cnblogs.com/ssgeek/p/11584870.html)
> [https://github.com/grafana/loki/blob/master/docs/README.md](https://github.com/grafana/loki/blob/master/docs/README.md)
> [https://www.bookstack.cn/read/loki/clients-docker-driver-README.md](https://www.bookstack.cn/read/loki/clients-docker-driver-README.md)
> [https://www.cnblogs.com/fsckzy/p/13231696.html](https://www.cnblogs.com/fsckzy/p/13231696.html)
