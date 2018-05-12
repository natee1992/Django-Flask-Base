基于python3,nginx,ubunto,uwsgi,django的服务器配置


```
apt-get update

adduser lee #创建用户lee

usermod -aG sudo lee #给予lee用户sudo权限

passwd lee #设定密码

su lee #切换用户为lee,其对应的文件夹为/home/lee

apt-get install nginx

sudo apt-get install python3-pip 

apt-get install python3-dev

#配置nginx proxy_pass
sudo vim /etc/nginx/site-enabled/default
添加代理
location /(test)正则匹配名{
      proxy_pass http://127.0.0.1:8000;
}

#重启nginx
sudo service nginx stop
sudo service nginx start


pip3 install virtualenv

pip3 install virtualenvwrapper

apt-get vim

vim ~/.bashrc
#行末插入
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3.4 
export WORKON_HOME=~/env_package
source /usr/local/bin/virtualenvwrapper.sh
#end

source ~/.bashrc
#重载配置文件

mkvirtualenv -p python3 env
#创建并激活

deactivate
#推出虚拟环境

###更改nginx代理
proxy_pass http://127.0.0.1:8000;


sudo pip3 install uwsgi

#编写测试文件
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"]
    
#测试uwsgi
uwsgi --http :8000 --wsgi-file test.py 



```
