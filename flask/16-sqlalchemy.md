---
title: 16.SQLAlchemy
date: 2023-12-24 13:36:43
categories: Flask框架
tags:
  - SQL
  - ORM
  - CRUD
  - alembic
  - migrate
---

# 介绍

SQLAlchemy是一个ORM（Object Relational Mapping）框架，用于实现面向对象编程语言里不同类型系统的数据之间的转换。

优点：

1. 简化数据库操作：SQLAlchemy提供了一套简洁的API，使得开发者可以更加方便地与关系型数据库交互，而无需编写复杂的SQL语句。
2. 提高开发效率：使用SQLAlchemy可以减少开发者与数据库之间的交互次数，从而提高开发效率。
3. 提高代码可读性：SQLAlchemy使用面向对象的方式来操作数据库，使得代码更加清晰、易于理解和维护。
4. 支持多种数据库：SQLAlchemy支持多种数据库，包括Mysql、PostgreSQL、SQLite、Oracle、Microsoft SQL Server等，可以根据需要灵活选择。
5. 提供ORM功能：SQLAlchemy提供了ORM功能，可以将数据库中的表映射为Python中的类和对象，从而实现数据库中数据和Python中数据的映射和转换。
6. 提供事务处理和回滚功能：SQLAlchemy提供了事务处理和回滚功能，可以保证数据的一致性和完整性。

# 连接数据库

连接数据库之前，需要确保已安装SQLAlchemy及使用的数据库驱动（如pymysql）。

```bash
pip3 install sqlalchemy pymysql
```

连接数据库：

```python
from sqlalchemy import create_engine, text

# 创建引擎
url = 'mysql+pymysql://root:123456@localhost:3306/project'
engine = create_engine(url)

# 测试连接
conn = engine.connect()
# sql语句需要使用text包裹
sql = text("select 1;")
# 获取查询集
res = conn.execute(sql).fetchall()
print(res)
```

> URL：dialect(数据库类型)+driver(数据库驱动)://username(用户名):password(密码)@host(主机IP):port(主机端口)/dbname(数据库名)。

# 映射模型

## 简单定义

```python
from sqlalchemy import create_engine, Column, String, Integer
from sqlalchemy.orm import declarative_base, DeclarativeBase

url = 'mysql+pymysql://root:20010201@localhost:3306/flask_demo'
engine = create_engine(url)

# 方式一，创建模型基类
Base = declarative_base()

# 方式二，创建模型基类
class Base(DeclarativeBase):
    pass

# 继承模型基类，创建用户表模型
class User(Base):
  	# 表名
    __tablename__ = "t_users"
    
    # 表字段及约束
    uid = Column(Integer, primary_key=True, autoincrement=True, comment="用户ID")
    username = Column(String(32), nullable=False, comment="用户名")
    password = Column(String(32), nullable=False, comment="密码")

    def __init__(self, username, password):
        self.username = username
        self.password = password


# 构建所有继承模型基类的表
Base.metadata.create_all(engine)
```

## 字段类型

1. `Integer`：整数类型，对应的数据库字段类型为`INT`。
2. `String`：字符串类型，对应的数据库字段类型为`VARCHAR`。
3. `Float`：浮点数类型，对应的数据库字段类型为`FLOAT`。
4. `DateTime`：日期时间类型，对应的数据库字段类型为`DATETIME`。
5. `Boolean`：布尔类型，对应的数据库字段类型为`BOOLEAN`。
6. `Text`：长文本类型，对应的数据库字段类型为`TEXT`。
7. `Unicode`：Unicode字符串类型，对应的数据库字段类型为`VARCHAR`。
8. `UnicodeText`：Unicode长文本类型，对应的数据库字段类型为`TEXT`。
9. `SmallInteger`：小整数类型，对应的数据库字段类型为`SMALLINT`。
10. `BigInteger`：大整数类型，对应的数据库字段类型为`BIGINT`。
11. `Numeric`：精确数值类型，对应的数据库字段类型为`NUMERIC`。
12. `Date`：日期类型，对应的数据库字段类型为`DATE`。
13. `Time`：时间类型，对应的数据库字段类型为`TIME`。
14. `Enum`：枚举类型，对应的数据库字段类型为`ENUM`。
15. `PickleType`：Pickle类型，对应的数据库字段类型为`BLOB`。
16. `JSON`：JSON类型，对应的数据库字段类型为`JSON`。
17. `ARRAY`：数组类型，对应的数据库字段类型为`ARRAY`。
18. `BINARY`：二进制类型，对应的数据库字段类型为`BINARY`。
19. `VARBINARY`：可变长二进制类型，对应的数据库字段类型为`VARBINARY`。
20. `LargeBinary`：大二进制类型，对应的数据库字段类型为`BLOB`。
21. `BLOB`：二进制大对象类型，对应的数据库字段类型为`BLOB`。
22. `CLOB`：字符大对象类型，对应的数据库字段类型为`CLOB`。
23. `NCLOB`：国家字符大对象类型，对应的数据库字段类型为`NCLOB`。

