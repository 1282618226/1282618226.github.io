---
title: "Ruoyi"
date: 2022-01-11T21:51:57+08:00
draft: true
---

# 1、准备工作

```
JDK >= 1.8 (实际：1.8.311)
Mysql >= 5.7.0 (实际：8.0.27)
Maven >= 3.0（实际：3.8.4）
```
# 2、修改配置
1. 修改数据库连接，编辑resources目录下的application-druid.yml
![image.png](https://note.youdao.com/yws/res/10256/WEBRESOURCEba4e0d66d695274e4469fbd5f975bafa)
2. 修改服务器配置，编辑resources目录下的application.yml
![image.png](https://note.youdao.com/yws/res/10259/WEBRESOURCE988b138301c292576884fb73af07e3b2)
![image.png](https://note.youdao.com/yws/res/10261/WEBRESOURCE0e7bbedbf916ba5dc23a5cf69a4f580e)
3. 修改输出日志配置，编辑resources目录下的logback.xml
![image.png](https://note.youdao.com/yws/res/10268/WEBRESOURCE9456fbd7583af1329c65678b2964e2ed)
# 3、部署系统
1. 将源码上传到服务器
![image.png](https://note.youdao.com/yws/res/10274/WEBRESOURCE05af092f1b35013434228589fe9da8a7)
2. 后端打包
- 进入项目根目录，执行

```
mvn package
```
- 会在各模块下的target目录生成jar包。
![image.png](https://note.youdao.com/yws/res/10286/WEBRESOURCE58f86a74057a6142b2d0770d8fd72614)
- 进入 ruoyi-admin/target 目录，执行：

```
nohup java -jar ruoyi-admin.jar &
```
- 能以后台的形式运行。
3. 前端打包
- 进入项目根目录，安装依赖，执行：

```
npm install --unsafe-perm --registry=https://registry.npm.taobao.org
```
- 执行如下命令，进行生产环境的打包

```
npm run build:prod
```
- 打包完成后，会在当前目录生成一个dist文件夹。
- 配置nginx路径。

```
location /prod-api/ {
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE_HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://localhost:8080/;
        }
```

![image.png](https://note.youdao.com/yws/res/10327/WEBRESOURCE74299639aa968a4395981749f307e2dd)
![image.png](https://note.youdao.com/yws/res/10329/WEBRESOURCEeb6b37396fb930095baad46f29d2e8e2)
![image.png](https://note.youdao.com/yws/res/10331/WEBRESOURCEc0a14181a4ed1b63f3952799277d256e)

- 执行如下命令，运行nginx

```
/usr/local/nginx/sbin/nginx
```

# 4、centos开放端口

```
3.1 查看开放的端口：firewall-cmd --list-ports 
3.2 开启端口：firewall-cmd --zone=public --add-port=80/tcp --permanent

3.3 命令含义：

–zone #作用域
–add-port=80/tcp #添加端口，格式为：端口/通讯协议
–permanent #永久生效，没有此参数重启后失效
3.4 重启、停止、禁用、查看防火墙

1 firewall-cmd --reload #重启firewall
2 systemctl stop firewalld.service #停止firewall
3 systemctl disable firewalld.service #禁止firewall开机启动
4 firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
3.5 结合上述命令来看，需要将8080端口添加到防火墙的开放端口中，然后重新载入防火墙的配置即可。如下：

1 sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
2 sudo firewall-cmd --reload
3 sudo firewall-cmd --list-ports
```
