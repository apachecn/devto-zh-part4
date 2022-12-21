# 运行 python django 站点

> 原文：<https://dev.to/endykaufman/running-python-django-site-4l49>

安装 python 和所有需要的库

```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib -y
sudo apt-get install python-pip python-dev build-essential -y
sudo apt-get install libpq-dev -y
sudo pip install --upgrade pip
sudo pip install --upgrade virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

创建项目

```
mkdir mysite && cd mysite
virtualenv venv
source venv / bin / activate
pip install django-toolbelt
django-admin.py startproject mysite.
echo "web: gunicorn mysite.wsgi"> Procfile
pip freeze> requirements.txt
echo "venv"> .gitignore
git init
git add.
git commit -m "First Commit for Mysite" 
```

Enter fullscreen mode Exit fullscreen mode

发射地点

```
virtualenv venv
source venv / bin / activate
export $ (cat .env)
pip install -r requirements.txt
python manage.py collectstatic --noinput
gunicorn project.wsgi -b 0.0.0.0:5000 
```

Enter fullscreen mode Exit fullscreen mode

在 Django 处理移民问题

```
python manage.py migrate
python manage.py makemigrations
python manage.py makemigrations --empty yourappname 
```

Enter fullscreen mode Exit fullscreen mode

使用所需软件包列表

```
pip freeze> requirements.txt
pip install -r requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

创建应用程序

```
django-admin.py startapp myapp app / myapp 
```

Enter fullscreen mode Exit fullscreen mode