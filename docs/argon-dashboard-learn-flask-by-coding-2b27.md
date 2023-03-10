# 烧瓶仪表板氩气-通过编码了解烧瓶

> 原文：<https://dev.to/sm0ke/argon-dashboard-learn-flask-by-coding-2b27>

你好编码器，

如果你是一个初学者，编写仪表板的想法可能听起来有挑战性或有点吓人，这篇文章应该会对你有一点帮助，因为最终，我们将有一个功能性的 [Flask 仪表板](https://appseed.us/admin-dashboards/flask-dashboard-argon)应用程序可以点击。

> *感谢阅读！* -内容由 **[App 生成器](https://appseed.us/)** 提供。

* * *

*   [烧瓶仪表盘氩气](https://appseed.us/admin-dashboards/flask-boilerplate-dashboard-argon) -产品页面
*   [烧瓶仪表盘氩气](https://flask-argon-dashboard.appseed-srv1.com/) -现场演示

* * *

[![Flask Dashboard Argon - Template project provided by AppSeed.](img/a17ad28c123d00de32ca46b5a527170f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vqDHSy6E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/app-generator/flask-boilerplate-dashboard-argon/master/media/flask-boilerplate-dashboard-argon-screen.png)

* * *

## App 功能

*   数据库管理系统:SQLite、PostgreSQL(生产)
*   数据库工具:SQLAlchemy ORM，Flask-Migrate(模式迁移)
*   带有**蓝图**的模块化设计，简单的代码库
*   基于会话的认证(通过 **flask_login** )，表单验证
*   部署脚本:Docker、Gunicorn / Nginx、Heroku

* * *

## 添加数据库

因为 Flask 本身不支持数据库，我们需要将 [Flask SqlAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/) 集成到我们的应用程序中，以便访问 underline database (Sqlite，MySql)
这个扩展帮助我们以面向对象的方式访问表，避免完全编写原始 SQL 查询。

* * *

> 要遵循的步骤:

*   安装`Flask SQLAlchemy`扩展
*   定义模型，添加所需的配置
*   更新应用启动程序，动态创建数据库

> 任何 python 扩展都可以通过 [PIP](https://pypi.org/project/pip/) 轻松安装

`pip install flask_sqlalchemy`

> 定义用户模型-在认证过程中使用- [来源](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/models.py)

```
class User(db.Model):

    id       = db.Column(db.Integer,     primary_key=True)
    user     = db.Column(db.String(64),  unique = True)
    email    = db.Column(db.String(120), unique = True)
    password = db.Column(db.String(500)) 
```

Enter fullscreen mode Exit fullscreen mode

> 添加 SQLAlchemy 所需的配置- [来源](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/configuration.py)

```
class Config():
    SQLALCHEMY_TRACK_MODIFICATIONS  = False
    SQLALCHEMY_DATABASE_URI = 'sqlite:///database.db') 
```

Enter fullscreen mode Exit fullscreen mode

> 将 SQLAchemy 对象绑定到我们的应用程序内文件 [**init** 。py](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/__init__.py)

```
from flask            import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

app.config.from_object('app.configuration.Config')

db = SQLAlchemy  (app) # flask-sqlalchemy 
```

Enter fullscreen mode Exit fullscreen mode

> 最后，更新应用启动器 [run.sh](https://github.com/app-generator/flask-argon-dashboard/blob/master/run.py) 来创建数据库

```
from app import app
from app import db

if __name__ == "__main__":
    db.create_all()
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 添加认证

如果我们具备基本的 Flask 知识，并花几分钟时间阅读文档，那么在 Flask 应用程序中实现基本身份验证应该是一件容易的事情。

> 要遵循的步骤

*   添加烧瓶登录模块
*   添加登录、注册表单和页面
*   处理认证路由

> 安装烧瓶登录-通过 PIP

`pip install flask-login`

> 安装表单助手来验证用户输入

```
pip install flask-login
pip install wtforms 
```

Enter fullscreen mode Exit fullscreen mode

> 创建[表单. py](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/forms.py)

```
from flask_wtf import FlaskForm
from wtforms.validators import Email, DataRequired

class LoginForm(FlaskForm):
    username = StringField   (u'Username', validators=[DataRequired()])
    password = PasswordField (u'Password', validators=[DataRequired()])

class RegisterForm(FlaskForm):
    username = StringField   (u'Username', validators=[DataRequired()])
    password = PasswordField (u'Password', validators=[DataRequired()])
    email    = StringField   (u'Email'   , validators=[Email()]) 
```

Enter fullscreen mode Exit fullscreen mode

> 创建[登录页面](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/templates/pages/login.html)

```
<form role="form" method="post" action="">
    {{ form.username(placeholder="Username") }}
    {{ form.password(placeholder="Password",type="password") }}
</form> 
```

Enter fullscreen mode Exit fullscreen mode

> 创建[注册](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/templates/pages/register.html)页面

```
<form role="form" method="post" action="">
    {{ form.username(placeholder="Username") }}
    {{ form.email(placeholder="eMail") }}
    {{ form.password(placeholder="Password") }}
</form> 
```

Enter fullscreen mode Exit fullscreen mode

> 更新 __init.py 文件——以包含 LoginManager

```
from flask            import Flask
from flask_sqlalchemy import SQLAlchemy # <-- new line
from flask_login      import LoginManager # <-- new line

app = Flask(__name__)

app.config.from_object('app.configuration.Config')

db = SQLAlchemy  (app) #flask-sqlalchemy

lm = LoginManager(   ) # flask-loginmanager
lm.init_app(app) # init the login manager 
```

Enter fullscreen mode Exit fullscreen mode

> 在[视图中创建新路线. py](https://github.com/app-generator/flask-argon-dashboard/blob/master/app/views.py) 用于登录、注销、注册

```
# update the imports to include latest assets
from app.models import User
from app.forms  import LoginForm, RegisterForm

# logout route
@app.route('/logout.html')
def logout():
    logout_user()

...

# register user
@app.route('/register.html', methods=['GET', 'POST'])
def register():

    # declare the Registration Form
    form = RegisterForm(request.form)
... 

@app.route('/login.html', methods=['GET', 'POST'])
def login():

    # Declare the login form
    form = LoginForm(request.form)
... 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 最后的话

如果有不清楚的地方，欢迎在评论区询问更多信息。**谢谢！**

> 资源

*   [烧瓶](https://flask.palletsprojects.com/) -官网
*   更多[烧瓶仪表盘](https://appseed.us/admin-dashboards/flask) -由 AppSeed 提供