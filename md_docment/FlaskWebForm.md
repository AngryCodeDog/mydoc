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





```
{% import "bootstrap/wtf.html" as wtf %}
{{ wtf.quick_form(form) }}
```



