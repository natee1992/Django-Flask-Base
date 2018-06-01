## day1
flask 入门
pip install flask <br>
简介：flask是小的python web框架 只提供了一个强健的核心，其它功能都是提供扩展库去实现的，其它功能都是提供扩展库去实现的
```python

@app.route('/')  # 路由
def index():  # 视图函数
    return 'hello'

#启动参数
app.run()
debug  默认为Flase True 调试模式，自动加载代码
port  指定端口号
host  指定主机名  host='0.0.0.0'
thredad 线程  Ture
###
if __name__ == '__main__':
    app.run(host='0.0.0.0', port='5001', debug=True, threaded=True)
###

#视图函数传参
（1）使用无参路由
@app.route('/')
def index(): #视图函数
    return 'hello'
    
（2）使用参数路由
@app.route('/find/<name>')
def findGirl(name):
    return '这个人是{0}'.format(name)
    
 (3) 参数的类型
#限制参数类型<int:arg>  <float:arg>
#默认字符串
@app.route('/arg_type/<int:arg>')
 def arg_type(type(arg)):
    print(type(arg))
    
#将路由地址后所有的字符都匹配为字符串
@app.route('/arg_type/<path:arg>')
 def arg_type(type(arg)):
    print(type(arg))   
    print(arg)
************************************
输入：http://127.0.0.1:5001/arg_type/2/ssss
输出： str  
      2/ssss
#参数类型
（1）string 默认字符串
（2）int 限制传递进来的参数为 int类型 不做类型的转换
（3）float 限制传递进来的参数为 float类型 不做类型的转换
（4）path 将请求的路由地址后面的所有参数 都认为是一个参数的值，不再做路由的查找

#路由地址传递多个参数
@app.route('/many_arg/<a>_<b>')
@app.route('/many_arg/<a>/<b>')
def many_arg(a,b):
    return '传递多个参数第一个 {0} 第二个 {1} '.format(a,b)

----------------------------------------------------
#####注意事项
1. 路由的末尾加/
2.int:参数名称
3.path其实也是字符串类型  只是参数的/不再认为是路由的分隔符

####视图的响应

(1) 手动制造响应
return,404
return 返回类型不能为int类型

###重定向
from flask import redirect
@app.route('/redirect/')
def myRedirect():
    return redirect('/')

@app.route('/redirect/')
def myRedict():
    # return '正常请求'
    # return redirect('/many_arg/1/2')
    print('-----------------------------------------')
    # print(url_for('index')) #index是视图函数的名
    # print(url_for('index'))
    print(url_for('many_arg', a=1, b=2))
    return redirect(url_for('many_arg', a=1, b=2))
   #输出结果：-----------------------------------------
              /many_arg/1/2

####abort  抛出指定的状态码，http指定状态码
#异常捕获
@app.route('/look_meizi')
def look_meizi():
    abort(404)


# 捕获指定的状态码
@app.errorhandler(404)
def page_not_found(e):
    return '你请求的地址被外星人带走了'
    
----------------------------------------------
#请求与响应
current_app 当前app实例化的代理对象，获取当前app的所有公共配置

@app.route('/current_app')
def myGetConfig():
    # 拿到app所有的配置
    return current_app.config['secret_key']
----------------------------------------------
#全局变量g
#在一次请求中 可以全局获取
例子和使用详见钩子函数或者模版

------------------------------------------------
#request
包含了所有的请求信息
@app.route('/request')
def get_http_message():
    print('----------------------------')
    print(request.url)
    print(request.base_url)
    print(request.host_url)
    print(request.path)
    print(request.method)
    print(request.args)
    print(request.args.get('name', 'none'))
    # 获取表单传递过来的数据
    print(request.form)
    # 获取表单文件上传的数据
    print(request.files)
    print('获取cookies', request.cookies)
    print('请求头', request.headers)
    return 'request对象'
-------------------------------------------
@app.route('/get_arg')
def get_arg():
    print(request.args)
    print(request.args.get('b'))
    print(request.args.getlist('a'))
    return '获取多个get传参'

## 会话控制  cookie和session
#原因是：http协议是无状态协议
cookie
response.set_cookie(
    key,#键
    value，#值
    max_age,#以秒为单位的cookie存活时间
    expires,失效时间需要datetime对象
    path = '/',储存的路径
)


@app.route('/get_cookie')
def get_cookie():
    response = make_response('设置cookie')
    # 不设置存活时间，默认为当前浏览会话结束，也就是关闭当前浏览器，并不是标签页
    return request.cookies.get('name', '什么都没有')
    
#移除cookie
    response.delete_cookie('name')
```
session
    cookie每次携带着唯一的sessionid来进行请求访问，服务端根据sessionid来区分不同用户的请求，所以sesion是基于cookie的，服务端会给米一个请求创建一个session对象
