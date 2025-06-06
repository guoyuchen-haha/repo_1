## <strong style='color: #A30000'>1. 常用操作系统介绍</strong>
### 1.1 通性

### 1.2 特性



## <strong style='color: #A30000'>2. 红帽企业linux入门</strong>

### 2.1 什么是Linux

OS=Tools + Kernel = GNU/Linux

Unix + 特定硬件 == 上百万

Linus + x86兼容机 + minux -=> Linux(kernel)

Stallman GNU.org(Tools)

redhat9

- fedora 个人版
- RHEL
- centos -=> stream



## <strong style='color: #A30000'>3. 常用虚拟化软件</strong>

物理机/VMware/

​	foundation(Linux)/

​		kvm



### 3.1 VMware 介绍

https://www.vmware.com/

- VMware Workstation Pro
  - Linux
  - Windows
- VMware Fusion
  - MacOS
    - Intel-based
    - Apple silicon(Arm64)

### 3.2 Virtual Box介绍

https://www.virtualbox.org/

https://www.vagrantup.com/

### 3.A other

- KVM(qemu) - Linux
- Hypervisor - Windows



## <strong style='color: #A30000'>4. 编辑文本文件</strong>

### 4.1 nano 类似 Windows 的记事本

<kbd>Ctrl</kbd>-<kbd>O</kbd>, <kbd>Ctrl</kbd>-<kbd>X</kbd>

```bash
$ nano new.txt
$ cat new.txt

$ nano new.txt 
$ cat new.txt
```



### 4.2 gedit 图型界面直接修改文件

```bash
$ gedit xin.txt
```

<kbd>Alt</kbd>-<kbd>F2</kbd> == win/<kbd>Win</kbd>-<kbd>R</kbd>

​		==gedit==



### 4.3  vim 高级用法，更高效

> 定义 vim config

```bash
$ tee ~/.vimrc <<EOF
set number
EOF
```

> 定义 Tab == 2空格，显示内容=保存内容

```bash
tee -a ~/.vimrc <<EOF
set ts=2 et
EOF
```

> 定义 光标列

```bash
tee -a ~/.vimrc <<EOF
set cuc
EOF
```

> 列缩进，<kbd>></kbd>右缩进，<kbd><</kbd> 左缩进，<kbd>.</kbd> 重复上一次缩进
> 	<kbd>V</kbd>, <kbd>G</kbd>

```
:set sw=2
```



## <strong style='color: #A30000'>5. RHEL 网络管理</strong>

### 5.1 设置服务器之间路由，开启内核转发

> - kvm
> - kubernetes

```bash
$ ls /etc/sysctl.conf /etc/sysctl.d/
/etc/sysctl.conf 
/etc/sysctl.d/99-sysctl.conf

$ sysctl -a

$ sysctl -a | grep ipv4.*forward
net.ipv4.ip_forward = 1

$ sysctl net.ipv4.ip_forward
```

```bash
# tee -a /etc/sysctl.conf <<EOF
net.ipv4.ip_forward = 0
EOF

# sysctl -p

# sysctl net.ipv4.ip_forward
```

```bash
sed -i '/ip_forward/s/0/1/' /etc/sysctl.conf
sysctl -p
sysctl net.ipv4.ip_forward
```



### 5.2 tcpdump 抓包

> Snipper 嗅探器

- **DHCP 简介**

  > Dynamic Host Configuration Protocol，动态主机配置协议
  >
  > 当我们将客户主机 ip 地址设置为动态获取方式时，
  > 根据 DHCP 协议给客户端分配 IP，使得客户机能够利用这个 IP 上网
  >
  > DHCP 的前身是 BOOTP 协议（Bootstrap Protocol）,
  > BOOTP被创建出来为连接到网络中的设备自动分配地址
  >
  > DHCP 比 BOOTP 更加复杂，功能更强大

- **DHCP 的实现**

  | STEP |    dhcp Client    |   dhcp Server   |
  | :--: | :---------------: | :-------------: |
  |  1   | Discover（广播）→ |                 |
  |  2   |                   | ← Offer（单播） |
  |  3   | Request（广播）→  |                 |
  |  4   |                   |  ← Ack（单播）  |

