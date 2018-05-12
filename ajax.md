let 创建的变量不可以再次被定义
var 创建的变量可以被重新定义
建立ajax对象
```javascript
var ajax = new XMLHttpRequest()
xhr.onreadystatechange=function(){
    var readystate= xhr.readyState
    switch(readystate){
        case 0: consloe.log('创建好了ajax对象');break;
        case 1: consloe.log('调取了open方法');break;
        case 2: consloe.log('调用了send方法');break;
        case 3: consloe.log('响应体部分正在被接受');break;
        case 4: consloe.log('请求完成');break;
    }
}
ajax.open('get','/tajx')
var data = 'hello'
ajax.send(data)
```
```javascript
var ajax = new XMLHttpRequest()
xhr.timeout = 2000
xhr.ontimeout = function(){
      xhr.abort()
      console.log('服务器在2秒内没有反应,请求被取消')
}
xhr.onreadystatechange=function(){
    var readystate= xhr.readyState
    switch(readystate){
        case 0: consloe.log('创建好了ajax对象');break;
        case 1: consloe.log('调取了open方法');break;
        case 2: consloe.log('调用了send方法');break;
        case 3: consloe.log('响应体部分正在被接受');break;
        case 4: {
               console.log('请求已经完成');
               console.log('response',xhr.response);
               console.log('responseText',xhr.resopnseText);
               console.log('responseType',xhr.resopnseType);
               console.log('status',xhr.staus);
               console.log('statusText',xhr.stausText);        
               
        }
    }
}
ajax.open('get','/tajx')
var data = 'hello'
ajax.send(data)
```
