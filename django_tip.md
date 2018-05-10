*开发环境pycharm mysql*
## 扩展auth_user表 
```
auth_user表继承于django.contrib.auth.models中的AbstractUser
继承时不要修改username字段
重定义AUTH_USER_MODEL
在执行makemigrations前，由于admin中很多依赖于auth_user，可能迁移出错，所以将数据库中除auth_user表全部删除后再执行数据库迁移
CharField类型必须指定max_length
```
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
## 后台管理
```
特点：
 权限管理
 少前端样式
 快速开发
```
## settings
```
USE_TZ = False 本地时间

```
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
5. templates静态页面配置
不用配置views方面，直接匹配静态页面。
```python
 # 在根目录的urls文件中配置templates方法，直接导入首页
 from django.views.generic import TemplateView
 #在urlpatterns中添加首页路由：
 url(r'^$',TemplateView.as_view(template_name='index.html'),name='index')
```
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
                login(request,user)  # 调用
                return render(request,'index.html',{})
   在静态网页中调用request.user.
```
