## Flask web表单



## 三、Flask web表单



Flask可以直接生成一个web表单返回，并坐一些表单的验证和限制。

Flask-WTF 能保护所有表单免受跨站请求伪造（Cross-Site Request Forgery， CSRF）的攻击。恶意网站把请求发送到被攻击者已登录的其他网站时就会引发 CSRF 攻击。  

为了实现 CSRF 保护， Flask-WTF 需要程序设置一个密钥。 Flask-WTF 使用这个密钥生成 加密令牌，再用令牌验证请求中表单数据的真伪。  

设置密钥的方式如下：

```python
app = Flask(__name__)
app.config['SECRET_KEY'] = 'hard to guess string'
```

app.config是一个字典，可用来存储框架、扩展和程序本身的配置变量。  密钥要设置有难度的字符串。



我们来个简单的例子，安装Flask-WTF，

```
pip install flask-wtf
```

然后创建一个LoginForm.py文件并写如下代码 ：

```python
# encoding=utf-8

from flask_wtf import Form
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Length, Email

class LoginForm(Form):
    email = StringField('Email', validators=[DataRequired(), Length(1, 64),Email()])
    password = PasswordField('Password', validators=[
        DataRequired(), EqualTo('password2', message='Passwords must match.')])
    password2 = PasswordField('Confirm password', validators=[DataRequired()])
    remember_me = BooleanField('Keep me logged in')
    submit = SubmitField('Log In')
```
password和password2两个设置了密码匹配验证，服务端需要写`form.validate_on_submit()`即可返回验证结果，界面也会相应提示，如果不写`form.validate_on_submit()`那就不会验证的。

在程序入口文件app.py中写入如下内容：

```python
# encoding=utf-8

from flask import Flask, render_template
from flask_bootstrap import Bootstrap
from forms.LoginForm import LoginForm

app = Flask(__name__, template_folder='templates')
bootstrap = Bootstrap(app)
app.config['SECRET_KEY'] = 'hard to guess string'


@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    # 这里要做验证，否则前端不会有提示的
    if form.validate_on_submit():
        return 'login succeed'
    return render_template('auth/index.html', form=form)


if __name__ == '__main__':
    app.run(debug=True, port=5001)

```



我们用flask-bootstrap创建一个父页面，坐一些基本的架子搭建：

```html
{% extends "bootstrap/base.html" %}
{% block title %}Flasky{% endblock %}
{% block navbar %}
    <div class="navbar navbar-inverse" role="navigation">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle"
                        data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="/">Flasky</a>
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li><a href="/">Home</a></li>
                </ul>
            </div>
        </div>
    </div>
{% endblock %}
{% block content %}
    <div class="container">
        <div class="page-header">
            <h1>Hello, {{ name }}!</h1>
        </div>
    </div>
{% endblock %}

```

最后我们新建一个login.html，我们使用最简单的表单渲染方式`{{ wtf.quick_form(form) }}`生成页面：

```
{% extends "base.html" %}
{% import "bootstrap/wtf.html" as wtf %}
{% block title %}Flasky - Login{% endblock %}
{% block content %}
    <div class="container">
    <div class="page-header">
        <h1>Login</h1>
    </div>
    <div class="col-md-4">
        {{ wtf.quick_form(form) }}
    </div>
    </div>
{% endblock %}
```