- **抓取 dhcp 的数据包**

  |   TYPE    | Windows<br>dhcp server |    MacOS<br>dhcp server    |          F0<br>dhcp client          |
  | :-------: | :--------------------: | :------------------------: | :---------------------------------: |
  |    NAT    |         VMnet8         | bridge101<br>192.168.147.1 |  ==ens36==/dhcp<br>192.168.147.128  |
  | Host-only |         VMnet1         | bridge100<br>172.25.254.1  | ens160/static/br0<br>172.25.254.250 |

  ```bash
  $ nmcli dev status 
  DEVICE       TYPE      STATE                   CONNECTION         
  `ens36`      ethernet  connected              `Wired connection 1`
  ```

  ```bash
  # tcpdump \
    -i ens36 \
    udp \
      port 67 or port 68 \
    -vvv
  ```

  ```bash
  $ nmcli con down 'Wired connection 1' 
  $ nmcli con up 'Wired connection 1'
  ```

  

## <strong style='color: #A30000'>6.  归档与系统间复制文件</strong>

### 6.1 scp 利用 IP，实现远程文件传输

> Win8 之后的版本，默认自带 ssh/scp

```bash
将远程目录，拷贝到当前目录
% scp -r kiosk@172.25.0.250:/content/slides .

将本地文件，拷贝到远程服务器，用户家目录
% scp ssh2rdp.excalidraw kiosk@172.25.0.250:
```



### 6.2 rz 和 sz 利用终端，实现远程文件传输

> PKGS: ==lrzsz== 服务器端与客户端，都需要安装这个包
>
> 客户端使用Windows，Xshell 自带 lrzsz

```bash
# ssh root@172.25.0.250

# dnf -y install lrzsz
```

> 上传，将本地文件传到服务器

```bash
# rz
在弹出窗口中选择，文件
# ls
```

> 下载，将服务器文件传到客户端

```bash
# sz anaconda-ks.cfg
在弹出窗口中选择，保存位置
```



## <strong style='color: #A30000'>7. OpenSSH 服务</strong>

### 7.1 ssh 通过堡垒主机，直连服务器

> 目标：ssh直接登陆目标主机

| Client<br>~/.ssh/config |      F0       |    Servera    |
| :---------------------: | :-----------: | :-----------: |
|          Host           | 172.25.0.250  |    servera    |
|          User           |     kiosk     |    student    |
|      IdentityFile       | ~/.ssh/f0_rsa | ~/.ssh/f0_rsa |

```bash
% vim ~/.ssh/config
```

```ini
...
# == bastion host
Host 172.25.0.250
  User kiosk
  IdentityFile ~/.ssh/f0_rsa

Host servera
  User student
  HostName servera
  IdentityFile ~/.ssh/f0_rsa
  ProxyCommand ssh 172.25.0.250 -W %h:%p
```

```bash
% ssh servera
[student@servera ~]$ exit
```



### 7.2 常用客户端 – Xshell，SecureCRT