## 字段约束

1. `nullable`：指定该列是否允许为空。默认值为`True`。
2. `default`：指定该列的默认值。
3. `server_default`：指定该列的服务器端默认值。
4. `primary_key`：指定该列是否为主键。默认值为`False`。
5. `unique`：指定该列的值是否唯一。默认值为`False`。
6. `index`：指定是否为该列创建索引。默认值为`False`。
7. `comment`：指定该列的注释。
8. `autoincrement`：指定该列是否自增。默认值为`False`。
9. `sequence`：指定该列的序列。
10. `onupdate`：指定该列的更新触发器。
11. `name`：指定该列的名称。
12. `type_`：指定该列的数据类型。
13. `check`：指定该列的约束条件。如果设置了该参数，则该列的值必须满足指定的约束条件；否则，该列的值可以是任意值。
14. `passive_deletes`：指定是否在删除该列时将其设置为被动删除。默认值为`False`。
15. `passive_updates`：指定是否在更新该列时将其设置为被动更新。默认值为`False`。

## 表关系

在表关系中，往往都是通过`ForeignKey`来进行约束和关联的。

在多表删除时，有如下完整性约束（`ondelete`选项）：

1. RESTRICT：删除外键表数据时，会被阻止。
2. NO ACTION：删除外键表数据时，子表什么都不做。
3. SET NULL：删除外键表数据时，子表关联的数据会设置为null。
4. SET DEFAULT：删除外键表数据时，子表关联的数据会设置为默认值。
5. CASCADE：删除外键表数据时，子表关联的数据也会删除。

### 一对多

在一对多表关系中，通常在"多"的表建立`ForeignKey`外键来关联"一"的表。

即："一" == 主表；"多" == 子/从表。

```python
# "一"的表、主表
class Users(Base):
    __tablename__ = "users"
    uid = Column(Integer, primary_key=True)
    username = Column(String(50))
    password = Column(String(50))

    def __repr__(self):
        return f"<Users: uid={self.uid} username={self.username} password={self.password}>"


# "多"的表、子表
class News(Base):
    __tablename__ = "news"
    nid = Column(Integer, primary_key=True)
    title = Column(String(50))
    content = Column(String(50))
    # 与主表建立外键
    uid = Column(Integer, ForeignKey("users.uid", ondelete="CASCADE"), nullable=False)
		
    # 与Users表建立一对多关系
    user = relationship("Users", backref="news")

    def __repr__(self):
        return f"<News: nid={self.nid} title={self.title} content={self.content} uid={self.uid}>"

def create_data():
    user1 = Users(username="小红", password="123456")
    user2 = Users(username="小白", password="123456")
    
    # 通过表关系进行添加，而不是外键uid
    news1 = News(title="标题1", content="内容1", user=user1)
    news2 = News(title="标题2", content="内容2", user=user1)

    session.add_all([user2, news1, news2])
    session.commit()
      
def query_relationship():
    # 正向查询：子查父
    new = session.query(News).first()
    print(new.user)

    # 反向查询：父查子
    user = session.query(Users).first()
    print(user.news)
```

> 通过使用`user = relationship("Users", backref="news")`建立关系后，子表获取主表数据时，可以直接通过该属性名获取；主表获取子表数据时，可以通过`backref`属性值获取。

### 一对一

在一对一表关系中，可以在其中任意一张表中建立`ForeignKey`外键来关联另外一张表。

```python
from sqlalchemy.orm import relationship, backref

class UserInfo(Base):
		...
    # 与主表建立外键
    uid = Column(Integer, ForeignKey("users.uid", ondelete="CASCADE"), nullable=False)
    
    # 与Users表建立一对一关系
    user = relationship("Users", backref=backref("user_info", uselist=False))
```

> 在一对多的基础上，`backref`属性值将使用`backref()`函数（可以反向指定关联表属性），并指定`uselist=False`即可。

