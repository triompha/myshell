- 工程只依赖expect，区别于ansible在于其简单，环境有expect的话无需任何安装，直接执行即可。当然如果涉及到操作需要重复输入密码的话只能使用ansible或pssh等工具





> **ssh-key-copy**  一个设置免登的命令，类同于ssh-copy-id，但是可以实现批量，且不需要每次手动输入密码

> **pssh**  一个批量机器执行命令的脚本，通过设定密码在批量机器上执行，类似于http://www.theether.org/pssh/  ,区别在于简单，且mac环境也能用

> **pscp**  一个批量机器复制文件的脚本，通过设定的密码在批量机器上复制文件，类似于http://www.theether.org/pssh/  ,区别在于简单，且mac环境也能用






**myshell**是工作几年的一些linux命令积累

### 代码块
``` shell
将IP列表写入文件中，然后读出
for ip in `cat iplist`;do ssh-key-copy root@"${ip}" 1234; done

直接循环出
for ip in {76..77};do ssh-key-copy root@"192.168.99.${ip}" 1234; done

批量命令
已经建立好ssh免登的批量命令
for ip in `cat iplist`;do ssh -o StrictHostKeyChecking=no -o GSSAPIAuthentication=no root@${ip} "cmd" ; done
未建立好ssh免登的批量命令
for ip in `cat iplist`;do pssh "cmd" root@${ip} passwd ; done

批量复制文件
已经建立好ssh免登的批量命令
for ip in `cat iplist`;do scp srcFile root@${ip}:/dist; done
未建立好ssh免登的批量命令
for ip in `cat iplist`;do pscp srcfile root@${ip} passwd ; done


关闭selinux并重启
setenforce 0;sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config;reboot 1;

添加yum源
yum install yum-utils;yum-config-manager --add-repo  https://mirrors.aliyun.com/centos/7/os/x86_64/;

安装ssh-client
yum install -y openssh-clients

安装NTP服务器
yum -y install ntp;service ntpd stop;ntpdate ${serverIp};cp /etc/ntp.conf /etc/ntp.conf.back; echo 'server ${serverIp}'>/etc/ntp.conf;chkconfig ntpd on;service ntpd restart

修改hostname -i的对应
ihostname=`ssh -o StrictHostKeyChecking=no -o GSSAPIAuthentication=no root@${ip} "hostname"`
ssh -o StrictHostKeyChecking=no -o GSSAPIAuthentication=no root@${ip} "sed -i /${ihostname}/d /etc/hosts; echo ${ip}'    '${ihostname} >> /etc/hosts"


mount 分区
ssh -o StrictHostKeyChecking=no -o GSSAPIAuthentication=no root@${ip} "yum -y install parted;mkdir -p ${2};parted -s ${1} mklabel gpt;parted -s ${1} mkpart primary 0% 100%;partprobe ${1}1 & >/dev/null;mkfs.ext4 ${1}1 -O extent,uninit_bg -E lazy_itable_init=1;mount ${1}1 $2;echo '${1}1 $2 ext4 defaults,noatime,nodiratime,nodelalloc,barrier=0 0 0' >> /etc/fstab;

修改机器密码
ssh -o StrictHostKeyChecking=no -o GSSAPIAuthentication=no root@${ip} "echo ${password}|passwd root --stdin"

清理iptable信息
iptables -F;iptables -X;
```



