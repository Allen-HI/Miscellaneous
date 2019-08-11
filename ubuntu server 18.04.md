Ubuntu18.04server下载和ubuntu18.04.2安装教程,Ubuntu配置IP,DNS和设置root能SSH



 ubuntu18.04.2server下载地址： http://mirrors.aliyun.com/ubuntu-releases/18.04/ubuntu-18.04.2-live-server-amd64.iso

 下面是详细的ubuntu18.04.2server安装教程：



![Ubuntu18.10server安装教程1.png](/image/1.png)

![Ubuntu18.10server安装教程2.png](/image/2.png)

![Ubuntu18.10server安装教程3.png](/image/3.png)

![Ubuntu18.10server安装教程4.png](/image/4.png)

![Ubuntu18.10server安装教程5.png](/image/5.png)

![Ubuntu18.10server安装教程6.png](/image/6.png)

![Ubuntu18.10server安装教程7.png](/image/7.png)

![Ubuntu18.10server安装教程8.png](/image/8.png)



将镜像源从http://archive.ubuntu.com/ubuntu修改为国内的http://mirrors.163.com/ubuntu




![Ubuntu18.10server安装教程9.png](/image/9.png)

![Ubuntu18.10server安装教程10.png](/image/10.png)

![Ubuntu18.10server安装教程11.png](/image/11.png)

![Ubuntu18.10server安装教程12.png](/image/12.png)

![Ubuntu18.10server安装教程13.png](/image/13.png)

![Ubuntu18.10server安装教程14.png](/image/14.png)

![Ubuntu18.10server安装教程15.png](/image/15.png)

![Ubuntu18.10server安装教程16.png](/image/16.png)

![Ubuntu18.10server安装教程17.png](/image/17.png)

![Ubuntu18.04建立一个普通用户.png](/image/18.png)

![Ubuntu18.04安装SSH服务.png](/image/19.png)

![Ubuntu18.10server安装教程19.png](/image/20.png)

![Ubuntu18.10server安装教程20.png](/image/21.png)

![Ubuntu18.10server安装教程21.png](/image/22.png)

![Ubuntu18.04到达登陆界面.png](/image/23.png)

### 查看网卡信息命令：

ip addr show

### 其他设置：

##### 1、设置root用户可以SSH链接服务器：

我们前面装系统的时候已经装了SSH服务端OpenSSH server
假如我们装系统时候没装SHH服务端，我们就安装一下即可：
sudo apt-get install openssh-server #安装OpenSSH server,我们在前面已经装了SSH服务,我们这里就不用再装了
sudo passwd root #执行后给root设置密码,会要求先输入当前用户的密码做验证
su - root #切换到root账户,并输入上面设置的root密码
vi /etc/ssh/sshd_config  #修改SSH配置文件
找到PermitRootLogin without-password或是PermitRootLogin prohibit-password
注释掉 #PermitRootLogin without-password 或 #PermitRootLogin prohibit-password，或直接删除
添加PermitRootLogin yes
就是允许SSH远程登录
重启SSH服务生效：
service ssh restart

##### 2、给服务器配置外网IP：

先用ifconfig -a看看网卡是不是eth0，如果是别的名称以下配置中的eth0要做对应修改，有的是enp1s0f0
vi /etc/network/interfaces
默认是：
auto eth0
iface eth0 inet dhcp
默认是以DHCP方式配置网卡自动分配IP

默认内容也有可能是：
source /etc/network/interfaces.d/*
# The loopback network interface
auto lo
iface lo inet loopback
我们要将默认的#注释掉或删除

我们需要修改为静态IP：
auto eth0 
iface eth0 inet static 
address 14.17.65.109 #IP
netmask 255.255.255.0 #子网
gateway 14.17.65.65 #网关
dns-nameservers 114.114.114.114 8.8.8.8 #这里设置了两个DNS
重启网卡生效：
sudo /etc/init.d/networking restart
如果重启网卡报错，就有可能是修改网卡的名称引起的。这时候可以重启服务器reboot
如果服务器要设置双IP呢：
vi /etc/network/interfaces
在该文件中再添加如下的行：
auto eth0:1  
iface eth0:1 inet static  
address x.x.x.x  
netmask x.x.x.x

##### 下面是我们服务器中设置双IP的配置信息请参考(配置文件中的网卡是enp1s0f1)：

auto enp1s0f1

iface enp1s0f1 inet static

address 14.17.65.84

netmask 255.255.255.192

gateway 14.17.65.65

auto enp1s0f1:1

iface enp1s0f1:1 inet static

address 114.119.116.59

netmask 255.255.255.0

dns-nameservers 114.114.114.114 8.8.8.8

##### 3、修改服务器的DNS(这里不需要操作,因为上面网卡IP配置文件中设置了DNS)：

vi /etc/resolvconf/resolv.conf.d/base
增加：
search localdomain #如果服务器做专门的DNS服务器,可以加上这一句,如果不是就不加,一般不用加
nameserver 114.114.114.114
nameserver 8.8.8.8
重启网络和DNS后生效：
sudo /etc/init.d/networking restart
sudo /etc/init.d/resolvconf restart
