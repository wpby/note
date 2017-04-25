### 准备工作
安装virtualbox、vagrant

### 添加box文件
laravel-VAGRANTSLASH-homestead 解压到 ~/.vagrant.d/boxes

### 查看vagrant以及box是否正确安装
cmd下 vagrant -v   vagrant box list

### 安装laravel/homestead
选择一个位置（d:\）: git clone git@github.com:laravel/homestead.git Homestead

cd Homestead

bash init.sh

### 配置信息
打开~/.homestead 下的Homestead.yaml
主要配置信息：
`
	folders:
    - map: d:\projects
      to: /home/vagrant/Code

	sites:
    - map: lwj.app
      to: /home/vagrant/Code/web/public
`
本地的d:\projects映射到/home/vagrant/Code

- map: lwj.app
      to: /home/vagrant/Code/web/public
和
hosts文件中
192.168.10.10      lwj.app 
可以将网站lwj.app 指向 /home/vagrant/Code/web/public

### 启动和连接
cd Homestead

vagrant up 启动虚拟机，启动完成后
vagrant ssh 连接虚拟机


### 关机和重启
修改homestead.yaml后，需要重启：vagrant provision
关机：vagrant halt

以上需要从虚拟机中退出： exit

### 安装mongodb

sudo apt install mongodb-server

sudo ln -s /usr/lib/x86_64-linux-gnu/libssl.so /usr/libssl.so

sudo pecl install mongodb

sudo vim /etc/mongod.conf

将bindIp 设置为 0.0.0.0

cd /etc/php/7.0
分别为/etc/php/7.0/cli/php.ini /etc/php/7.0/fpm/php.ini 添加extension=mongodb.so

sudo service nginx restart

### 安装cnpm
cnpm是npm在中国的镜像，由淘宝维护
sudo npm install cnpm -g --registry=https://registry.npm.taobao.org
