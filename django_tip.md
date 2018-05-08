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

  
```
