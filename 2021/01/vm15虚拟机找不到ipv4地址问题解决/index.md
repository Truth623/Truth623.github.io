# vm15虚拟机找不到ipv4地址问题解决



#### 1. 打开编辑>虚拟网络编辑器

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1612850138329-c6e965b2-56a6-42b3-aec9-6e684f295854.png#align=left&display=inline&height=380&margin=%5Bobject%20Object%5D&name=image.png&originHeight=760&originWidth=1128&size=92085&status=done&style=none&width=564)

#### 2. 更改设置

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1612850261539-b0570488-c4c7-41f8-8476-f618fa7a8b6f.png#align=left&display=inline&height=351&margin=%5Bobject%20Object%5D&name=image.png&originHeight=702&originWidth=730&size=52337&status=done&style=none&width=365)

#### 3. 将网卡改为自己的真实wifi网卡，或者直接点击“还原默认设置”

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1318615/1612850386256-17b471d5-1f82-49df-b81c-b151f02dfd7b.png#align=left&display=inline&height=333&margin=%5Bobject%20Object%5D&name=image.png&originHeight=666&originWidth=730&size=61266&status=done&style=none&width=365)

#### 总结：

产生这种问题的原因，推测可能是电脑安装了各种vpn引起的，导致桥接虚拟机找不到真正的网卡。

