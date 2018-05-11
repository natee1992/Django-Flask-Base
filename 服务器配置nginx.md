基于python3,nginx,ubunto,uwsgi,django的服务器配置


```
apt-get update

apt-get install nginx

apt-get install 

apt-get install python3-dev

apt-get isntall 

apt-get isntall pip3

pip3 install uwsgi

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
export VIRTUALENVWRAPPER_PYTHON = python版本目录(可使用which python3查看)
export WORKON_HOME = 虚拟环境地址指定
source virtualenvwrapper.sh文件的地址（可使用find / -name virtualenvwrapper.sh命令查找）
#end

source ~/.bashrc
#重载配置文件

mkvirtualenv -p python3 env
#创建并激活

deactivate
#推出虚拟环境



```
