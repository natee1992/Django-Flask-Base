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
```
xadmin自动发现userprofile用户表,其余表需要注册到adminx.py中，所以需要在每个app下新建adminx.py
import xadmin
from .model import *
class EmailVerifyRecordAdmin(object):  # xadmin继承于object类
   list_display=['code','email','send_type','send_time']  # 自定义后台显示字段
   search_fields = ['code','email','send_type']   # 自定义搜索类型
   list_filter = ['code','email','send_type','send_time']  # 自定义筛选字段
   ######## list_fitter = ['course__name'] 外键的筛选需要两个下划线  ########
xadmin.site.register(EmailVerifyRecord,EmailVerifyRecordAdmin)   # 注册emailverifyrecord表到xadmin后台

```