### 多对多

在多对多表关系中，通常需要一张中间表来绑定两者的关系，并且使用两表的外键作为复合主键。

```python
from sqlalchemy import Column, Integer, String, ForeignKey, Table

# 创建中间表
tags_news = Table(
    "tags_news",
    Base.metadata,
  
  	# 两张表的外键，并指定为复合主键
    Column("tid", Integer, ForeignKey("tags.tid", ondelete="CASCADE"), primary_key=True),
    Column("nid", Integer, ForeignKey("news.nid", ondelete="CASCADE"), primary_key=True)
)

class Tags(Base):
  	...
    # 与News建立多对多关系
    news = relationship("News", backref="tags", secondary="tags_news")
```

> 在一对多的基础上，任一表使用`relationship`建立关系，并使用`secondary`参数来指定中间表即可。

### relationship

以下是常用参数：

1. `backref`：它允许你在关系的另一端创建一个反向引用。这使得在查询和操作数据时更加方便和高效。
2. `lazy`：它控制了关系数据的加载策略。根据你的需求和性能要求，你可以选择不同的加载策略来优化查询性能。
   1. `select`（默认）：对于多对一和一对一关系，使用子查询加载关联的对象。对于一对多关系，使用外链接加载关联的对象。`select`加载策略通常会在查询时生成更多的SQL语句，但可以确保只加载需要的数据。
   2. `joined`：对于多对一和一对一关系，使用JOIN操作加载关联的对象。对于一对多关系，使用外链接加载关联的对象。`joined`加载策略通常会在查询时生成一个更复杂的SQL语句，但可以减少查询次数，从而提高查询性能。
   3. `subquery`：对于多对一和一对一关系，使用子查询加载关联的对象。对于一对多关系，也使用子查询加载关联的对象。`subquery`加载策略通常会在查询时生成更多的SQL语句，但可以在需要时通过子查询来过滤关联的对象。
   4. `no_load`：这个值表示在访问关联的对象时，不会立即加载它们。相反，当你尝试访问关联的对象时，会触发一个延迟加载操作，从而只在需要时加载数据。
   5. `dynamic`：这个值表示在访问关联的对象时，会根据需要自动选择加载策略。在第一次访问关联的对象时，会使用`select`加载策略。如果关联的对象在查询结果中已经存在，那么在使用`joined`或`subquery`加载策略时，会使用已加载的对象，而不是重新查询数据库。
3. `cascade`：用于定义在对关系中的一个对象进行操作时应该如何级联到其他对象。它可以帮助你在删除或更新对象时自动处理关联的数据，从而简化数据操作。
   1. `save-update`（默认）：当关系中的一个对象被保存或更新时，关联的对象也会自动保存或更新。
   2. `merge`：当关系中的一个对象被合并时，关联的对象也会自动合并。
   3. `delete`：当关系中的一个对象被删除时，关联的对象也会自动删除。
   4. `delete-orphan`：当关系中的一个对象被删除时，如果关联的对象没有其他关系引用它们，那么这些对象也会自动删除。
   5. `refresh-expire`：当关系中的一个对象被刷新或过期时，关联的对象也会自动刷新或过期。
   6. `all`：包含所有上述级联操作。当你想要在对关系中的一个对象进行操作时，自动级联到所有关联的对象时，可以使用这个值。
   7. `none`：不进行任何级联操作。如果你不想在对关系中的一个对象进行操作时自动级联到关联的对象，可以使用这个值。
4. `order_by`：用于指定关系中对象的排序方式。在显示数据时，你可能需要按照特定的顺序来排列关联的对象，这时可以使用`order_by`参数。
5. `primaryjoin`和`secondaryjoin`：用于指定关系的连接条件。它们在构建复杂的查询和关系时非常有用，可以帮助你灵活地定义关系的连接条件。
6. `uselist`：用于指定关系是否为一对多或多对一。在定义关系时，你需要根据实际情况来设置`uselist`参数，以确保关系的正确性。
7. `collection_class`：用于指定关系集合的类型。在某些情况下，你可能需要使用特定的集合类型来存储关联的对象，这时可以使用`collection_class`参数。
8. `remote_side`：用于指定关系中的“远程”属性。在多对多关系中，它可以帮助你指定关系的另一端的属性，从而实现正确的关联。
9. `sync_backref`：用于指定是否在关系的另一端自动同步关系。在某些情况下，你可能需要禁用自动同步功能，以便手动控制关系的同步。
10. `foreign_keys`：用于指定关系中使用的外键。在某些情况下，你可能需要手动指定外键，以确保关系的正确性。

