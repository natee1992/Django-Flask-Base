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


    
*两个架构：<br>
* B/S
* C/S

## day2 
模板
jinja2
## day3
提交表单 使用wtf第三方
## day4
文件上传与邮件发送
## day5
model模型
