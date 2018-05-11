*开发环境pycharm mysql*
## 扩展auth_user表 
```
auth_user表继承于django.contrib.auth.models中的AbstractUser
继承时不要修改username字段
重定义AUTH_USER_MODEL
在执行makemigrations前，由于admin中很多依赖于auth_user，可能迁移出错，所以将数据库中除auth_user表全部删除后再执行数据库迁移
CharField类型必须指定max_length
```
***
## 设计app
设计表时避免表的循环引用
```
例如在users的models中有*UserCourse*, 在course的models中有*CourseComment*
 在users中的UserCourse中的course 需要import course
 在course中的CourseComment中的user 需要import user
 
避免循环import，循环import会出错
```
解决思路：app分层，建立上层app引用下层<br>
同时将只于user衔接的表定义在user中
***
## 设计表tips
```
引入包分3部分：
第一部分，python自带包
第二部分，第三方库包
第三部分，自定义导入模块
每部分之间用一行空格分隔

datatime.now()是生成表的时间
datatime.now 是表对象实例化的时间

django没有一对多，多对一的映射，只有外键的映射
model可以继承

```
***
## 后台管理
```
特点：
 权限管理
 少前端样式
 快速开发
```
***
## settings
```
USE_TZ = False 本地时间

```
***
## xadmin 导入设置
1. pip安装
```

虚拟环境中pip install git+git://github.com/sshwsfc/xadmin.git
在settings中注册xadmin 以及依赖 crispy_forms

```
2. 源码安装
```
git 搜索xadmin 下载源码安装包
拷贝源码中xadmin文件
在项目中新建extra_apps package
将xadmin放入extra_apps 中
在pycharm中souce extra_apps文件夹
在settings中加入搜索位置
improt sys
sys.path.insert(0,os.path.join(BASE_DIR,'extra_apps'))
```
3. app中注册model
```python
# xadmin自动发现userprofile用户表,其余表需要注册到adminx.py中，所以需要在每个app下新建adminx.py
import xadmin
from .model import *
class EmailVerifyRecordAdmin(object):  # xadmin继承于object类
   list_display=['code','email','send_type','send_time']  # 自定义后台显示字段
   search_fields = ['code','email','send_type']   # 自定义搜索类型
   list_filter = ['code','email','send_type','send_time']  # 自定义筛选字段
   ######## list_fitter = ['course__name'] 外键的筛选需要两个下划线  ########
xadmin.site.register(EmailVerifyRecord,EmailVerifyRecordAdmin)   # 注册emailverifyrecord表到xadmin后台

```
4. xadmin全局配置
在基础app的adminx下配置
```python 
from xadmin import views


class BaseSetting(object):
    enable_themes = True
    use_bootswatch = True
    # 以上两个为主题功能

class GlobalSettings(object):
    site_title = 'NATEE-后台管理系统'
    site_footer = 'NATEE.site'
    # 以上为页头和页脚配置
    menu_style = 'accordion'
    # 以上为左侧导航样式
    
    
xadmin.site.register(views.BaseAdminView,BaseSetting) # 注册主题功能
xadmin.site.register(views.CommAdminView,GlobalSettings) # 注册全局功能

'''
改变右侧英文为中文：
  在app的apps文件中加入：
    verbose_name = '自定义名字'
  每个app文件__init__：
    default_app_config = 'app.apps.name'
''' 
```
***
5. templates静态页面配置
不用配置views方面，直接匹配静态页面。
```python
 # 在根目录的urls文件中配置templates方法，直接导入首页
 from django.views.generic import TemplateView
 #在urlpatterns中添加首页路由：
 url(r'^$',TemplateView.as_view(template_name='index.html'),name='index')
```
***
6. 用户登录配置
```python
# 将登陆html中的form中的action指向urls配置的地址
# 配置users中的views方法 
   #通过django自带认证方法认证
  from django.contrib.auth import authenticate,login
    	if request.method == 'POST':
          user_name = request.POST.get('username','')
          pass_word = request.POST.get('password','')
          user = authenticate(user_name, pass_word)
          if user is not None:
                login(request,user)  # 调用login方法验证登陆
                return render(request,'index.html',{})  # 在返回的request中含有user参数
   # 在静态网页中调用request.user.is_authenticated进行判断user是否存在，验证登陆逻辑
   '''
   action里面填写的是你的url 因为浏览器会自动在前面加上域名 所以实际上浏览器在请求的时候路径应该是 域名/login/ <br>
   这个实际上就是你的urls中配置的对应的view， 记住如果login前面不加/ 比如写成login/ <br>
   那么浏览器会自动在你当前请求的路径下加上login比如当前浏览器的路径是 域名/user 那实际上路径会变成 域名/user/login
   '''
```
***
7. 自定义auth验证
```python
# 首先对django自带的authenticate中的backend进行自定义验证
  # 自定义CustomBackend方法
  from django.contrib.auth.backends import ModelBackend
  from django.db.models import Q
  
  from users.models import UserProfile
  
  
  class CustomBackend(ModelBackend):
       def authenticate(self, request, username=None, password=None, **kwargs):
           try:
               user = UserProfile.objects.get(Q(username = username)|Q(email = username))
               if user.check_password(password):  #AbstractUser中的验证方法
                  return user
           except Exception as e:
               return None
  # 1.在settings中重定义AUTHENTICATE_BACKENDS=('apps.users.views.CustomBackend',)
  
```
***
8. mac pycharm 调试快捷键
```
运行和调试快捷键

control +R 运行程序

control +alt +R 快速选择运行/调试配置并运行或编辑它

command +R 重新运行

control +R 重复执行相同的设置，与编辑器具有相同的标签焦点

control +D 调试程序

control +alt +D 快速选择运行/调试配置并进行调试或编辑

F8   转到当前文件的下一行

F7   步骤到下一个执行行

shift +F7 选择要进入的方法，如果当前行包含多个方法调用表达式

shift +F8 从当前方法返回后，转到第一个执行行

alt + shift +F8 运行到此方法或文件中的下一行，跳过当前执行点引用的方法并忽略断点

alt + shift +F7 即使跳过此方法，也可以在当前执行点中调用该方法

alt +F9 运行到插入符所在的行

alt +command +F9 强制运行到插入符所在的行，忽略断点

alt +command +R 恢复程序执行

shift +F2 终止调试会话

alt +F8 评估任意表达式

alt +command +F8 评估任意表达式而不调用 评估表达式对话框

command +F8 在当前行切换断点

shift + command + F8 查看/管理所有断点
```
***
9. 重新基于类重写views方法
```python
#继承django的基类View
from django.views.generic.base import View

class LoginView(View):
    def get(self,request):
       return render(request,'login.html',{})
    def post(self,request):
       user_name = request.POST.get('username', '')
       pass_word = request.POST.get('password', '')
       user = authenticate(username = user_name,password = pass_word)
       if user:
          login(request,user)
          return render(request,'index.html',{})
       else:
          return render(request,'login.html',{'msg': '用户名或密码错误'})
# 在setting的urlpatterns配置中，重定义路由：
from users.view import LoginView 

urlpatterns = [
     url(r('^login/'),LoginView.as_view(),name = 'login')
  ]
  
```
***
10. form表单预处理，格式验证
```python
# 在app中新建forms.py

 from django import forms
 
 class LoginForm(forms.Form):
     username = forms.CharField(required=True)
     password = forms.CharField(required=True,min_length=5)   required=True监听输入变量是否为空
     
#在views中导入form
from usrs.form import LoginForm

class LoginView(View):
    def get(request):
        return render(request,'login.html')
    def post(request):
        login_form = LoginForm(request.POST)
        if login_form.is_vaild():
          #判断逻辑
          user_name = request.POST('username','')
          pass_word = request.POST('password','')
          user = authenticate(username= user_name,password = pass_word)
          if user:
             login(request,user)
             return render(request,'index.html',{})
          else:
             return render(request,'login.html,{'msg':'登录失败','login_form':login_form})  
             #login_form交给静态页面进行配置
             #在执行过for key,value in login_form.errors.items():后输出的key,value如下:
             #错误字段：username 错误类型<ul class="errorlist"><li>这个字段是必填项。</li></ul> 
             #错误字段：password 错误类型<ul class="errorlist"><li>确保该变量至少包含 5 字符(目前字符数 1)。</li></ul> 
             #说明value是包含前端标签的字段，同时带有errorlist--class属性，可以在css文件中自定义错误样式
          
```
***
**遗留问题，自定义form.errors中的错误字段**<br>
11. session和cookie登录机制
```python
#http无状态协议，导致无法识别用户，统一返回一个界面
'''
-----cookies-----机制-------
   浏览器A向服务器发送请求，服务器自动给浏览器设置id为1，随后将id返回给浏览器。浏览器吧id放入cookie中，下次浏览器发送数据给浏览器时会带上sessionid
 此时服务器可以识别浏览器id。不同的服务器的cookie存在不同的域，服务器之间不能跨域访问。
   cookies的储存形式为字典{}形式。
   
------session-----机制----------
django将用户名密码等用户信息加密后储存在session_data中，在settings中可以设置expire_date的时间，从而确定session_data的过期时间
settins中注册的app中有 'django.contrib.sessions',此app在接收到页面的request请求后在reuqest中读取到sessionid然后在django_session中找到
对应的session_data然后进行反解，再读取出其中的字段信息，将信息放入request中。在页面向服务器返回resoponse数据时，也会自动为返回的数据加上sessionid。

'''
# session是cookie的一种实现方式，为了用户信息安全
```
***
12. 用户注册
```python
'''
验证码模块的导入
1.  pip3 install django-simple-captcha
2.  注册captcha
3.  url(r'^captcha/', include('captcha.urls')),
4.  ./mangage.py migrate
5.  定义注册RegisterForm
    from captcha.fields import CaptchaField
    
    class RegisterForm(forms.Form):
        email = forms.EmailField(require=Ture)
        password = forms.CharField(require=True, min_length=5)
        captcha = CaptchaFidld()
        
6.  在views中导入form
   from users.forms import RegisterForm
   
   class RegisterView(View):
      def get(request):
          register_form = RegisterForm()
          return render(request,'register.html',{'register_form':register_form})
7.  在静态页面中引入register_form
   {{ register_form.captcha }}
   自动生成html标签如下：
   <img src="/captcha/image/9edec57be36a17ae5d4a15b5c9c7ccca9bf8fe49/" alt="captcha" class="captcha" /><input id="id_captcha_0" name="captcha_0" type="hidden" value="9edec57be36a17ae5d4a15b5c9c7ccca9bf8fe49" />
<input autocapitalize="off" autocomplete="off" autocorrect="off" spellcheck="false" id="id_captcha_1" name="captcha_1" type="text" />
'''
# 密码加密
from django.contrib.auth.hashers import make_password

user_profile.password = make_password(password)
```
```
```
