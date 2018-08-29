## Flask web表单



Flask-bootstrap给我们提供了美观的样式文件，它集成了bootstrap的样式，所以我们可以用它来写出和bootstrap一样的页面。同时我们在开发网页过程中必然会面临要生成web表单



```python
# encoding=utf-8

from flask_wtf import Form
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Length, Email



class LoginForm(Form):
    email = StringField('Email', validators=[DataRequired(), Length(1, 64),Email()])
    password = PasswordField('Password', validators=[DataRequired()])
    remember_me = BooleanField('Keep me logged in')
    submit = SubmitField('Log In')
```



app.py

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
    return render_template('auth/index.html', form=form)


if __name__ == '__main__':
    app.run(debug=True, port=5001)

```



base.html

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

login.html

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







