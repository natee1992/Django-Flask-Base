### python中赋值、引用、拷贝、作用域
##### 赋值
python中赋值是建立一个对象的引用值。<br>其实python中没有赋值，比如a=3,不是将3赋值给a，实际上是对3贴上一个标签a，a中储存指向3的地址。<br>
```
s=[1,2]
a=[1,3]
id(s) != id(a) 
可变类型，两个变量是同一个值时，其实是新建了一个原对象

1 在Python中，对象分为两种：可变对象和不可变对象，
2 不可变对象包括int，float，long，str，tuple等，可变对象包括list，set，dict等。
3 需要注意的是：这里说的不可变指的是值的不可变。对于不可变类型的变量，如果要更改变量，则会创建一个新值，
  把变量绑定到新值上，而旧值如果没有被引用就等待垃圾回收。另外，不可变的类型可以计算hash值，作为字典的key。
4 可变类型数据对对象操作的时候，不需要再在其他地方申请内存，只需要在此对象后面连续申请(+/-)即可，
  也就是它的内存地址会保持不变，但区域会变长或者变短。
```
##### 引用
如果a=3,a=4,即将对象3上的标签去掉，贴到对象4上面，此时那么python会启动回收机制，当对象3上的引用计数为0，将会对对象3进行回收。
##### *拷贝
###### 浅拷贝
    1. a.copy()
    2. a[:]   
    3. list(a) 内置函数list
    浅复制其实是创建了一个对于该对象的引用，也就是说只是给同一个对象贴上了另一个标签而已。
```python
a=[1,[1,3],4]
b=a[:]
a[0]=8
a[1][1]=9
print(a)  #[8,[1,9],4]
print(b)  #[1,[1,9],4]
```
   出现b不等于a的原因是因为，python中对于对象储存的是引用值，a[1]是[1,3]的引用，当a[1][1]=9时，python通过引用追溯到[1,3],改变3的值为9。<br>
但当拷贝为浅拷贝时，b拷贝a是拷贝[1,3]的引用，并没有将[1,3]对象拷贝过来，所以b[1]还是[1,3]的引用。此时[1,3]对象并没有被拷贝，所以b[1]被改变。<br>
原理如下图：<br>
<img src='https://draapho.github.io/images/1618/python_point_5.jpg' title='引用计数图' width='500px' height='300px'>
###### 深拷贝
    import copy
    copy.deepcopy()
    本质是递归copy
```python
import copy
a=[0,[1,2],3]
b=copy.deepcopy(a)
a[1][1]=9
print(a) #[0,[1,9],3]
print(b) #[0,[1,2],3]
```
当引用copy模块中的deepcopy时，会递归的对a进行复制，将a中所有引用对象复制。
###### 函数值传递
```python
def func(t):
    t += 4
    print(id(t))  
t = 0
func(t)
print(id(t)) # 和函数中t的id值不同
print(t)  # t=0

def func(l):
    l[0]=4
 l=[1,2,3,4]
 func(l)
 print(l) # l=[4,2,3,4]
 '''
 对于可变对象，对象的操作不会重建对象，而对于不可变对象，每一次操作就重建新的对象。
 '''
```
***
#### 1.python中的函数参数传递

```python
l1=[1,2,3]
l2=[1,2,3]
a=1
b=1
id(l1) != id(l2)  #创建可变对象永远是分配新地址
id(a) = id(b) #对于不可变类型，地址是共享的

def func(a):
    a += 1
t=0
func(t)
print(t)  #0
```
**python函数中参数传递的实质：依次把传入参数的对象的引用赋值给函数内部变量。**<br>
如果对象是可变的，那么操作是在传入对象上操作的，如果是不可变的，那么相当于这个标识符指向了另一个对象。<br>
```python
a='you'
b={'a':3,'b':4}
def func():
    a='me'
    b['c']=4
print(a) # 'you'
print(b) # '{'a':3,'b':4,'c':4}'
```
出现上面结果的原因：当在函数中执行a='me'时，语意不明确，python无法判断是对全局变量a重新赋值还是新建局部变量a，默认新建局部变量，除非显式声明global<br>
但字典b在函数体内执行时，语意明确，如果把b当作局部变量，则会报错keyerror,所以会将b当作全局变量。<br>
```python
a=[]
def func():
    a = [1]
func()
print(a)  # []

a=[]
def func():
    a.append(1)
func()
print(a)  # [1]
```
出现以上结果的原因：=创建了局部变量，append和extend重用了全局变量
***
#### 2.python中的元类
  元类 (metaclass) 是创建类的类，类是元类的实例，只有 type 及其派生类才可能充当元类。正如我们要先创建一个类，然后才能创建实例，我们先要创建元类，然后才能创建类。
***
#### 3.@staticmethod和@classmethod
静态方法staticmethod<br>
类方法classmethod
实例方法
***
#### 4.类变量和实例变量
类变量是可在类的所有实例之间共享的值<br>
实例变量是类实例化以后每个实例单独应用的变量
```python
class Person(object):
    name='人'
if __name__=='__main__':
    p1=Person()
    p2=Person()
    p1.name='jack'
    p2.name='人'
    print(p1.name) #'jack'
    print(p2.name) #'人'
    
 
class Person(object):
    name=[]
if __name__ == '__main__':
    p1=Person()
    p2=Person()
    p1.name.append(1)
    print(p1.name)  #[1]
    print(p2.name)  #[1] 
 ```
 ***
