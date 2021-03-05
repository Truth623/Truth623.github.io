# 解决druid连接池jquery的XSS漏洞


## 解决过程
1.我们的项目经过渗透测试，发现目前druid中所用的jquery版本是v1.8.0，存在xss漏洞，网上查找了一下资料，部分资料说低于1.10.2存在xss漏洞。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596772326070-b7192b34-822e-44fd-8d36-ad8e5313fdcd.png#align=left&display=inline&height=516&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1031&originWidth=1920&size=224361&status=done&style=none&width=960)

2.本人一开始升级到的版本是1.12.4，但是用jquery with xss测试工具测试后，还存在其他漏洞，于是就升级到了目前最新的稳定版本jquery3.5.1，暂无xss漏洞， 顺便隐藏一下版本号，增加黑客破解的难度。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596790879660-db275378-6174-4f7b-93f7-bb454a2dbe66.png#align=left&display=inline&height=375&margin=%5Bobject%20Object%5D&name=image.png&originHeight=749&originWidth=1824&size=278501&status=done&style=none&width=912)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596786843215-d7c95eef-b6a6-43f9-9d08-d9bfb1a83be5.png#align=left&display=inline&height=340&margin=%5Bobject%20Object%5D&name=image.png&originHeight=680&originWidth=1910&size=142294&status=done&style=none&width=955)

3.重新maven编译打包后，将自己的jar包替换掉maven仓库中jar包

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596774649108-46b3464e-287d-4f00-b64b-f9175de9cae7.png#align=left&display=inline&height=238&margin=%5Bobject%20Object%5D&name=image.png&originHeight=475&originWidth=1221&size=77582&status=done&style=none&width=610.5)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596774686554-d082af5f-6129-4050-9ba5-36dd93e22e12.png#align=left&display=inline&height=241&margin=%5Bobject%20Object%5D&name=image.png&originHeight=481&originWidth=1251&size=77468&status=done&style=none&width=625.5)

## 测试对比：Bug-11290

- jquery1.8.1

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596780703533-308345b9-9bd4-4b13-b747-a614727771b7.png#align=left&display=inline&height=363&margin=%5Bobject%20Object%5D&name=image.png&originHeight=725&originWidth=1296&size=92548&status=done&style=none&width=648)

- jquery1.12.4

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596780820988-61495b61-7248-4ab0-9691-3d625ba8368b.png#align=left&display=inline&height=228&margin=%5Bobject%20Object%5D&name=image.png&originHeight=455&originWidth=1296&size=56230&status=done&style=none&width=648)

- jquery3.5.1

![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596787021027-07a8c007-a352-48d6-b69c-e30ecdab8cba.png#align=left&display=inline&height=261&margin=%5Bobject%20Object%5D&name=image.png&originHeight=522&originWidth=1296&size=50693&status=done&style=none&width=648)





## 总结

1.jquery1.12.4和3.5.1已经解决jquery的bug-11290
1.jquery3.5.1是目前bug最少的版本
1.欢迎评论指正




## 后续安排
    顺便做一次开源贡献吧，之前发过一个issue，不知道有没有其他人提到过，反正pull requests发上去了。
    如果能得到很多人赞同的话，希望项目维护人员能尽快merge，我也想成为一个contributor。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596788880998-d78be19d-a06a-45ff-b3ba-13b4725ad895.png#align=left&display=inline&height=354&margin=%5Bobject%20Object%5D&name=image.png&originHeight=708&originWidth=1787&size=97023&status=done&style=none&width=893.5)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1596790257129-3effc0ad-ef8c-4665-b514-54ae72f74c91.png#align=left&display=inline&height=396&margin=%5Bobject%20Object%5D&name=image.png&originHeight=792&originWidth=1892&size=172289&status=done&style=none&width=946)


