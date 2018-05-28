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