# 增查改删

创建session：

```python
from sqlalchemy.orm import DeclarativeBase, sessionmaker

Session = sessionmaker(bind=engine)
session = Session()
```

## 增

```python
def add_user(username, password):
    # 创建数据对象
    user = User(username=username, password=password)
    # 添加单条数据
    session.add(user)
    # 添加多条数据
    # session.add_all([user, ])
    # 提交
    session.commit()
    return user
```

## 查

```python
def query_user(username):
    user = session.query(User).filter(User.username == username).first()
    return user
```

### query

返回一个Query对象，在query中，可以传递两种参数：

1. 模型类/模型属性：将查询结果返回全字段/部分字段。
2. 聚合函数(`func`)：将查询结果进行处理后再返回。

常用聚合函数如下：

* `func.count()`：计算一组值的数量。
* `func.sum()`：计算一组值的总和。
* `func.avg()`：计算一组值的平均值。
* `func.max()`：计算一组值的最大值。
* `func.min()`：计算一组值的最小值。
* `func.random`：用于生成一个随机数。

Query对象常用方法如下：

* `filter()`：过滤查询结果。

```python
query.filter(User.age == 18)
```

* `filter_by()`：指定关键字过滤查询结果。

```python
query.filter_by(age = 18)
```

* `order_by()`：对查询结果进行排序。

```python
# 升序，默认
query.order_by(User.age.asc())
# 降序
query.order_by(User.age.desc())
```

* `limit()`：限制查询结果的数量。

```python
query.limit(10)
```

* `offset()`：设置查询结果的偏移量。

```python
query.offset(5)
```

* `slice()`：返回查询结果的一个切片。

```python
query.slice(10, 20)
```

* `group_by()`：给查询结果分组。

```python
query.group_by(User.age)
```

* `having()`：给分组结果过滤。

```python
query.group_by(User.age).having(User.age == 18)
```

* `join()`：在查询中添加一个INNER JOIN操作。

```python
query.join(Address, User.id == Address.user_id)
```

* `outerjoin()`：在查询中添加一个OUTER JOIN操作。

```python
query.outerjoin(Address, User.id == Address.user_id)
```

* `subquery()`：将查询对象转换为子查询。

```python
query.subquery()
```

* `distinct()`：给查询结果去重。

```python
query.distinct()
```

* `cout()`：返回查询结果的数量。

```python
query.count()
```

* `first()`：返回查询结果的第一条数据。

```python
query.first()
```

* `one()`：返回查询结果的唯一数据（为空/多条会报错）。

```python
query.one()
```

* `all()`：返回查询结果的所有数据。

```python
query.all()
```

* `with_entities()`：返回查询结果的指定字段。

```python
query.with_entities(User.name)
```

### filter

返回一个Query对象，使用filter包含了许多用法进行数据的过滤。

* and/or/not

```python
from sqlalchemy import and_, or_, not_

# 与
session.query(User).filter(and_(User.username == "苦艾酒", User.tag == "多金")).all()

# 或
session.query(User).filter(or_(User.username == "苦艾酒", User.tag == "帅气")).all()

# 非
session.query(User).filter(not_(User.username == "苦艾酒")).all()
```

* equal/not equal

```python
# 属性相等
session.query(User).filter(User.username == "admin").all()

# 属性不相等
session.query(User).filter(User.username != "admin").all()
```

* null/not null

```python
# 属性为空
session.query(User).filter(User.tag.is_(None)).all()

# 属性不为空
session.query(User).filter(User.tag.isnot(None)).all()
```

* in/not in

```python
# 集合内
session.query(User).filter(User.username.in_(['苦艾酒', '琴酒'])).all()

# 集合外
session.query(User).filter(not_(User.username.in_(['苦艾酒', '琴酒']))).all()
```

* lt/le、gt/ge、between

```python
# 小于/小于等于
session.query(User).filter(User.money <= 100).all()

# 大于/大于等于
session.query(User).filter(User.money >= 100).all()

# 指定区间
session.query(User).filter(User.money.between(100, 200)).all()
```

* contains/startswith/endswith

```python
# 包含
session.query(User).filter(User.username.contains("艾")).all()

# 匹配开头
session.query(User).filter(User.username.startswith("苦")).all()

# 匹配结尾
session.query(User).filter(User.username.endswith("酒")).all()
```

