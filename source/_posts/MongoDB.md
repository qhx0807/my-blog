---
title: MongoDB安装配置
date: 2017-11-20 14:39:05
tags: [mongodb]
categories: MongoDB
---

非关系型数据库MongoDb，与Node.js搭配最佳。记录MongoDB的安装和配置过程如下(windows)：

1. 下载MongoDB。 [mongodb down center](https://www.mongodb.com/download-center "down center") version3.4

2. 安装的过程比较简单，可以默认安装，默认是安装 `C:\Program Files\` 目录下 MongoDB

3. 配置mongodb数据库的数据文件和日志文件，需要手动创建：

	- 在 `C:\Program Files\MongoDB\Server\3.4` 目录下创建文件夹 `data` & `logs`, 在 `logs` 下新建文件 MongoDB.log 作为日志文件。

	- 以管理员身份运行cmd, 进入 `C:\Program Files\MongoDB\Server\3.4\bin` 输入命令： `mongod -dbpath "C:\Program Files\MongoDB\Server\3.4\data"` 执行完出现 ... on port 27017 说明成功，此时已可以连接数据库。
    <!-- more -->
	- 以管理员身份运行cmd, 进入 `C:\Program Files\MongoDB\Server\3.4\bin` 输入命令：  `mongod -dbpath "C:\Program Files\MongoDB\Server\3.4\data" -logpath "C:\Program Files\MongoDB\Server\3.4\logs\MongoDB.log" -install -serviceName "MongoDB"` 分别为指定数据库data路径，日志路径，将mongodb作为Windows服务。

4. 输入net start mongodb，mongodb的服务被启动。



5. 输入 mongo 可以进入mongoDB shell `show dbs`

|  name |  size  |
| ------------ | ------------ |
|  local | 0.000GB  |
| admin | 0.000GB  |


6. 完成上述步骤已经可以使用数据库，MongoDB默认是不需要输入用户名和密码，客户就可以登录的。出于安全性的考虑，我们还是要为其设置用户名和密码。

- 另起一个终端，运行下列命令
	
```bash
use admin
db.createUser(
   {
     user: "adminUser",
     pwd: "adminPass",
     roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
   }
)
```

- Mongodb 用户验证登陆
	
	
```bash
mongo --port 27017
use admin
db.auth("adminUser", "adminPass")
```

- 创建普通用户

```bash
use foo
db.createUser(
  {
    user: "simpleUser",
    pwd: "simplePass",
    roles: [ { role: "readWrite", db: "foo" },
             { role: "read", db: "bar" } ]
  }
)
```

注意：use foo表示用户在 foo 库中创建，就一定要 foo 库验证身份，即用户的信息跟随随数据库。比如上述 simpleUser 虽然有 bar 库的读取权限，但是一定要先在 foo 库进行身份验证，直接访问会提示验证失败。
还有一点需要注意，如果 admin 库没有任何用户的话，即使在其他数据库中创建了用户，启用身份验证，默认的连接方式依然会有超级权限

7. 生产中常用 URI 形式对数据库进行连接

`mongodb://your.db.ip.address:27017/foo`

`mongodb://simpleUser:simplePass@your.db.ip.address:27017/foo`