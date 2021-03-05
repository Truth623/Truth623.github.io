# Hugo博客LoveIt主题valine评论系统配置



1、登录[leancloud](https://leancloud.cn/dashboard/login.html#/signin),选择“国际版”，若无账号先注册，可能需要手机号验证后才能创建应用![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595916936670-194aee17-22c9-4aae-878b-e790e228c0d2.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=95641&status=done&style=none&width=960)
2、创建应用，如图所示


![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595917041216-ce4a5d91-81d7-48a1-b1a6-421266e276b0.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=138355&status=done&style=none&width=960)
3、将AppID、AppKey保存，后面需要配置到代码中
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595917110992-ad0d074e-52bf-47c6-a112-3fdb931a2aab.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=164892&status=done&style=none&width=960)
4、安全域名，此处配置你自己的域名，配置后仅可在该域名下通过JavaScript SDK 调用服务器资源
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595917387135-8b07890d-825c-4853-b8ee-551e8ce4e9ed.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=134599&status=done&style=none&width=960)
5、域名绑定（重点），如果是国际版，不要求绑定自定义域名，可以使用如下共享域名：`appid前八位.api.lncldglobal.com`；如果是国内节点，就需要绑定自定义域名，且根据工信部规定需要备案
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595917349198-052f95fb-4dd7-49ae-a583-f196cfc2e972.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=173920&status=done&style=none&width=960)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595925301816-0d67c050-2352-4fbb-abb7-0552a08465e2.png#align=left&display=inline&height=434&margin=%5Bobject%20Object%5D&name=image.png&originHeight=867&originWidth=1920&size=144942&status=done&style=none&width=960)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595926471234-ff7b7e71-690e-49c5-9733-4ab1d51d2fb4.png#align=left&display=inline&height=248&margin=%5Bobject%20Object%5D&name=image.png&originHeight=496&originWidth=1367&size=77708&status=done&style=none&width=683.5)
6、修改代码配置文件，需要配置前面保存的appId,appKey，serviceURLs填写你绑定的自定义域名或者使用国际版时的共享域名
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1318615/1595925965815-0e1a282a-7fea-434c-83bf-306c23727101.png#align=left&display=inline&height=152&margin=%5Bobject%20Object%5D&name=image.png&originHeight=304&originWidth=1145&size=38837&status=done&style=none&width=572.5)
7、参考文档
> [https://leancloud.cn/docs/rest_api.html](https://leancloud.cn/docs/rest_api.html)

> [https://leancloud.cn/docs/custom-api-domain-guide.html](https://leancloud.cn/docs/custom-api-domain-guide.html)

> [https://www.smslit.top/2018/07/08/hugo-valine/](https://www.smslit.top/2018/07/08/hugo-valine/)