* like/ilike

```python
# 不区分大小写
user = session.query(User).filter(User.username.like("%酒%")).all()

# 区分大小写（部分数据库支持）
user = session.query(User).filter(User.username.ilike("%酒%")).all()
```

## 改

```python
def update_user(username, password):
  	# 查询修改的数据
    user = session.query(User).filter_by(username=username).first()
    # 修改某字段的数据
    user.password = password
    # 提交
    session.commit()
    return user
```

## 删

```python
def delete_user(username):
  	# 查询删除的数据
    user = session.query(User).filter_by(username=username).first()
    # 删除数据
    session.delete(user)
    # 提交
    session.commit()
    return user
```

# Flask-SQLAlchemy

[Flask-SQLAlchemy](https://flask-sqlalchemy2.readthedocs.io/zh-cn/latest/)封装于SQLAlchemy的Flask插件。

* 安装

```bash
pip3 install flask-sqlalchemy
```

* 连接

```python
from flask_sqlalchemy import SQLAlchemy

# 配置数据库URL
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:20010201@localhost:3306/flask_demo'
# 关闭追踪修改
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
# 创建连接
db = SQLAlchemy(app)
```

* 定义模型

```python
class Users(db.Model):
    __tablename__ = "t_users"
    uid = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50))
    password = db.Column(db.String(50))

    def __repr__(self):
        return f"<Users: uid={self.uid}, username={self.username}, password={self.password}>"


class News(db.Model):
    __tablename__ = "t_news"
    nid = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(50))
    content = db.Column(db.Text)
    uid = db.Column(db.Integer, db.ForeignKey("t_users.uid"))

    users = db.relationship("Users", backref="news")

    def __repr__(self):
        return f"<News: nid={self.nid}, title={self.title}, content={self.content}, uid={self.uid}>"

```

* 增删表

```python
with app.app_context():
    # 创建表，需要能获取到模型类（导入即可）
    db.create_all()
    # 删除表
    db.drop_all()
```

* CRUD

```python
# 创建数据
user = Users(username="admin", password="123456")
news = News(title="新闻标题", content="新闻内容", uid=1)
db.session.add_all([user, news])
db.session.commit()

# 查询数据
# 单表查询
user = Users.query.filter_by(username="admin").first()
# 多表查询
res = db.session.query(Users, News).join(News, Users.uid == News.uid).all()

# 更新数据
user = Users.query.filter_by(username="admin").first()
user.password = "654321"
db.session.commit()

# 删除数据
user = Users.query.filter_by(username="admin").first()
db.session.delete(user)
db.session.commit()
```

# Alembic

[Alembic](https://alembic.sqlalchemy.org/en/latest/)是一个用于ORM的迁移映射工具，可以更方便的修改表结构。

* 安装

```bash
pip3 install alembic
```

* 初始化

```bash
alembic init dirName
```

> 一般`dirName`取`migrations`。
>
> 初始化完成后，会创建一个包含*env.p*脚本的目录及*alembic.ini*配置文件。

* 配置

alembic.ini

```ini
# 配置数据库URL
sqlalchemy.url = mysql+pymysql://root:20010201@localhost:3306/flask_demo
```

env.py

```python
target_metadata = db.metadata
```

* 迁移

```bash
alembic revision --autogenerate -m "init"
```

> 第一次会生成`alembic_version`版本表，用于记录模型与数据库版本。
>
> 会在*migrations/versions*目录下产生一个版本映射py文件。

* 映射

```bash
alembic upgrade head
```

> 会将迁移的版本文件映射到数据库进行更新。

# Flask-Migrate

[Flask-Migrate](https://flask-migrate.readthedocs.io/en/latest/index.html)封装于Alembic的Flask插件，因此除了指令与配置方法不一样外，其他都与Alembic一致。

* 安装

```python
pip3 install flask-migrate
```

* 配置

```python
# 配置数据库URL
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:20010201@localhost:3306/flask_demo'
# 关闭追踪修改
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
# 创建连接
db = SQLAlchemy(app)
# 创建迁移
migrate = Migrate(app, db)
```

* 初始化

```bash
flask db init
```

* 迁移

```bash
flask db migrate -m '注释'
```

**注意**：必须要让app可以获取到模型类（导入即可）。

* 映射

```bash
flask db upgrade
```

* 回退版本

```bash
flask db downgrade version_
```

