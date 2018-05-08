### http
#### 请求报文
1. 起始行:
```
  method path version
  GET/ default.html HTTP/1.1
```
2. 头部
```
  Host:
  Cookie:
  Connect-type:text/html:application/javascript
```
3. 主体
```
  {'key':value,'name':'zhang'}
  GET:获取资源
  POST：更新资源
  PUT：创建资源
  DELETE：删除资源
  HEAD:返回资源信息,不需要将资源返回
  OPTION:查询对资源的操作方法
  
  GET和POST区别：
    get没有请求主体,只有path
    post有请求主体
    
  创建请求报文：
    GET/default.html HTTP/1.1 
    HOST:www.baidu.con
    Connecion:keep-alive
    Cookie:name=zhang
```
#### 响应报文
1. 起始行
```
告诉客户端，服务器对客户端的请求做出的处理，处理结果是什么

一些版本号 状态码 状态码说明
HTTP/1.1 200 
状态码：
  100-199:
  200-299:服务器操作成功
  300-399:重定向
  400-499:资源访问不存在或者资源限制
  500-599:服务器错误
```
2.头部
```
connect-type
cookies
```
3.主体
```

```
