---
title: Django后台搭建
date: 2017-09-16 09:23:15
tags: Django
categories: Python
---
# Django后台搭建
需安装git, pip, python版本3.5.x以上

python3搭建虚拟环境
 
### python配置中国镜像
root账户配置中国镜像路径: ~/.pip/pip.conf
 
#### 内容
```
[global]
trusted-host = mirrors.aliyun.com
index-url = http://mirrors.aliyun.com/pypi/simple
```

### 启动命令
1. 创建虚拟环境

```
python3 -m venv filename
```

2. 进入虚拟环境中

```
source filename/bin/activate
```

3. 使用git下载项目（项目名称service， 可将项目放到虚拟环境目录下）

```
git clone ssh地址
```

4. 安装项目python包

	路径 service/requirements/base.txt

```
pip install -r requirements/base.txt
```

5. 执行启动命令(进入service文件夹执行下列命令)

```
./manager.py makemigrations user
./manager.py migrate user
./manager.py migrate
./manager.py runserver 0.0.0.0:8080 
```