|  ID  |                      NAME                       |          OS           | COST             |
| :--: | :---------------------------------------------: | :-------------------: | ---------------- |
|  1   | [Xshell](https://www.netsarang.com/zh/xshell/)  |        Windows        | School,home 免费 |
|  2   |                    SecureCRT                    |    Windows, MacOS     | 收费             |
|  3   |   [MobaXterm](https://mobaxterm.mobatek.net/)   |        Windows        | 免费             |
|  4   | [FinalShell](http://www.hostbuf.com/t/988.html) |        Windows        | 免费             |
|  5   |           [tabby](https://tabby.sh/)            | Windows, Linux, MacOS | 免费             |
|  6   |                      iTerm                      |         MacOS         | 免费             |



### 7.3 神奇的 -X 选项

> RDP 远程桌面
>
> Remote App 远程应用 == -X

|  ID  | Client  | Other                                  |
| :--: | :-----: | -------------------------------------- |
|  1   |  Linux  | -                                      |
|  2   |  MacOS  | brew install xquartz                   |
|  3   | Windows | https://sourceforge.net/projects/xming |

```bash
% ssh -X 172.25.0.250
$ virt-manager
```



## <strong style='color: #A30000'>8. 使用驾驶舱 cockpit</strong>

### 8.1 Windows 管理员可以迅速上手 Linux

```bash
# systemctl enable --now cockpit.socket
```

> https://172.25.0.250:9090

```bash
# dnf -y install cockpit-machines
```

> https://172.25.0.250:9090
>
> 刷新



## <strong style='color: #A30000'>9. 分析服务器并获得支持</strong>

### 9.1 分析和管理远程服务器

1. 日志
2. prometues
3. sosreport
4. 截图



### 9.2 从红帽客户门户获取帮助

https://access.redhat.com/

1. 红帽帐号（免费）
2. 开发者帐号（免费）
   https://developers.redhat.com/



### 9.3 利用红帽智能分析检测并解决问题

1. 系统订阅

2. Insigts 注册

3. 开发者帐号（免费）

   

## <strong style='color: #A30000'>10. 配置链路聚合</strong>

### 10.1 配置链路聚合

Link Aggregation 是一个计算机网络术语，
指将多个物理端口汇聚在一起，形成一个逻辑端口，以实现流量吞吐量在各成员端口的负荷分担。
链路聚合在增加链路带宽、实现链路传输弹性和工程冗余等方面是一项很重要的技术。

常用的有 **bond** 和 **team** 两种模式

- bond 模式最多可以添加==2==块网卡（RHEL<=6，RHEL>=9）
- team 模式最多可以添加==8==块网卡（RHEL7，8）

|       NIC        | servera | 配置文件 | Bridge name |
| :--------------: | :-----: | :------: | :---------: |
|  物理网卡: NIC1  |  eth0   | 重新生成 |   privbr0   |
| 物理网卡: + NIC2 |  eth1   | 重新生成 |   privbr0   |
|     逻辑端口     | mybond0 | 重新生成 |             |

**[kiosk@foundation0]**

<kbd>Alt</kbd>-<kbd>F2</kbd> / ==virt-manager==

**[root@servera]**

```bash
0. 确认原有网络参数
# ip a
2: `eth0`: ...
    inet `172.25.250.10/24`
3: `eth1`
# ip route
default via `172.25.250.254` ...
# cat /etc/resolv.conf 
search `lab.example.com` `example.com`
nameserver `172.25.250.220`

1. 删除原有网卡配置文件
# \rm /etc/NetworkManager/system-connections/*
# nmcli con delete 'Wired connection 1' 
# nmcli con delete 'Wired connection 2'

2. 生成新的网卡配置文件
# mandb
# man -k nmcli 
# man nmcli-examples
/bond

# nmcli con add type bond ifname mybond0 mode active-backup
# nmcli con add type ethernet ifname eth0 master mybond0
# nmcli con add type ethernet ifname eth1 master mybond0

# ls /etc/NetworkManager/system-connections/

# nmcli con mod bond-mybond0 \
  connection.autoconnect true \
  ipv4.method manual \
    ipv4.addresses 172.25.250.10/24 \
    ipv4.gateway 172.25.250.254 \
    ipv4.dns 172.25.250.220 \
    ipv4.dns-search 'lab.example.com,example.com'

# nmcli con down bond-mybond0 \
  && nmcli con up bond-mybond0
```

```bash
验证：即使关闭了正在使用的网卡，网络不会断开
# nmcli connection show
# ip a
# nmcli con down bond-slave-eth1
# nmcli con up bond-slave-eth1
```



## <strong style='color: #A30000'>11. 管理 DHCP 和 IP 地址分配</strong>

### 11.1 使用 DHCP 配置 IPv4 地址分配

> 场景：无线路由、无线宽带、网络布署系统、网络Ghost
>
> 分配：ip/netmask, gateway, dns, dns-search（除了mac地址）

|   ID    |      SERVER      |     CLIENT      |
| :-----: | :--------------: | :-------------: |
|  host   |     servera      |   workstation   |
|   IP    | 172.25.250.10/24 | 172.25.250.9/24 |
| Gateway |                  | 172.25.250.254  |
|   DNS   |                  | 172.25.250.220  |

**[root@servera]**

```bash
# dnf search dhcp
 
# dnf -y install dhcp-server
 
# rpm -qc dhcp-server
 
# egrep -v '^$|^#' /usr/share/doc/dhcp-server/dhcpd.conf.example > /etc/dhcp/dhcpd.conf
 
# vim /etc/dhcp/dhcpd.conf
```

```ini
option domain-name "lab.example.com";
option domain-name-servers 172.25.250.220;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;
 
subnet 172.25.250.0 netmask 255.255.255.0 {
  range 172.25.250.26 172.25.250.30;
  option routers 172.25.250.254;
}
 
host workstation {
  hardware ethernet 52:54:00:00:fa:09;
  fixed-address 172.25.250.9;
}
```

```bash
# systemctl enable --now dhcpd
```

**[root@workstation]**

```bash
# nmcli con mod 'System eth0' ipv4.method auto

# nmcli con down 'System eth0' && nmcli con up 'System eth0'

# nmcli con show 'System eth0'
```



## <strong style='color: #A30000'>12. 配置 MariaDB SQL 数据库</strong>

> mysql
>
> mariadb
>
> 场景：Nextcloud, Discuz, Wordpress
>
> LNMP, LAMP: Linux Nginx|Apache Mariadb PHP

### 12.1 安装 MariaDB 数据库 

|  ID  |     SERVER     |   CLIENT    |
| :--: | :------------: | :---------: |
| HOST |    servera     | workstation |
| PKGS | mariadb-server |   Mariadb   |
| CMD  |                |    mysql    |

**[root@servera]**

```bash
# dnf search mariadb

# dnf -y install mariadb-server
```

**[root@workstation]**

```bash
# dnf -y install mariadb
```



### 12.2 在 MariaDB 中使用 SQL 

**[root@servera]**

```bash
# systemctl enable --now mariadb

# firewall-cmd --permanent --add-service=mysql 
# firewall-cmd --reload

# mysql_secure_installation 
...
Switch to unix_socket authentication [Y/n] `<Enter>`
...
Change the root password? [Y/n] `<Enter>`
New password: `mysql`
Re-enter new password: `mysql`
...
Remove anonymous users? [Y/n] `<Enter>`
...
Disallow root login remotely? [Y/n] `<Enter>`
...
Remove test database and access to it? [Y/n] `<Enter>`
...
Reload privilege tables now? [Y/n] `<Enter>`

# mysql -u root -pmysql
...
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> help;
MariaDB [(none)]> exit
```

**[root@workstation]**

```bash
因为服务器端设置『Disallow root login remotely』

# mysql -u root -pmysql -h servera
ERROR 1130 (HY000): Host 'workstation.lab.example.com' is not allowed to connect to this MariaDB server
```



### 12.3 管理 MariaDB 用户和访问权限

**[root@servera]**

```bash
# mysql -u root -pmysql

MariaDB [(none)]>help contents;
MariaDB [(none)]> help 'Data Definition';

MariaDB [(none)]> help CREATE DATABASE;
MariaDB [(none)]> CREATE DATABASE testdb;

MariaDB [(none)]> help Account Management;
MariaDB [(none)]> help CREATE USER
MariaDB [(none)]> help grant;

MariaDB [(none)]> GRANT ALL PRIVILEGES ON testdb.* to 'tom'@'%';

MariaDB [(none)]> exit
```

**[root@workstation]**

```bash
# mysql -u tom -pttt -h servera testdb
...
MariaDB [testdb]>
```



### 12.4 创建和还原 MariaDB 备份

```bash
# mysqldump --databases testdb > testdb.sql
```

```bash
# mysql -u root -pmysql -e 'DROP DATABASE testdb;'

# mysql -u root -pmysql -e 'SHOW DATABASES;'
```

```bash
# mysql -u root -pmysql < testdb.sql

# mysql -u root -pmysql -e 'SHOW DATABASES;'
```



## <strong style='color: #A30000'>13. 配置 Web 服务器</strong>

> http 超文本传输协议

### 13.1 使用 Apache配置基本 Web 服务器

**[root@servera]**

```bash
# dnf search apache
# dnf -y install httpd.x86_64
 
# rpm -qc httpd
# vim /etc/httpd/conf/httpd.conf 
/DocumentRoot
/DirectoryIndex
 
# echo 'hello world' > /var/www/html/index.html
 
# systemctl enable --now httpd
 
# firewall-cmd --permanent --add-service=http
# firewall-cmd --reload
```

**[kiosk@foundation0]**

```bash
# curl servera
```



### 13.2 使用 Apache 配置虚拟主机

> 基于主机名的虚拟主机
>
> 当存在虚拟主机时，第一个虚拟主机就是默认的web站点

**[root@servera]**

```bash
# tee -a /etc/hosts <<EOF
172.25.250.10 sa.lab.com virt.lab.com
EOF

# mkdir /var/www/virt
# echo virtual host > /var/www/virt/index.html

# egrep -v '^$|^#' /usr/share/doc/httpd/httpd-vhosts.conf > /etc/httpd/conf.d/httpd-vhosts.conf 
# vim /etc/httpd/conf.d/httpd-vhosts.conf
```

```ini
<VirtualHost *:80>
    DocumentRoot "/var/www/html"
    ServerName sa.lab.com
</VirtualHost>
<VirtualHost *:80>
    DocumentRoot "/var/www/virt"
    ServerName virt.lab.com
</VirtualHost>
```

```bash
# systemctl status httpd
# systemctl restart httpd
```

```bash
# curl virt.lab.com
# curl servera
# curl sa.lab.com
# curl 172.25.250.10
```

**[root@workstation]**

```bash
# tee -a /etc/hosts <<EOF
172.25.250.10 sa.lab.com virt.lab.com
EOF

# curl virt.lab.com
# curl servera
# curl sa.lab.com
# curl 172.25.250.10
```



### 13.3 使用 Apache 配置 HTTPS

> 基于端口的虚拟主机
>
> 证书：
>
> 1. 私有证书
> 2. 公共证书
>    1. 花钱（1年）
>    2. 免费（3个月）

**[root@servera]**

```bash
# 生成私钥
openssl genrsa \
  -out /etc/pki/tls/private/sa.key 2048

# 生成证书请求文件
openssl req -new \
  -key /etc/pki/tls/private/sa.key \
  -out /tmp/sa.req \
  -subj "/C=CN/ST=BJ/L=bj/O=rt/OU=tech/CN=sa.lab.com"

# 生成证书
openssl x509 -req \
  -days 366 \
  -in /tmp/sa.req \
  -signkey /etc/pki/tls/private/sa.key \
  -out /etc/pki/tls/certs/sa.crt
```

```bash
# dnf search ssl
# dnf -y install mod_ssl

# rpm -ql mod_ssl
# vim /etc/httpd/conf.d/ssl.conf
```

```ini
...
DocumentRoot "/var/www/html"
ServerName sa.lab.com
...
SSLCertificateFile /etc/pki/tls/certs/sa.crt
SSLCertificateKeyFile /etc/pki/tls/private/sa.key
...
```

```bash
# systemctl restart httpd
 
# firewall-cmd --permanent --add-service=https
# firewall-cmd --reload
 
# curl https://sa.lab.com
# curl -k https://sa.lab.com
```

**[root@workstation]**

```bash
# curl https://sa.lab.com
# curl -kv https://sa.lab.com
```



### 13.4 使用 Nginx 配置 Web 服务器

> 同一台机器，一个端口只能跑一个服务

**[root@servera]**

```bash
# dnf search nginx
# dnf -y install nginx
 
# rpm -qc nginx
# grep index /etc/nginx/nginx.conf.default
# vim /etc/nginx/nginx.conf
```

```ini
...
    server {
        listen       8080;
        server_name  _;  
        root         /usr/share/nginx/html;
        index  index.html index.htm;
...
```

```bash
# echo hello nginx > /usr/share/nginx/html/index.html
 
# systemctl enable --now nginx
 
# firewall-cmd --permanent --add-port=8080/tcp
# firewall-cmd --reload
 
# curl servera:8080
```

**[root@workstation]**

```bash
# curl servera:8080
```



## <strong style='color: #A30000'>14. 提供基于文件的网络存储</strong>

|  ID  | NAME | COMMENT                         |            |
| :--: | :--: | ------------------------------- | ---------- |
|  1   | DAS  | SATA, SCSI, USB                 | 直连存储   |
|  2   | NAS  | SMB（异构平台）, NFS（类 Unix） | 共享文件夹 |
|  3   | SAN  | iSCSI                           | 共享块设备 |



### 14.1 导出 NFS 文件系统 

> Unix, Linux

|  ID  |  Server   |   Client    |
| :--: | :-------: | :---------: |
| host |  servera  | Workstation |
| Pkgs | nfs-utils |  nfs-utils  |
|      |           |             |

**[root@servera]**

```bash
# dnf search nfs

# man -k nfs

# man exports
# vim /etc/exports
```

```ini
/path			*(ro)
```

```bash
# systemctl enable --now nfs-server

# showmount -e

# firewall-cmd --permanent --add-service=nfs \
  --add-service=rpc-bind \
  --add-service=mountd
# firewall-cmd --reload
```

**[root@workstation]**

```bash
# showmount -e servera
```

```ini
# mount servera:/ /media

# cat /media/etc/hostname 
servera.lab.example.com
# df /media/
Filesystem     1K-blocks    Used Available Use% Mounted on
servera:/        9756672 1747200   8009472  18% /media
```



### 14.2 提供 SMB 文件共享

> 无线路由，Windows， Linux, MacOS

|  ID  |               Server                | Client  |
| :--: | :---------------------------------: | :-----: |
| host |             foundation0             | Windows |
|  IP  |           192.168.147.129           |         |
| Pkgs | samba, samba-common<br>samba-client |         |

**[root@foundation]**

```bash
# dnf search samba
# dnf -y install samba

# vim /etc/samba/smb.conf.example
# vim /etc/samba/smb.conf
```

```ini
...
[gen]
       path = / 
       public = yes 
```

```bash
# systemctl enable --now smb 

# firewall-cmd --permanent --add-service=samba
# firewall-cmd --reload

# smbclient -L localhost -N

# (echo r; echo r) | smbpasswd -a root
```

> Windows
>
> ​	<kbd>Win</kbd>-<kbd>R</kbd> / ==\\\192.168.147.129==
>
> ​	输入帐号 ==root==， 密码 ==r==

**[root@workstation]**

```bash
# dnf -y install samba-client

# smbclient -L 172.25.254.250 -N
Anonymous login successful
...
	`gen`             Disk      

# smbclient //172.25.254.250/gen -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
smb: \> exit
```



## <strong style='color: #A30000'>15. 访问基于块的网络存储</strong>

### 15.1 iSCSI 存储 

> Windows Server 自带服务器端（Win Target）

|  ID  | Server  | Client  |
| :--: | :-----: | :-----: |
| Host | Servera | Bastion |
| PKGS | target* |  iscsi  |

**[root@servera]**

```bash
# dnf search target

# dnf -y install target*

# sysemctl enable --now target

# targetcli
/> help
/> ls
/> /backstores/block create dev=/dev/vdb blockb
/> /iscsi create wwn=iqn.1994-05.com.example:two        
/> /iscsi/iqn.1994-05.com.example:two/tpg1/luns create /backstores/block/blockb            
/> /iscsi/iqn.1994-05.com.example:two/tpg1/acls create iqn.1994-05.com.redhat:2c4be3a0bb44
/> exit
Global pref auto_save_on_exit=true

# firewall-cmd --permanent --add-service=iscsi-target
# firewall-cmd --reload
```



### 15.2 访问 iSCSI 存储

> win7 控制面板里，自带客户端

**[root@bastion]**

```bash
# dnf search iscsi

# dnf -y install iscsi-initiator-utils

# rpm -ql iscsi-initiator-utils | grep service
# systemctl enable --now iscsid iscsi

# vim /etc/iscsi/initiatorname.iscsi
```

```ini
InitiatorName=iqn.1994-05.com.redhat:2c4be3a0bb44
```

```bash
# systemctl restart iscsid
 
# man iscsiadm | grep iscsiadm.*-m -A 1
# iscsiadm --mode discoverydb --type sendtargets --portal servera --discover
# iscsiadm --mode node --targetname  iqn.1994-05.com.example:two --portal servera --login
 
# lsblk
 
# iscsiadm  -m session -P 3

```



## <strong style='color: #A30000'>16. 配置 Ftp 服务器</strong>

> ftp 明文, sftp 密文, tftp 传引导文件

### 16.1 服务器端配置

**[root@servera]**

```bash
# dnf search ftp 
# dnf -y install vsftpd
 
# rpm -qc vsftpd
# vim /etc/vsftpd/vsftpd.conf
```

```ini
...
anonymous_enable=YES
listen=YES
listen_ipv6=NO
```

```bash
# systemctl enable --now vsftpd
 
# firewall-cmd --permanent --add-service=ftp
# firewall-cmd --reload
 
# rpm -ql vsftpd | grep var 

```



### 16.2 客户端使用

**[root@foundation0]**

```bash
# dnf -y install lftp

# lftp -u ftp,'' servera

lftp ftp@servera:/> ls
lftp ftp@servera:/> exit

```



## <strong style='color: #A30000'>17. JumpServer 堡垒机</strong>

> https://github.com/jumpserver/jumpserver
>
> https://jumpserver.org/index.html

### 17.1 堡垒主机介绍

> JumpServer 是广受欢迎的开源堡垒机，是符合 4A 规范的专业运维==安全审计==系统。
>
> JumpServer 堡垒机帮助企业以更安全的方式管控和登录各种类型的资产，包括：
>
> - **SSH**: Linux / Unix / 网络设备 等；
> - **Windows**: Web 方式连接 / 原生 RDP 连接；
> - **数据库**: MySQL / MariaDB / PostgreSQL / Oracle / SQLServer / ClickHouse 等；
> - **NoSQL**: Redis / MongoDB 等；
> - **GPT**: ChatGPT 等;
> - **云服务**: Kubernetes / VMware vSphere 等;
> - **Web 站点**: 各类系统的 Web 管理后台；
> - **应用**: 通过 Remote App 连接各类应用。

### 17.2 配置方法

**[root@foundation0]**

```bash
# systemctl disable --now httpd

# curl -sSL https://resource.fit2cloud.com/jumpserver/jumpserver/releases/latest/download/quick_start.sh | bash
```

> http://localhost



## <strong style='color: #A30000'>18. kickstart 自动安装</strong>

### 18.1 pxe 网络安装

|  ID   |   SERVER<br>servera    | CLIENT<br>serverb | comment  |                                                              |                                                              |
| :---: | :--------------------: | :---------------: | :------: | :----------------------------------------------------------- | ------------------------------------------------------------ |
| **1** |          dhcp          |        pxe        |    IP    | next-server: 172.25.250.10;<br>filename "BOOTX64.efi";<br>filename "/pxelinux.0"; | 从 哪儿获得IP，<br>引导服务器地址<br>- UEFI 引导文件<br>- legcay 引导文件 |
|  2U   |      tftp-server       |       UEFI        |   boot   | /tftpboot/BOOTX64.efi<br>/tftpboot/==grub.cfg==              | 引导文件启动<br/>grub启动菜单                                |
|  2L   |      tftp-server       |      legcay       |   boot   | /tftpboot/pxelinux.0<br>/tftpboot/pxelinux.cfg/==default==   | 引导文件启动<br>grub启动菜单                                 |
|   3   |      tftp-server       |  kernel + ramfs   | anaconda | /tftpboot/{vmlinuz,initrd.img} <br>/tftpboot/{boot.msg,vesamenu.c32} | 内核，内存文件系统<br>message                                |
| **4** | **ftp**, <br>http, nfs |        ftp        |  manual  | append ... ==inst.stage2=ftp://172.25.250.10/dvd==           | *.rpm，append实现自动                                        |
|   5   |       kickstart        |        ks         |   auto   | append ... ==inst.ks=ftp://172.25.250.10/pub/ks.cfg==        | 无人值守                                                     |

#### 1 - dhcp

**[servera|172.25.250.10]**

```bash
# dnf -y install dhcp-server

# egrep -v '^$|^#' /usr/share/doc/dhcp-server/dhcpd.conf.example \
  > /etc/dhcp/dhcpd.conf

# vim /etc/dhcp/dhcpd.conf
```
```bash
option domain-name "example.com";
option domain-name-servers 172.25.250.220;
# 架构类型
option architecture-type code 93 = unsigned integer 16;

default-lease-time 600;
max-lease-time 7200;

log-facility local7;

subnet 172.25.250.0 netmask 255.255.255.0 {
  range 172.25.250.10 172.25.250.20;
  option routers 172.25.250.254;
}

# 两种同时
class "pxeclients" {
  match if substring (option vendor-class-identifier, 0, 9) = "PXEClient";
  next-server 172.25.250.10;
  if option architecture-type = 00:07 {
    filename "BOOTX64.EFI";
  }
  else {
    filename "pxelinux.0";
  }
}
```
```bash
# systemctl enable --now dhcpd

# firewall-cmd --permanent --add-service=dhcp
# firewall-cmd --reload
```
> - 测试1
>   serverb 网卡启动 -=> 可以获得 ip 地址

#### 2 - syslinux-tftpboot（准备tftp使用的文件）

```bash
# dnf -y install syslinux-tftpboot

# mkdir /tftpboot/pxelinux.cfg

# mkdir /content
# mount 172.25.254.250:/content /content
# cd /content/rhel9.0/x86_64/dvd
```
##### 2.1 EFI

```bash
# mount images/efiboot.img /media
# cp -r /media/EFI/BOOT/* /tftpboot

# vim /tftpboot/grub.cfg
```

```ini
#set default="1"
set default="0"
...
#set timeout=60
set timeout=6
...
menuentry 'Install Red Hat Enterprise Linux 9.0' --class fedora --class gnu-linux --class gnu --class os {
       #linuxefi /images/pxeboot/vmlinuz inst.stage2=hd:LABEL=RHEL-9-0-0-BaseOS-x86_64 quiet
       linuxefi vmlinuz inst.stage2=ftp://172.25.250.10/dvd inst.ks=ftp://172.25.250.10/pub/ks.cfg quiet
       #initrdefi /images/pxeboot/initrd.img
       initrdefi initrd.img
}
...
```

##### 2.2 legcay

```bash
# cp isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default
# vim /tftpboot/pxelinux.cfg/default
```

```ini
default vesamenu.c32
# timeout 600
timeout 60

display boot.msg
...
label linux
  menu label ^Install Red Hat Enterprise Linux 9.0
  # 第二个菜单 默认值，剪切过来
  menu default
  kernel vmlinuz
# append initrd=initrd.img inst.stage2=hd:LABEL=RHEL-9-0-0-BaseOS-x86_64 quiet  
  append initrd=initrd.img inst.stage2=ftp://172.25.250.10/dvd inst.ks=ftp://172.25.250.10/pub/ks.cfg quiet
...
```
```bash
# cp images/pxeboot/{vmlinuz,initrd.img} /tftpboot/
```
> - 网络
>   - **inst.ks=ftp://172.25.250.10/pub/ks.cfg**
>   - inst.ks=http://172.25.250.10/pub/ks.cfg
>   - inst.ks=nfs:172.25.250.10:/var/ftp/pub/ks.cfg
> - 本地
>   - inst.ks=cdrom:/ks.cfg
>   - inst.ks=hd:/ks.cfg

#### 3 - tftp-server

```bash
# dnf -y install tftp-server

# vim /usr/lib/systemd/system/tftp.service
```
```ini
...
#ExecStart=/usr/sbin/in.tftpd -s /var/lib/tftpboot
ExecStart=/usr/sbin/in.tftpd -s /tftpboot
```
```bash
# systemctl enable --now tftp

# firewall-cmd --permanent --add-service=tftp
# firewall-cmd --reload
```
> - 测试2
>   servera -=>
>   - ip
>     - BOOTX64.EFI, grub.cfg
>     - pxelinux.0, default
>       - {kernel, initramfs}, 
>         - manual-install

#### 4 - ftp

```bash
# dnf -y install vsftpd

# vim /etc/vsftpd/vsftpd.conf 
```
```ini
...
#anonymous_enable=NO
anonymous_enable=YES
```
```bash
# systemctl enable --now vsftpd

# mkdir /var/ftp/dvd
# mount /content/rhel9.0/x86_64/isos/rhel-baseos-9.0-x86_64-dvd.iso /var/ftp/dvd
# setsebool -P ftpd_full_access 1

# firewall-cmd --permanent --add-service=ftp
# firewall-cmd --reload

# curl ftp://servera
# curl ftp://servera/dvd/
```

#### 5. [kickstart](https://access.redhat.com/labs/kickstartconfig/)

> 在线生成、下载
> https://access.redhat.com/labs/

```bash
# vim /var/ftp/pub/ks.cfg
```
```ini
lang en_US
keyboard --xlayouts='us'
timezone Asia/Shanghai --utc
rootpw $2b$10$ZZwcHHI7d1Bw8pYni/gVNuVxSfAv.pQKrKfEcmYi6FmNjHLAS.9u6 --iscrypted
url --url=ftp://172.25.250.10/dvd
bootloader --append="console=ttyS0 console=ttyS0,115200n8 no_timer_check net.ifnames=0 crashkernel=auto"
zerombr
clearpart --all --initlabel
autopart
network --bootproto=dhcp
skipx
firstboot --disable
selinux --enforcing
firewall --enabled --ssh

%post
useradd tom
echo ttt | passwd --stdin tom
%end

%packages
@^minimal-environment
kexec-tools
%end
```
#### 6. PXE

**[serverb]**

> 网卡启动，**6** 秒倒计时，自动开始安装

```bash
Login: `root`
Password: `redhat`

# id tom
```



### 18.2 光盘 kickstart

**serverb**

> 1. 关机
>
> 2. 添加光驱
>
> 3. 插入光盘==/content/rhel9.0/x86_64/isos/rhel-baseos-9.0-x86_64-dvd.iso==
>
> 4. 光驱启动
>
> 5. 启动
>
> 6. <kbd>Install Red Hat Enterprise Linux 9.0</kbd>
>    <kbd>e</kbd> 
>
> 7. ```bash
>    ...
>    linuxefi ...` inst.ks=ftp://172.25.250.10/pub/ks.cfg`
>    ```
>
>    <kbd>Ctrl</kbd>-<kbd>x</kbd>

```ini
lang en_US
keyboard --xlayouts='us'
timezone Asia/Shanghai --utc
rootpw $2b$10$ZKSvUH1ob4dfMsEiPxuov.td23IkkwxVVVMxh1QUYm4wXhHCThwKq --iscrypted
cdrom
bootloader --append="console=ttyS0 console=ttyS0,115200n8 no_timer_check net.ifnames=0 crashkernel=auto"
zerombr
clearpart --all --initlabel
autopart
network --bootproto=dhcp
skipx
firstboot --disable
selinux --enforcing
firewall --enabled --ssh
%post
useradd tom
echo ttt | passwd --stdin tom
%end
%packages
@^minimal-environment
kexec-tools
%end
```

