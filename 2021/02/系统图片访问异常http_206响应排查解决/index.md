# 系统图片访问异常（HTTP_206响应）排查解决


<a name="ZfOL6"></a>
#### 问题描述
- 周一上午，客户群大量反馈：线上所有系统的图片出现有时无法访问、访问加载慢、有时需要多次加载才可以显示等问题。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613971301324-dfc2a428-0d8c-49fb-b45a-17eab9339f55.png#align=left&display=inline&height=354&margin=%5Bobject%20Object%5D&name=image.png&originHeight=707&originWidth=1069&size=83467&status=done&style=none&width=534.5)
<a name="AHdjf"></a>
#### 问题排查

- 根据这种有时无法访问的问题，首先推测是不是有的节点挂了，经排查，发现前后端docker服务集群中的所有服务节点均正常。
- 推断在应用程序服务正常，只是图片异常的情况，很有可能是nginx（通用入口服务器）或者fastdfs(文件服务器)的问题；经过多次刷新后，终于登录上后台管理系统，测试文件上传功能，成功；然后访问上传成功的图片，发现只加载了一半，而另一个同事测试移动端系统的图片http响应码为206（客户端通过发送范围请求头Range抓取到了资源的部分数据），而图片又不大，于是可以大致推断fastdfs正常，很有可能是nginx异常引起的。
- 登录nginx服务器（通用入口），执行top命令，发现nginx cpu占用20%左右，正常情况下都是占用0.几%，不至于这么多；执行free -mh命令，发现mem的free只剩下一百多mb,而used也只用的几百mb,反而buffer/cache很高。

    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613973292811-ca1325eb-c4a0-433c-9b1b-c081b430ec15.png#align=left&display=inline&height=213&margin=%5Bobject%20Object%5D&name=image.png&originHeight=426&originWidth=1089&size=59595&status=done&style=none&width=544.5)

- 执行df -h命令，未响应；执行df /命令，发现系统盘空间100%。综上所述，系统盘空间不够导致nginx无法正常存储运行日志，缓冲区占用较高说明磁盘空间不足开始向物理内存借用空间，系统盘需要扩充以快速解决问题。

    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613972719997-15c90408-eb86-4b9b-8846-df28a85095bd.png#align=left&display=inline&height=34&margin=%5Bobject%20Object%5D&name=image.png&originHeight=67&originWidth=715&size=13410&status=done&style=none&width=357.5)<br />    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613973655981-5a5e92b0-0847-4e52-93c5-b6edea46e7bb.png#align=left&display=inline&height=140&margin=%5Bobject%20Object%5D&name=image.png&originHeight=280&originWidth=808&size=34330&status=done&style=none&width=404)
<a name="WMtFX"></a>
#### 临时解决

- 经过各方资源的积极配合和快速处理，系统盘成功又增加了50G可用空间，重启nginx后，线上图片访问恢复正常。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613973699702-e76ce731-e44d-432e-9cc7-b0935efdd0ea.png#align=left&display=inline&height=122&margin=%5Bobject%20Object%5D&name=image.png&originHeight=244&originWidth=769&size=31014&status=done&style=none&width=384.5)
<a name="zjdeT"></a>
#### 根本原因

- 临时解决后，准备找出哪个目录占用了大量空间，输入du -sh /*,查看根目录下各目录使用空间，发现/app目录占用了32G。

    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613974696389-ae025a66-2c91-408c-9eda-030f0889ff0a.png#align=left&display=inline&height=290&margin=%5Bobject%20Object%5D&name=image.png&originHeight=580&originWidth=926&size=61614&status=done&style=none&width=463)

- 经排查，/app目录是很多nginx子配置的access_log目录，而/app目录竟然不是挂载到/data数据盘上的，而是挂在了系统盘上；这样，不断产生的日志文件导致了系统盘占满。

    ![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613973699702-e76ce731-e44d-432e-9cc7-b0935efdd0ea.png#align=left&display=inline&height=122&margin=%5Bobject%20Object%5D&name=image.png&originHeight=244&originWidth=769&size=31014&status=done&style=none&width=384.5)
<a name="T9hgt"></a>

#### 最终解决

- 把/app移动到空间足够的数据盘目录/data。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613973077878-3b780c20-c8b9-41c5-ad85-3110e4030064.png#align=left&display=inline&height=42&margin=%5Bobject%20Object%5D&name=image.png&originHeight=84&originWidth=673&size=9298&status=done&style=none&width=336.5)

- df -h 查看空间并未释放，输入lsof | grep app，nginx进程之前打开了这些文件，还在向/app写日志，并未真正从磁盘删除。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613976463700-e9340223-cfe9-4523-9dcc-cfaa52403813.png#align=left&display=inline&height=520&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1040&originWidth=1920&size=368343&status=done&style=none&width=960)

- 修改nginx配置文件，查看nginx域名配置，把所有日志写入文件路径为/app开头的，替换成/data/app。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613977029853-351dee9d-6b69-4b57-b16a-209fa3a7d2a8.png#align=left&display=inline&height=83&margin=%5Bobject%20Object%5D&name=image.png&originHeight=166&originWidth=777&size=13731&status=done&style=none&width=388.5)

- 新的日志写入已经生效，老的还被nginx死进程占用着，需要删除nginx死进程。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613978537985-d44a0adf-9ecc-426e-9960-ff491533afca.png#align=left&display=inline&height=449&margin=%5Bobject%20Object%5D&name=image.png&originHeight=898&originWidth=1676&size=293035&status=done&style=none&width=838)<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613978419439-2256cae9-57c2-4b6b-ac72-af4161e1806d.png#align=left&display=inline&height=52&margin=%5Bobject%20Object%5D&name=image.png&originHeight=103&originWidth=1015&size=10726&status=done&style=none&width=507.5)

- 删除死进程后查看磁盘挂载空间，/app占用的32G空间已释放。

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613973699702-e76ce731-e44d-432e-9cc7-b0935efdd0ea.png#align=left&display=inline&height=122&margin=%5Bobject%20Object%5D&name=image.png&originHeight=244&originWidth=769&size=31014&status=done&style=none&width=384.5)![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1613978711492-a39a6e95-3c6d-43ff-8248-8a35ff934741.png#align=left&display=inline&height=124&margin=%5Bobject%20Object%5D&name=image.png&originHeight=247&originWidth=756&size=31632&status=done&style=none&width=378)



