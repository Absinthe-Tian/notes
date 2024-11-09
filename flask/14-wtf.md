---
title: 14.WTF表单验证
date: 2023-12-22 18:12:40
categories: Flask框架
tags:
  - form
  - wtf
  - 文件上传
  - 国际化
  - CSRF
---

[Flask-WTF — Flask-WTF Documentation (1.2.x)](https://flask-wtf.readthedocs.io/en/latest/)

封装于WTForms，包含表单验证、模板渲染、CSRF、文件上传、国际化等功能。

安装：

```python
pip3 install Flask-WTF
```

# 表单验证

## 基本使用

定义验证类：

```python
from wtforms import Form, StringField
from wtforms.validators import Length, EqualTo


class RegisterForm(Form):
    username = StringField(label='Username', validators=[Length(min=4, max=25)])
    password = StringField(label='Password', validators=[Length(min=6, max=35)])
    confirm = StringField(label='Repeat password', validators=[EqualTo('password')])
```

使用验证：

```python
class RegisterView(MethodView):
    def get(self):
        return render_template("register.html")

    def post(self):
        form = RegisterForm(request.form)
        if form.validate():
            return "验证通过"
        else:
            return f"验证失败：[{form.errors}]"
```

## 常用字段

1. StringField：用于输入字符串类型的数据。
2. TextAreaField：用于输入多行文本数据。
3. PasswordField：用于输入密码数据，输入的值会被隐藏。
4. IntegerField：用于输入整数类型的数据。
5. FloatField：用于输入浮点数类型的数据。
6. DecimalField：用于输入十进制数类型的数据。
7. BooleanField：用于输入布尔类型的数据，通常用于复选框或单选框。
8. RadioField：用于输入单选框类型的数据。
9. SelectField：用于输入下拉列表类型的数据。
10. SelectMultipleField：用于输入多选下拉列表类型的数据。
11. HiddenField：用于输入隐藏类型的数据。
12. FileField：用于输入文件类型的数据，通常用于上传文件。
13. SubmitField：用于提交表单数据。

## 常用验证器

1. DataRequired：验证字段是否为空。
2. Length：长度是否在指定的范围内。
3. InputRequired：验证字段数据为必填项。
4. EqualTo：验证字段是否等于另一个字段的值。
5. Email：验证字段是否为有效的电子邮件地址。
6. NumberRange：验证字段的值是否在指定的数值范围内。
7. Regexp：使用正则表达式验证字段的值。
8. UUID：验证字段的值是否是有效的UUID。
9. URL：验证字段是否为有效的URL。
10. IPAddress：验证字段是否为有效的IP地址。
11. MacAddress：验证字段是否为有效的MAC地址。
12. Optional：标记字段为可选。
13. AnyOf：验证字段的值是否符合指定的选项之一。

## 自定义验证

```python
from wtforms.validators import Length, ValidationError

class LoginForm(Form):
  	...
    code = IntegerField(label='validate Code', validators=[Length(min=4, max=4)])
    
    def validate_code(self, field):
        if field.data != server_code:
            raise ValidationError("验证码错误!")
```

1. 重写验证方法，方法为`validate_fieldName(self, field)`。
2. 未通过验证时，需要抛出`ValidationError`。

# 渲染模板

1. 将Form对象传入模板。
2. 在模板中使用。

```html
{% raw %}
{{ form.username.label }}
{{ form.username() }}
{% endraw %}

<!-- 等价于 -->
<label for="username">Username</label>
<input type="text" name="username" id="username" minlength="4" maxlength="25">
```

# CSRF

Flask-WTF提供了CSRF保护功能，可以防止跨站请求伪造攻击。

## 配置

```python
from flask_wtf import CSRFProtect

app.secret_key = "your-secret-key"
app.config['WTF_CSRF_ENABLED'] = True
csrf = CSRFProtect(app)
```

## 添加csrf_token

```html
<form action="/upload/" method="post" enctype="multipart/form-data">
    <input type="hidden" name="csrf_token" value="{% raw %}{{ csrf_token() }}{% endraw %}">

    <input type="file" name="pic" id="pic" required>
    <input type="submit" value="提交">
</form>
```

> 也可以在表单类中定义`CSRFTokenField()`字段，在渲染模板时`{% raw %}{{ form.csrf_token }}{% endraw %}`进行使用。

# 文件安全

## 验证

要求上传的文件不能为空，且文件后缀只能为jpg/png/gif。

```python
from flask_wtf.file import FileRequired, FileAllowed
from wtforms import Form, FileField

class UploadFileForm(Form):
    pic = FileField("File", validators=[FileRequired(), FileAllowed(['jpg', 'png', 'gif'])])
```

## 上传

**注意**：需要在表单中添加`enctype="multipart/form-data"`属性。

```python
from werkzeug.utils import secure_filename
from wtf_utils import UploadFileForm
from werkzeug.datastructures import CombinedMultiDict

class UploadFileView(MethodView):
    def get(self):
        return render_template("upload.html")

    def post(self):
        try:
            form = UploadFileForm(CombinedMultiDict((request.files, request.form)))
            if form.validate():
                # file = request.files.get("pic")
                file = form.pic.data
                filename = secure_filename(file.filename)
                file.save(os.path.join(UPLOAD_FOLDER, filename))
            else:
                return f"上传失败！\n {form.errors}"
        except Exception as error:
            return f"上传失败！\n {error}"
        return "上传成功！"
```

## 下载

```python
from flask import send_from_directory

@app.get('/download/<filename>/')
def download_view(filename):
    return send_from_directory(UPLOAD_FOLDER, filename)
```

# 国际化

flask-wtf配合[Flask-Babel](http://www.pythondoc.com/flask-babel/)可以让表单label国际化。

```python
from flask_babel import lazy_gettext as _
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired

class MyForm(FlaskForm):
    name = StringField(_('Name'), validators=[DataRequired()])
    submit = SubmitField(_('Submit'))
```

