1、下载安装包 （wget ）。解压
yum -y install wget 
wget http://bkopen-1252002024.file.myqcloud.com/ce/bkce_product-4.1.16.tgz
wget http://bkopen-1252002024.file.myqcloud.com/ce/bkce_common-1.0.0.tgz
wget http://bkopen-1252002024.file.myqcloud.com/ce/install_ce-master-1.4.13.tgz
cd /data
tar xf bkce_product-4.1.16.tgz
tar xf bkce_common-1.0.0.tgz
tar xf install_ce-master-1.4.13.tgz
tar xf ssl_certificates.tar.gz -C /data/src/cert/
2.配置yum源  本地源  epel源
yum -y install epel-release
3.解压脚本，申请证书
http://bk.tencent.com/download/#ssl
gse,liscense服务器MAC申请，上传至/data
4.先修改install.config   globals.env 
globals.env 域名  paas密码  
export AUTO_GET_WANIP=1
export GSE_WAN_IP=(1.1.1.1 1.1.1.1)
export NGINX_WAN_IP=(2.2.2.2 2.2.2.2)  
5.配置免密
yum -y install rsync
cd /data/install
bash configure_ssh_without_pass 
6.配置时间同步、时区
for i in `cat install.config|awk '{print $1}'`;do ssh $i timedatectl set-timezone Asia/Shanghai;done
7.防火墙、selinux配置，NetWorkManger
for i in `cat install.config|awk '{print $1}'`;do ssh $i systemctl disable firewalld;done
for i in `cat install.config|awk '{print $1}'`;do ssh $i systemctl stop firewalld;done
for i in `cat install.config|awk '{print $1}'`;do ssh $i iptables -F;done
for i in `cat install.config|awk '{print $1}'`;do ssh $i setenforce 0;done
for i in `cat install.config|awk '{print $1}'`;do ssh $i sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config;done
echo -e "*  hard  nofile  204800 \n*  soft  nofile  204800" >> /etc/security/limits.conf
for i in `cat install.config|awk '{print $1}'`;do ssh $i systemctl disable NetworkManager;done
for i in `cat install.config|awk '{print $1}'`;do ssh $i systemctl stop NetworkManager;done
for i in `cat install.config|awk '{print $1}'`;do ssh $i yum -y install epel-release;done

8. vim /etc/resolv.conf    首行  nameserver 127.0.0.1  删除option rotate

9.开源组件补全 社区版已补全

10.precheck检查

11.开始安装
cd /data/install
./bk_install paas 
./bk_install cmdb
./bk_install job
./bk_install app_mgr 
./bk_install bkdata
./bk_install fta
./bk_install saas-o



cpu=$(cat <(grep 'cpu ' /proc/stat) <(sleep 1 && grep 'cpu ' /proc/stat) | awk -v RS="" '{print ($13-$2+$15-$4)*100/($13-$2+$15-$4+$16-$5)}')
