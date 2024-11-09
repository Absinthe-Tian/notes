---
title: 9.蓝图与项目结构
date: 2023-12-19 19:08:16
categories: Flask框架
tags: 
  - blueprint
  - 项目结构
---

# 使用

使用蓝图可以按模块进行视图管理。

特点：

* 一个应用可以具有多个蓝图。
* 蓝图可以注册任何一个未使用的URL。
* 蓝图可以单独具有自己的模板、静态文件或其他的通用操作方法，并不是必须要实现应用的视图函数。

蓝图并不是一个完整的应用，它不能独立于应用运行，而时要注册到某个应用中。

```python
from flask import Blueprint

# 创建蓝图
user_bp = Blueprint('user', __name__, url_prefix='/user', template_folder='templates', static_folder='static', subdomain=None)


# 蓝图中的视图
@user_bp.route('/login/')
def user():
    return "登录模块"


# 注册蓝图
app.register_blueprint(user_bp, url_prefix='/user')
```

> 只有注册的蓝图才可以生效。
>
> 访问蓝图所属的静态资源时，需要注意是否添加前缀。
>
> 蓝图中`url_for('bpName.source')`需要使用到蓝图名称。

# 项目结构

使用蓝图可以帮助我们更好的管理项目结构。

```xml
flask_project/
  |- .env
  |- config.py
  |- run.py
  |- requirements.txt
  |- README.md
  |- migrations/
  |- apps/
    |- __init__.py
    |- extensions.py
    |- static/
      |- css/
      |- js/
      |- images/
    |- templates/
      |- moduleName
        ｜- xxx.html
      |- index.html
      |- base.html
    |- moduleName
      |- __init__.py
      |- views.py
      |- models.py
      |- dao.py
      |- forms.py
```

* `.env`：环境变量配置文件。

```python
FLASK_APP=run
FLASK_DEBUG=true
FLASK_ENV=development
FLASK_RUN_HOST=0.0.0.0
FLASK_RUN_PORT=8000
```

* `config.py`：app.config配置文件。

```python
from datetime import timedelta


class Config:
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://username:password@host:port/dbName'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SECRET_KEY = 'secret_key'
    JWT_SECRET_KEY = 'secret_key'
    JWT_ACCESS_TOKEN_EXPIRES = timedelta(days=7)

```

* `run.py`：项目的启动文件。

```python
from apps import create_app

if __name__ == '__main__':
    app = create_app()
    app.run(host='0.0.0.0', port=8000, debug=True)

```

* `requirements.txt`：项目依赖管理文件。

```ini
Flask==3.0.0
Flask-JWT-Extended==4.6.0
Flask-Migrate==4.0.5
Flask-SQLAlchemy==3.1.1
```

* `README.md`：项目的说明文件。

```markdown
# 说明

pass

# 配置

pass

# 启动

pass
```

* `migrations/`：模型迁移映射目录，由Flask-Migrate生成，一般不需要管理。

* `apps/`：存放项目的管理模块。

	* `apps/__init__.py`：用于初始化app。
	
	```python
	from flask import Flask
	
	from config import Config
	from apps.extensions import db, jwt, migrate
	from apps.moduleName.models import *
	
	
	def create_app():
	    # 创建app
	    app = Flask(__name__)
	    # 配置app
	    app.config.from_object(Config)
	
	    # 加载插件
	    jwt.init_app(app)
	    db.init_app(app)
	    migrate.init_app(app, db)
	
	    # 注册蓝图
	    from apps.moduleName import bp as module_bp
	    app.register_blueprint(module_bp)
	
	    return app
	
	```
	
	* `apps/extensions.py`：创建app的第三方插件。
	
	```python
	from flask_sqlalchemy import SQLAlchemy
	from flask_migrate import Migrate
	from flask_jwt_extended import JWTManager
	
	migrate = Migrate()
	db = SQLAlchemy()
	jwt = JWTManager()
	
	```
	
	* `apps/static/`：存放js、css、images等静态文件。
	
	* `apps/templates/`：存放每个功能模块的模板。
	
	* `apps/modulName/`：存放功能模块的蓝图、视图、模型、DAO、表单类。
	
	  * `apps/modulName/__init__.py`：管理功能模块的蓝图。
	
	  ```python
	  from flask.blueprints import Blueprint
	  from .views import *
	  
	  # 创建蓝图
	  bp = Blueprint('moduleName', __name__, url_prefix='/moduleName')
	  # 注册路由
	  bp.add_url_rule('/route/', view_func=LoginView.as_view('endpoint'))
	  ```
	
	  * `apps/modulName/views.py`：视图。
	
	  ```python
	  from flask.views import MethodView
	  from flask import render_template, request
	  
	  from .forms import xxxForm
	  from .dao import xxxDAO
	  
	  class xxxView(MethodView):
	      def get(self):
	          pass
	  
	      def post(self):
	          pass
	  
	  ```
	
	  * `apps/modulName/models.py`：模型。
	
	  ```python
	  from apps import db
	  
	  
	  class User(db.Model):
	      __tablename__ = 't_users'
	      uid = db.Column(db.Integer, primary_key=True)
	      username = db.Column(db.String(20), unique=True, nullable=False)
	      password = db.Column(db.String(20), nullable=False)
	  
	      def __repr__(self):
	          return f"<User: uid={self.uid}, username={self.username}>"
	  
	  ```
	
	  * `apps/modulName/dao.py`：DAO。
	
	  ```python
	  from .models import User
	  
	  
	  class UserDAO:
	  
	      @staticmethod
	      def get_user_by_uid(uid):
	          user = User.query.filter_by(uid=uid).first()
	          return user
	  
	  ```
	
	  * `apps/modulName/forms.py`：表单（可省略）。
	
	  ```python
	  from wtforms import Form, StringField
	  from wtforms.validators import Length
	  
	  
	  class LoginForm(Form):
	      username = StringField(label='Username', validators=[Length(min=6, max=20)])
	      password = StringField(label='Password', validators=[Length(min=6, max=35)])
	  ```
