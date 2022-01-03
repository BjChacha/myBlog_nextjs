---
title: 'LOG->在Ubuntu快速部署Django'
description: '开发5分钟，部署2小时'
toc: false
layout: post
categories: [log, django]
image: images/posts/2018-04-30-ubuntu-django-deployment/django_logo.png
author: BjChacha
---

## 配置环境

- Ubuntu 16.04-64bit
- Python 3.5
- Django 2.0.4

## **第零步 部署准备工作**

编写`requirements.txt`文件(根据自身情况编写)

    Django==2.0.4
    django-pagedown==1.0.4
    Markdown==2.6.11
    Pygments==2.2.0

关闭项目的`DEBUG`模式  
`/mysite/mysite/settings.py`

    ...
    DEBUG = True					# 关闭DEBUG模式

    ALLOWED_HOSTS = ['*']			# 允许访问的域名，'*'表示允许所有

    LANGUAGE_CODE = 'en_us'			# Ubuntu中Django不包含中文语言包

    TIME_ZONE = 'Asia/Shanghai'		# 修改时区

本示例项目名字为`mysite`,后面内容请根据自身情况修改  
然后把项目和`requirements.txt`上传到服务器  
将项目放在`/home`,即项目目录为`/home/mysite`(即`manage.py`文件所在目录)

---

## **第一步 更新系统**

> sudo apt-get update  
> sudo apt-get upgrade

---

## **第二步(可选) 安装 Python3.6**

> sudo apt-get install build-essential checkinstall  
> sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev  
> cd /etc/  
> wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz  
> sudo tar xzf Python-3.6.4.tgz  
> cd Python-3.6.4  
> sudo ./configure  
> sudo make altinstall

---

## **第三步 安装项目依赖项**

> pip3 install -r requirements.txt

---

## **第四步 项目在服务器中初始化**

> python manage.py makemigrations  
> python manage.py migrate  
> python manage.py createsuperuser # 若数据库中已有管理员账号则可跳过  
> python manage.py collectstatic

测试

> python manage.py runserver 0.0.0.0:80

访问公网 ip，能显示项目内容，但可能无法显示静态文件内容  
杀掉 80 端口

> fuser -k 80/tcp

---

## **第五步 安装 uWSGI**

> pip3 install uwsgi

测试

> uwsgi --http :80 --chdir /home/mysite/ -w mysite.wsgi

访问公网 ip，访问正常则退出并继续

> mkdir -p /etc/uwsgi/sites  
> cd /etc/uwsgi/sites  
> nano mysite.ini

输入以下内容

    [uwsgi]
    project = mysite
    base = /home

    chdir = %(base)/%(project)
    module = %(project).wsgi:application

    master = true
    processes = 5

    socket = %(base)/%(project)/%(project).sock
    chmod-socket = 666
    vacuum = true

---

## **第六步 安装 Nginx**

> apt-get install nginx  
> nano /etc/nginx/sites-available/mysite

输入以下内容

    server {

    	listen 80;
    	server_name yourdomain.com;

    	location /static/ {
    		root /home/mysite;
    		}
    	location / {
    		include         uwsgi_params;
    		uwsgi_pass      unix:/home/mysite/mysite.sock;
    		}
    	}

其中`yourdomain.com`改成自己的域名(假设已经完成解析设置)

链接文件并检测 nginx 服务器

> ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/  
> service nginx configtest

(可能需要)删除 nginx 默认模板

> rm -r /etc/nginx/sites-available/default

---

## **第七步 启动服务**

> service nginx restart  
> uwsgi /etc/uwsgi/sites/mysite.ini -d /home/mysite/mysite.log

访问域名，能够正常访问，静态文件也能正常显示

---

## **最终步 欢呼吧！**

[来源](https://www.jianshu.com/p/d6f9138fab7b)