（1）设置session
```python
@app.rout('/set_session')
def set_session():
    session['username'] = 'lisi'
    return '设置session'
```
（2）获取session
```python
@app.route('/get_session')
def get_session():
    return session.get('username')
```
(3) 删除session
```python
@app.route('/del_session')
def del_session():
    return session.pop('username')
```
（4）设置过期时间
```python
@app.route('/set_session')
def set_session():
    session['username'] = 'lisi'
    session_life_time = timedelta(minutes=10)
    # 设置session的持久化
    session.permanent = True
    app.permanent_session_lifetime = session_life_time
    return '设置session'
```
### flask-script 扩展库
*概念：一个flask终端的运行解析器，因为在项目完成之后，不应该有任何的改动，所以需要接种终端来完成不同启动项的配置*
```
-h 主机名
-p 指定端口号
-threader 开启多线程
-d 开启调试模式
-r 代码修改后重新加载
```
### blueprint蓝图
*当代码越来月复杂时，视图文件不应该放在一个文件中，使用蓝本将不同模块功能的视图函数做成一个视图函数，放在一个文件中*
```python
from flask import Flask, session, request, Blueprint
app.register_blueprint(mysession，url_prefix = '/user')
from mysession import mysession
--------------------------------------------------------
#在mysession中
from flask import Blueprint, session
# 设置session
mysession = Blueprint('mysession', __name__)


@mysession.route('/set_session')
def set_session():
    session['username'] = 'lisi'
    session_life_time = timedelta(minutes=10)
    # 设置session的持久化
    session.permanent = True
    app.permanent_session_lifetime = session_life_time
    return '设置session'

# 获取session


@mysession.route('/get_session')
def get_session():
    return session.get('username')

# 删除session


@mysession.route('/del_session')
def del_session():
    return session.pop('username')

```

*两个架构：<br>
* B/S
* C/S

## day2 
####钩子函数
*类似与django中间件 在使用的时候需要使用装饰器*
在主程序中使用
```python
#钩子函数                     |      #功能描述
before_first_request         |      第一次请求之前
before_request               |       每次请求之前
after_request                |       每次请求之后 前提是没有出现异常
teardown_request             |       每次请求之后  即使出现异常

```
#####钩子函数使用在蓝本中
```python
#钩子函数                         |      #功能描述
before_app_first_request         |      第一次请求之前
before_app_request               |       每次请求之前
after_app_request                |       每次请求之后 前提是没有出现异常
teardown_app_request             |       每次请求之后  即使出现异常

```
##模版引擎
***
按照一定的规则 负责展示并渲染一个html页面给用户 模版给拱了替换的规则<br>
**使用的模版引擎**
*jinja2 由flask开发*
###一、模版的使用
*（1）创建模板目录*
```python
project/
       templates/
                模板文件
```
*（2）创建模板目录*
1.变量<br>
{{ 变量名称 }}
2.标签
{% 标签名称 %}
***
###过滤器
```python
var|abs  绝对值
var|default()  默认值   只有未定义才采取默认值   可加参数 boolean=True
var|first 取出var值中第一个值
            绝对值{{ data.num|abs }}

            默认值{{ data.bool|default('default') }}

            默认值{{ data.none|default('default',boolean=True) }}

            第一个值{{ title|first }}

            格式化值{{ '我叫%s,我今年%s岁,我的存款为%s'|format('雷神',18,200) }}

            长度{{ title|length }}

            合并字符串{{ ['a','b']|join('x') }}

            safe{{ data.h3|safe }}

            int{{ data.num|int }}

            float{{ data.num|float }}


```
###三、标签
*语法{% tag %}
```python
 <h2>分支</h2>
    {% if data.bool %}
        {{data.bool}}为真
    {% else%}
        {{ data.bool }}为假
    {% endif %}
  {% for k,v in data.items()%}
    {{ k}}{{v}}
  {% endfor%}
```
###文件的包含
主题结构
    {% include('模版名称') %}
## day3
提交表单 使用wtf第三方
## day4
文件上传与邮件发送
## sqlalchemy 模型
*安装pip install flask-sqlalchemy*
**orm使用的好处**
1.增加sql的重复使用率  <br>
2.使表容易阅读   <br>
3.可移植性      <br>
### 一、原生数据库
#### （1）新建一个数据库
```python
create database if not exists 库名 character set utf8;
alter database 库名 character set utf8;
alter table 表名 character set utf8;
alter table 表名 modify 字段名 字段类型 可选约束条件 character set utf8
```
#### (2) 安装数据库模块
```python
pip install pymysql
```
#### （3）安装flask-sqlalchemy
```python
pip install flask-sqlalchemy
```
#### (4) 配置路径
```
DB_URI= 'mysql+pymql://root:password@host:port/demo'
```
```python
from sqlalchemy import create_engine

HOST = '127.0.0.1'
USERNAME = 'root'
PASSWORD = '12345'
DATABASE = 'demo'
PORT = '3306'
DB_URI = 'mysql+pymysql://{}:{}@{}:{}/{}'.format(
    USERNAME, PASSWORD, HOST, PORT, DATABASE)
engine = create_engine(DB_URI)

with engine.connect() as db:
    data = db.execute('select * from user')
    db.execute('delete from user where id=1')

```
## 二、在flask中使用ORM模型
**配置**
```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask('__name__')
# 实例化ORM
db = SQLAlchemy(app)
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+mysql://root:12345@127.0.0.1:3306/demo'


@app.route('/')
def index():
    return '首页'


if __name__ == '__main__':
    app(debug=True)

```
## 三、设计表模型
**字段类型**
<table>
<td>id</td>
</table>
    

