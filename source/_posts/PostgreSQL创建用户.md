---
title: PostgreSQL创建用户
date: 2017-12-20 15:17:29
tags: SQL
categories: Others
---

## 创建新用户

1. 先切换到Linux用户postgres，并执行psql：

```
su - postgres
psql
// 进入到该命令中
postgres=#
```

2. 创建数据库新用户，如 dbuser：

```
postgres=# CREATE USER dbuser WITH PASSWORD '*****';
```
注意：

- 语句要以分号结尾。
- 密码要用单引号括起来。

3. 创建用户数据库，如example：

```
postgres=# CREATE DATABASE example OWNER dbuser;
```

4. 将exampledb数据库的所有权限都赋予dbuser：

```
postgres=# GRANT ALL PRIVILEGES ON DATABASE example TO dbuser;
```

5. 使用命令 \q 退出psql：

```
postgres=# \q

```

## 配置远程访问

1. 编辑配置文件postgresql.conf

```
/etc/postgresql/9.5/main/postgresql.conf
```

> 添加/修改：在所有IP地址上监听，从而允许远程连接到数据库服务器：

```
listening_address: '*'
```

2. 编辑配置文件pg_hba.conf

```
/etc/postgresql/9.5/main/pg_hba.conf
```

> 添加/修改：允许任意用户从任意机器上以密码方式访问数据库，把下行添加为第一条规则：

host    all             all             0.0.0.0/0               md5

 
3. 重启数据库服务：

```
sudo systemctl restart postgresql
```

