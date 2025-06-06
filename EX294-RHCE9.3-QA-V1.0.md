<strong style='color: #00B9E4'>重要配置信息</strong>

在练习期间，除了您就坐位置的台式机之外，还将使用多个虚拟系统。您不具有台式机系统的 root 访问权，但具有对虚拟系统的完整 root 访问权。

#### 系统信息

在本练习期间，您将操作下列虚拟系统：

|  系统   |    IP 地址     |     Ansible 角色     |
| :-----: | :------------: | :------------------: |
| control | 172.25.250.254 | ansible control node |
|  node1  |  172.25.250.9  | ansible managed node |
|  node2  | 172.25.250.10  | ansible managed node |
|  node3  | 172.25.250.11  | ansible managed node |
|  node4  | 172.25.250.12  | ansible managed node |
|  node5  | 172.25.250.13  | ansible managed node |

这些系统的 IP 地址采用静态设置。请勿更改这些设置。

主机名称解析已配置为解析上方列出的完全限定主机名，同时也解析主机短名称。

#### 帐户信息

所有系统的 root 密码是 `flectrag`。

请勿更改 root 密码。除非另有指定，否则这将是用于访问其他系统和服务的密码。此外，除非另有指定，否则此密码也应用于您创建的所有帐户，或者任何需要设置密码的服务。

为方便起见，所有系统上已预装了 SSH 密钥，允许在不输入密码的前提下通过 SSH 进行 root 访问。请勿对系统上的 root SSH 配置文件进行任何修改。

Ansible 控制节点上已创建了用户帐户 greg。此帐户预装了 SSH 密钥，允许在 Ansible 控制节点和各个 Ansible 受管节点之间进行 SSH 登录。请勿对系统上的 greg SSH 配置文件进行任何修改。您可以从 root 帐户使用 su 访问此用户帐户。

<div style="background: #ffedcc; padding: 12px; line-height: 24px; margin-bottom: 24px;"><b>重要信息</b><br>
除非另有指定，否则您的所有工作（包括 Ansible playbook、配置文件和主机清单等）应当保存在控制节点上的目录 <font style="color: red">/home/greg/ansible</font> 中，并且应当归 greg 用户所有。所有 Ansible 相关的命令应当由 <font style="color: red">greg</font> 用户从 Ansible 控制节点上的这个目录运行。
</div>


#### 其他信息

一些练习项目可能需要修改 Ansible 主机清单。您要负责确保所有以前的清单组和项目保留下来，与任何其他更改共存。您还要有确保清单中所有默认的组和主机保留您进行的任何更改。

`练习系统上的防火墙默认为不启用，SELinux 则处于强制模式。`

如果需要安装其他软件，您的物理系统和 Ansible 控制节点可能已设置为指向 `content` 上的下述存储库：

- http://content/rhel9.3/x86_64/dvd/BaseOS

- http://content/rhel9.3/x86_64/dvd/AppStream

一些项目需要额外的文件，这些文件已在以下位置提供：

- http://classroom/materials

产品文档可从以下位置找到：

- http://materials/docs

容器镜像仓库信息：utility.lab.example.com

- 账号：admin
- 密码：redhat

其他资源也进行了配置，供您在练习期间使用。关于这些资源的具体信息将在需要这些资源的项目中提供。

<div style="background: #ffedcc; padding: 12px; line-height: 24px; margin-bottom: 24px;"><b>重要信息</b><br>
请注意，在评分之前，您的 Ansible 受管节点系统将重置为考试开始时的初始状态，您编写的 Ansible playbook 将通过以 <font style="color: red">greg</font> 用户身份从控制节点上的目录 <font style="color: red">/home/greg/ansible</font> 目录运行来应用。在 playbook 运行后，系统会对您的受管节点进行评估，以判断它们是否按照规定进行了配置。
</div>


<h2>练习要求</h2>

在您的系统上执行以下所有步骤。

[toc]

## 1. 安装和配置 Ansible
> **安装和配置 Ansible**
>
> 按照下方所述，在控制节点 `control` 上安装和配置 Ansible：
>
> - [x] 安装所需的软件包
> - [x] 创建名为 `/home/greg/ansible/inventory` 的静态清单文件，以满足以下要求：
>   - [x] `node1` 是 `dev` 主机组的成员
>   - [x] `node2` 是 `test` 主机组的成员
>   - [x] `node3` 和 `node4` 是 `prod` 主机组的成员 
>   - [x] `node5` 是 `balancers` 主机组的成员
>   - [x] `prod` 组是 `webservers` 主机组的成员
> - [ ] 创建名为 `/home/greg/ansible/ansible.cfg` 的配置文件，以满足以下要求：
>   - [ ] 主机清单文件为 `/home/greg/ansible/inventory`
>   - [ ] playbook 中使用的角色的位置包括 `/home/greg/ansible/roles`
>   - [ ] 自定义的collection目录在`/home/greg/ansible/mycollection`

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
  <li> <b>vars</b>, <b>children</b><br>
    Docs » User Guide » Working with Inventory
  <li> <b>roles_path</b>, 使用RHEL系统角色
  <li> <b>host_key_checking</b> 配置文件的参数； ssh 命令连接时，输入 ignore yes*5
</div>

<div style="background: #e7f2fa; padding: 12px; line-height: 24px; margin-bottom: 24px; ">
<dt style="background: #6ab0de; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; padding: 6px 12px; margin-bottom: 12px;" >Note - inventory</dt>
  <b>inventory</b>
  <li> /etc/ansible/hosts(不全)
  <li> $ ansible-doc -t inventory ini<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# ansible-doc -t inventory -l  <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# ansible-doc -h
  <li> Docs » User Guide » How to build your inventory
</div>
<img width=35 src="https://i0.hdslb.com/bfs/album/556a153ed2f3ffb9343c691ade3d2cb11b87a7ae.png">**[foundation]**

```bash
远程登陆
*$ ssh root@control
*$ ssh greg@localhost
```

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
安装ansible
*$ sudo yum search ansible

*$ sudo yum -y install ansible-core ansible-navigator

创建文件夹
*$ mkdir -p /home/greg/ansible/roles

切换工作目录
*$ cd /home/greg/ansible

拷贝配置文件
$ rpm -qc ansible-core
$ cat /etc/ansible/ansible.cfg
*$ ansible-config init --disabled > /home/greg/ansible/ansible.cfg

创建集合存储目录
*$ mkdir /home/greg/ansible/mycollection

编辑配置文件
*$ vim ansible.cfg
```
```ini
...此处省略...
[defaults]
## 配置文件中，按题意要求修改
inventory = /home/greg/ansible/inventory
remote_user = greg
## 配置文件中，默认存在，不生效。
## 相关于ssh 选项 StrictHostKeyChecking=no,不检测主机指纹。
## 建议配置。不配置的话，需要ssh分别yes保留主机指纹
host_key_checking = False

#这行配置，做14题时候再加，现在加上后，影响16题改密码。 
vault_password_file = /home/greg/ansible/secret.txt

#4.
roles_path = /home/greg/ansible/roles
collections_path = /home/greg/ansible/mycollection:~/.ansible/collections:/usr/share/ansible/collections

#greg（ssh免密，sudo免密）,考试时建议使用
[privilege_escalation]
become=True
```
```bash
确认生效的配置文件
$ ansible --version
ansible [core 2.13.0]
  config file = `/home/greg/ansible/ansible.cfg`
  ...
$ ansible-galaxy list
# /home/greg/ansible/roles

$ ansible-doc -l -t inventory  #清单帮助
$ ansible-doc -t inventory ini #清单帮助
*$ vim /home/greg/ansible/inventory
```
```ini
[dev]
node1

[test]
node2

[prod]
node3
node4

[balancers]
node5

[webservers:children]
prod 
```

```bash
*$ podman login utility.lab.example.com -u admin -p redhat
$ ansible-navigator images
*$ ansible-navigator collections 
#验证：此命令可以验证podman的登录、执行环境下载正确，查看集合。
```

```bash
*$ ansible-inventory --graph
$ ansible all -a id

*$ ansible all -m ping
# 验证： 如果可以ping通所有节点，证明配置文件、账户、清单都没有问题。
```



```bash
$ ansible all -a whoami
node2 | CHANGED | rc=0 >>
`root`

node4 | CHANGED | rc=0 >>
`root`

node5 | CHANGED | rc=0 >>
`root`

node3 | CHANGED | rc=0 >>
`root`

node1 | CHANGED | rc=0 >>
`root`
```




## 2. 配置您的系统以使用默认存储库

> **配置您的系统以使用默认存储库**
>
> 作为系统管理员，您需要在受管节点上安装软件。
>
> 请按照正文所述，创建一个名为 `/home/greg/ansible/yum_repo.yml`，在各个受管节点上安装 yum 存储库：
>
> 存储库1：
>
> - [ ] 存储库的名称为 `EX294_BASE`
> - [ ] 描述为 `EX294 base software`
> - [ ] 基础 URL 为 `http://content/rhel9.0/x86_64/dvd/BaseOS`
> - [ ] GPG 签名检查为`启用状态`
> - [ ] GPG 密钥 URL 为 `http://content/rhel9.0/x86_64/dvd/RPM-GPG-KEY-redhat-release`
> - [ ] 存储库为`启用状态`
>
> 存储库2：
>
> - [ ] 存储库的名称为 `EX294_STREAM`
> - [ ] 描述为 `EX294 stream software`
> - [ ] 基础 URL 为 `http://content/rhel9.0/x86_64/dvd/AppStream`
> - [ ] GPG 签名检查为`启用状态`
> - [ ] GPG 密钥 URL 为 `http://content/rhel9.0/x86_64/dvd/RPM-GPG-KEY-redhat-release`
> - [ ] 存储库为`启用状态`

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
Docs » User Guide » Introduction To Ad-Hoc Commands
</div>


<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

0.1 确认`各个受管节点`==`all`

```bash
$ ansible-inventory --graph
```
0.2 查模块名称

```bash
$ ansible-doc -l | grep yum
```

0.3 查模块用法。==/EX==，在手册中搜索 EXAMPLE 示例

```bash
$ ansible-doc yum_repository
```

1. 编辑剧本

```bash
$ vim /home/greg/ansible/yum_repo.yml
```

```yaml
---
- name: repository
  hosts: all
  tasks:
  - ansible.builtin.yum_repository:
      file: rhel  #file相同，会将配置放到相同repo中。
      name: EX294_BASE
      description: "EX294 base software"
      baseurl: http://content/rhel9.0/x86_64/dvd/BaseOS
      gpgcheck: yes
      gpgkey: http://content/rhel9.0/x86_64/dvd/RPM-GPG-KEY-redhat-release
      enabled: yes

  - ansible.builtin.yum_repository: 
     file: rhel
     name: EX294_STREAM
     description: "EX294 stream software"
     baseurl: http://content/rhel9.0/x86_64/dvd/AppStream
     gpgcheck: yes
     gpgkey: http://content/rhel9.0/x86_64/dvd/RPM-GPG-KEY-redhat-release
     enabled: yes
```
2. 运行剧本

```bash
$ ansible-navigator run yum_repo.yml -m stdout
```

3. 验证两个仓库（BaseOS, AppStream）

```bash
$ ansible all -a 'yum -y remove yum-utils'
$ ansible all -a 'yum -y install yum-utils ftp'
$ ansible all -a 'rpm -q yum-utils ftp'
#验证：验证所有节点，若软件安装完毕，证明配置成功。
```



## 3. 安装软件包
> **安装软件包**
>
> 创建一个名为 `/home/greg/ansible/packages.yml` 的 playbook :
>
> - [x] 将 `php` 和 `mariadb` 软件包安装到 `dev`、`test` 和 `prod` 主机组中的主机上
> - [x] 将 `RPM Development Tools` 软件包组安装到 `dev` 主机组中的主机上
> - [x] 将 `dev` 主机组中主机上的`所有软件包更新为最新版本`

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
Docs » User Guide » Working With Playbooks » Intro to Playbooks
</div>
<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ ansible-doc -l | grep yum
$ ansible-doc yum

$ echo set nu ts=2 et cuc sw=2 > ~/.vimrc

$ vim /home/greg/ansible/packages.yml
```
```yaml
---
- name: play 1
  hosts: dev,test,prod
  tasks:
  - name: ensure a list of packages installed
    ansible.builtin.yum:
      name: "{{ packages }}"
    vars:
      packages:
      - php
      - mariadb

- name: play 2
  hosts: dev
  tasks:
  - name: install the package group
    ansible.builtin.yum:
      name: "@RPM Development Tools"
      state: present
  - name: upgrade all packages
    ansible.builtin.yum:
      name: '*'
      state: latest

```
```bash
$ ansible-navigator run packages.yml -m stdout
```

```bash
$ ansible dev,test,prod -a 'rpm -q php mariadb'
$ ansible all -m shell  -a "yum grouplist  'RPM Development Tools'"
...
Installed Groups:
   RPM Development Tools
$ ansible dev -a 'yum update'
...
Nothing to do.
#验证： 通过查看软件安装情况，确认题目正确。
```



## 4A. 使用 RHEL 系统角色

> **使用 RHEL 系统角色**
>
> 安装 RHEL 系统角色软件包，并创建符合以下条件的 playbook `/home/greg/ansible/selinux.yml` ：
>
> - [ ] 在`所有受管节点`上运行
> - [ ] 使用 `selinux` 角色
> - [ ] 配置该角色，配置被管理节点的 selinux 为`enforcing`

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
<li>系统角色就是安装包</li>
<li>注意系统角色路径</li>
<li>系统角色要不看<b>example</b>，要不看<b>README</b></li>
  <li>删除操作 <strong>:23,30d</strong> <strong>:9,19d</strong>
<li>先做5题再做4题</li>
</div>

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ vim ansible.cfg
```

```ini
...
roles_path=/home/greg/ansible/roles
collections_path=./mycollection/:.ansible/collections:/usr/share/ansible/collections
#collections_path=/home/greg/ansible/mycollection:~/.ansible/collections:/usr/share/ansible/collections
```

```bash
$ sudo find ./mycollection/ -name '*.yml' | grep selinux #不安装角色包可以使用该条命令搜索
$ cp /home/greg/ansible/mycollection/ansible_collections/redhat/rhel_system_roles/docs/timesync/selinux-playbook.yml /home/greg/ansible/selinux.yml

$ vim /home/greg/ansible/selinux.yml
```

```yaml
---
- hosts: all 
  vars:
    selinux_policy: targeted
    selinux_state: enforcing

# Prepare the prerequisites required for this playbook
  tasks:
    - name: Creates directory
      file:
        path: /tmp/test_dir
        state: directory
        mode: "0755"
    - name: Add a Linux System Roles SELinux User
      user:
        comment: Linux System Roles SELinux User
        name: sar-user
    - name: execute the role and catch errors
      block:
        - name: Include selinux role
          include_role:
            name: redhat.rhel_system_roles.selinux
      rescue:
    # Fail if failed for a different reason than selinux_reboot_required.
        - name: handle errors
          fail:
            msg: "role failed"
          when: not selinux_reboot_required

        - name: restart managed host
          reboot:

        - name: wait for managed host to come back
          wait_for_connection:
            delay: 10
            timeout: 300 

        - name: reapply the role
          include_role:
            name: redhat.rhel_system_roles.selinux
```

简洁版 

```bash
---
- hosts: all
  vars:
    selinux_policy: targeted
    selinux_state: enforcing
  roles:
  - redhat.rhel_system_roles.selinux #简洁版和上面旧版的区别就是需要自己编写roles字段内容。

```



```bash
$ ansible-navigator run selinux.yml -m stdout
#使用rpm包安装的角色，还是用ansible-playbook执行
#使用集合安装的角色，要用ansible-navigator执行
#除了使用方法不同以外，角色路径和集合路径也是不一样的，不能混用。
```

```bash
$ ansible all -m shell -a 'grep ^SELINUX= /etc/selinux/config'
node2 | CHANGED | rc=0 >>
SELINUX=enforcing
Enforcing
node4 | CHANGED | rc=0 >>
SELINUX=enforcing
Enforcing
node5 | CHANGED | rc=0 >>
SELINUX=enforcing
Enforcing
node3 | CHANGED | rc=0 >>
SELINUX=enforcing
Enforcing
node1 | CHANGED | rc=0 >>
SELINUX=enforcing
Enforcing
#验证：结果应为所有节点状态为Enforcing，且配置文件/etc/selinux/config内为SELINUX=enforcing
```

## 4B. 使用 RHEL 系统角色

> **使用 RHEL 系统角色**
>
> 安装 RHEL 系统角色软件包，并创建符合以下条件的 playbook `/home/greg/ansible/timesync.yml` ：
>
> - [ ] 在`所有受管节点`上运行
> - [ ] 使用 `timesync` 角色
> - [ ] 配置该角色，以使用当前有效的 NTP 提供商
> - [ ] 配置该角色，以使用时间服务器 `172.25.254.254`
> - [ ] 配置该角色，以启用 `iburst` 参数

**[172.25.250.254|control]**

```bash
$ vim ansible.cfg
```

```ini
...
collections_path=./mycollection/:.ansible/collections:/usr/share/ansible/collections
#collections_path=/home/greg/ansible/mycollection:~/.ansible/collections:/usr/share/ansible/collections
```

```bash
$ find /home/greg/ansible/mycollection/ -name '*.yml' | grep timesync  #不安装角色包可以使用该条命令搜索
$ cp /home/greg/ansible/mycollection/ansible_collections/redhat/rhel_system_roles/docs/timesync/single-pool.yml /home/greg/ansible/timesync.yml

$ vim /home/greg/ansible/timesync.yml
```

```yaml
---
- hosts: all 
  vars:
    timesync_ntp_servers:
      - hostname: 172.25.254.254
        iburst: yes 
  roles:
    - redhat.rhel_system_roles.timesync
```

```bash
$ ansible-navigator run -m stdout  timesync.yml
```

```bash
$ ansible all -m shell -a 'chronyc sources'
$ ansible all -m shell -a 'grep ^server /etc/chrony.conf'
$ ansible all -m shell -a 'timedatectl | grep -B 1 NTP'
```

```bash
#验证，保证看到^* classroom.lab.example.com
[greg@control ansible]$ ansible all -m shell -a ' chronyc sources'
node2 | CHANGED | rc=0 >>
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* classroom.lab.example.com     8   6    17    58  +5468us[+5540us] +/- 6255us
node4 | CHANGED | rc=0 >>
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* classroom.lab.example.com     8   6    17    58  +8791ns[  +31us] +/-  470us
node3 | CHANGED | rc=0 >>
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* classroom.lab.example.com     8   6    17    58   -137us[ -328us] +/- 1354us
node5 | CHANGED | rc=0 >>
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* classroom.lab.example.com     8   6    17    58  +6828ns[ +189us] +/-  817us
node1 | CHANGED | rc=0 >>
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* classroom.lab.example.com     8   6    17    58    +38us[ +242us] +/-  401us

```



## 5. 配置collection

> **配置 collection**
>
> - [ ] http://classroom/materials/
>   - redhat-insights-1.0.7.tar.gz
>   - community-general-5.5.0.tar.gz
>   - redhat-rhel_system_roles-1.19.3.tar.gz
> - [ ] 上面3个安装在`/home/greg/ansible/mycollection`目录中

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
*$ vim requirements.yml
```

```yaml
---
collections:
- name: http://classroom/materials/redhat-insights-1.0.7.tar.gz
- name: http://classroom/materials/community-general-5.5.0.tar.gz
- name: http://classroom/materials/redhat-rhel_system_roles-1.19.3.tar.gz
```

```bash
*$ ansible-galaxy collection install \
  -r requirements.yml \
  -p /home/greg/ansible/mycollection
```

```bash
*$ ansible-galaxy collection list
*$ ansible-navigator collections list
$ ansible-navigator collections
$ ansible-navigator doc community.general.filesystem -m stdout
#此题若未完成影响 4题系统角色，‘7题-创建使用角色’，及 ‘9题-逻辑卷’
#通过查看集合是否存在，判定集合是否安装，也可在ansible-navigator collections直接观察角色是否安装
```



## 6. 使用 Ansible Galaxy 安装角色

> **使用 Ansible Galaxy 安装角色**
>
> 使用 Ansible Galaxy 和要求文件 `/home/greg/ansible/roles/requirements.yml` 。从以下 URL 下载角色并安装到 `/home/greg/ansible/roles` ：
>
> - [ ] `http://classroom/materials/haproxy.tar` 此角色的名称应当为 `balancer`
> - [ ] `http://classroom/materials/phpinfo.tar` 此角色的名称应当为 `phpinfo`

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
  <li> <b>requirements.yml</b></li>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Docs » Galaxy User Guide /<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Installing roles from Galaxy /<br> 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Installing multiple roles from a file
</div>
<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ vim /home/greg/ansible/roles/requirements.yml
```

```yaml
---
- src: http://classroom/materials/haproxy.tar
  name: balancer
- src: http://classroom/materials/phpinfo.tar
  name: phpinfo
```

```bash
$ ansible-galaxy install -r /home/greg/ansible/roles/requirements.yml 
```

```bash
$ ansible-galaxy list
# /home/greg/ansible/roles
...
- balancer, (unknown version)
- phpinfo, (unknown version)
...
#验证：查看到两个角色即可。
```



## 7. 创建和使用角色
> **创建和使用角色**
>
> 根据下列要求，在 `/home/greg/ansible/roles` 中创建名为 `apache` 的角色：
>
> - [ ] httpd 软件包已安装，设为在`系统启动时启用`并`启动`
>
> - [ ] `防火墙`已启用并正在运行，并使用允许访问 `Web` 服务器的规则
>
> - [ ] 模板文件 `index.html.j2` 已存在，用于创建具有以下输出的文件 `/var/www/html/index.html` ：
>
>     ```
>     Welcome to HOSTNAME on IPADDRESS
>   ```
>
>   其中，HOSTNAME 是受管节点的`完全限定域名`，`IPADDRESS` 则是受管节点的 IP 地址。
>
> - [ ] 创建一个名为 `/home/greg/ansible/apache.yml` 的 playbook：
>
>
> - [ ] 该 play 在 `webservers` 主机组中的主机上运行并将使用 `apache` 角色



<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
方法A，切换当前目录
默认生成在当前目录
$ cd roles/
$ ansible-galaxy init apache
ansible 生效的配置文件
$ cd ..

方法B，指明角色目录
生成在在指定的目录
$ cd /home/greg/ansible/
$ ansible-galaxy init roles/apache
```

```bash
$ ansible-galaxy list
```

```bash
$ vim roles/apache/tasks/main.yml
```
```yaml
---
- name: Install the latest version of Apache
  ansible.builtin.yum:
    name: httpd
    state: latest
- name: Start service httpd, if not started
  ansible.builtin.systemd:
    name: httpd
    state: started
    enabled: yes
- name: Start service firewalld, if not started
  ansible.builtin.systemd:
    name: firewalld
    state: started
    enabled: yes
#考试时先做第五题否则没有posix集合
- name: permit apache
  ansible.posix.firewalld:
    service: http
    permanent: yes
    state: enabled
    immediate: yes
- name: j2
  ansible.builtin.template:
# path = roles/apache/templates/
    src: index.html.j2
    dest: /var/www/html/index.html
```
```bash
$ ansible node1 -m setup | grep node1
$ ansible node1 -m setup -a 'filter=*ip*'

$ vim roles/apache/templates/index.html.j2
```
```jinja2
Welcome to {{ ansible_fqdn }} on {{ ansible_default_ipv4.address }}
```

```bash
$ vim /home/greg/ansible/apache.yml
```

```yaml
---
- name: 创建和使用角色
  hosts: webservers
  roles:
  - apache
```

```bash
$ ansible-navigator run apache.yml -m stdout

$ ansible webservers --list-hosts
  hosts (2):
    node3
    node4
```

<img width=35 src="https://i0.hdslb.com/bfs/album/556a153ed2f3ffb9343c691ade3d2cb11b87a7ae.png">**[foundation]**

```bash
$ curl http://node3
Welcome to node3.lab.example.com on 172.25.250.11

$ curl http://node4
Welcome to node4.lab.example.com on 172.25.250.12

#验证：通过访问node3、4返回网页结果（可用命令行或浏览器），判断角色是否执行正常。
```




## 8. 从 Ansible Galaxy 使用角色
> **从 Ansible Galaxy 使用角色**
>
> 根据下列要求，创建一个名为 `/home/greg/ansible/roles.yml` 的 playbook ：
>
> - [x] playbook 中包含一个 play， 该 play 在 `balancers` 主机组中的主机上运行并将使用 `balancer` 角色。
>
>   - [x] 此角色配置一项服务，以在 `webservers` 主机组中的主机之间平衡 Web 服务器请求的负载。
>
>   - [ ] 浏览到 `balancers` 主机组中的主机（例如 `http://172.25.250.13` ）将生成以下输出：
>
>     ```
>     Welcome to node3.lab.example.com on 172.25.250.11
>     ```
>
>   - [ ] 重新加载浏览器将从另一 Web 服务器生成输出：
>
>     ```
>     Welcome to node4.lab.example.com on 172.25.250.12
>     ```
>
> - [x] playbook 中包含一个 play， 该 play 在 `webservers` 主机组中的主机上运行并将使用 `phpinfo` 角色。
>
>   - [ ] 请通过 URL `/hello.php` 浏览到 `webservers` 主机组中的主机将生成以下输出：
>
>     ```
>     Hello PHP World from FQDN
>     ```
>
>    - [ ] 其中，FQDN 是主机的完全限定名称。
>
>      ```
>      Hello PHP World from node3.lab.example.com
>      ```
>   
>
> 另外还有 PHP 配置的各种详细信息，如安装的 PHP 版本等。
>
> - [ ] 同样，浏览到 `http://172.25.250.12/hello.php` 会生成以下输出：
>
>      ```
>      Hello PHP World from node4.lab.example.com
>      ```
>
>
>  另外还有 PHP 配置的各种详细信息，如安装的 PHP 版本等。

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
  <li> <b>roles</b> 角色关键词直接查网页<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Docs » User Guide » Working With Playbooks » Creating Reusable Playbooks » Roles
</div>
<div style="background: #f0f0f0; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #ee0000; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Important - 重要</dt>
  <li>考试环境中，必须：<br>
    第一个play, webservers；<br>
    第二个play, balancers</li>
</div>


<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ vim /home/greg/ansible/roles.yml
```
```yaml
---
- name: 从 Ansible Galaxy 使用角色 1  #考试时phpinfo角色要在balancer之前做
  hosts: webservers
  roles:
  - phpinfo

- name: 从 Ansible Galaxy 使用角色 2
  hosts: balancers
  roles:
  - balancer
```
```bash
$ ansible-navigator run roles.yml -m stdout 
```
<img width=35 src="https://i0.hdslb.com/bfs/album/556a153ed2f3ffb9343c691ade3d2cb11b87a7ae.png">**[foundation]**

```bash
$ curl http://172.25.250.13
Welcome to node3.lab.example.com on 172.25.250.11

$ curl http://172.25.250.13
Welcome to node4.lab.example.com on 172.25.250.12
```

<kbd>🦊firefox</kbd>

```bash
- `http://172.25.250.11/hello.php`
- `http://172.25.250.12/hello.php`

#验证： 推荐浏览器访问，可以看到页面内容为php版本信息，整体为紫色，且两个地址11,12,13都可访问即可。
```



## 9A. 创建和使用逻辑卷

> **创建和使用逻辑卷**
>
> 创建一个名为 `/home/greg/ansible/lv.yml` 的 playbook ，它将在`所有受管节点`上运行以执行下列任务：
>
> - [x] 创建符合以下要求的逻辑卷：
>
>   - [x] 逻辑卷创建在 `research` 卷组中
>   - [x] 逻辑卷名称为 `data`
>   - [x] 逻辑卷大小为 `1500 MiB`
>
> - [x] 使用 `ext4` 文件系统格式化逻辑卷
>
> - [x] 如果无法创建请求的逻辑卷大小，应显示错误信息
>
>     ```
>     Could not create logical volume of that size
>     ```
>
>      ，并且应改为使用大小 `800 MiB`。
>
> - [ ] 如果卷组 `research` 不存在，应显示错误信息
>
>     ```
>     Volume group done not exist
>     ```
>
>     。
>
> - [ ] 不要以任何方式挂载逻辑卷

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
Docs » User Guide » Working With Playbooks » Blocks
</div>

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ ansible-navigator doc community.general.lvol -m stdout  #先做第五题否则未没有lvg的集合，查不到模块
$ ansible-navigator doc community.general.filesystem -m stdout
$ ansible-doc debug

$ ansible-doc stat
$ ansible all -m setup -a 'filter=*lvm*'

$ vim /home/greg/ansible/lv.yml
```
```yaml
---
- name: haha
  hosts: all
  tasks:
  - block:
    - name: lv 1500M
      community.general.lvol:
        vg: research
        lv: data
        size: 1500
    - name: Create a ext4
      community.general.filesystem:
        fstype: ext4
        dev: /dev/research/data
    rescue:
    - name: Could not create logical volume of that size
      ansible.builtin.debug:
        msg: Could not create logical volume of that size
    - name: lv 800M
      community.general.lvol:
        vg: research
        lv: data
        size: 800
    - name: Create a ext4
      community.general.filesystem:
        fstype: ext4
        dev: /dev/research/data
    when: ansible_lvm.vgs.research is defined
  - name:  
    ansible.builtin.debug:
      msg: Volume group done not exist
    when: ansible_lvm.vgs.research is not defined
```
```bash
$ ansible-navigator run /home/greg/ansible/lv.yml -m stdout
PLAY [创建和使用逻辑卷] 

TASK [Gathering Facts] **********************************************************************************************************************
ok: [node5]
ok: [node3]
ok: [node2]
ok: [node4]
ok: [node1]

TASK [Create a logical volume] *****************************************************************************************
skipping: [node1]
fatal: [node3]: FAILED! => {"changed": false, "err": "  Volume group \"research\" has insufficient free space (31 extents): 47 required.\n", "msg": "Creating logical volume 'data' failed", "rc": 5}
changed: [node5]
changed: [node4]
changed: [node2]

TASK [Create a ext4 filesystem] *****************************************************************************************
skipping: [node1]
changed: [node5]
changed: [node2]
changed: [node4]

TASK [ansible.builtin.debug] *****************************************************************************************
ok: [node3] => {
    "msg": "Could not create logical volume of that size"
}

TASK [Create a logical volume] **************************************************************************************************************
changed: [node3]

TASK [debug] ********************************************************************************************************************************
skipping: [node2]
skipping: [node5]
skipping: [node3]
ok: [node1] => {
    "msg": "Volume group done not exist"
}
skipping: [node4]

PLAY RECAP **********************************************************************************************************************************
node1                      : ok=2    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
node2                      : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
node3                      : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=1    ignored=0   
node4                      : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
node5                      : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```

```bash
$ ansible all -m shell -a 'lvs'
$ ansible all -m shell -a 'blkid /dev/research/data'

#观察ansible剧本执行过程，是否符合题意，结合blkid命令查询。
```



## 9B.[创建和使用分区]()

> 创建一个名为 /home/greg/ansible/partition.yml的 playbook，它将在prod 主机上运行以执行下列任务:
>
> - [ ] 该playbook只有一个play
>
> - [ ] 在prod主机组中创建分区
> - [x] vdd创建一个1500M主分区，分区号为1,格式化ext4,并挂载到/newpart
> - [x] vde创建一个1500M主分区，分区号为1,格式化ext4,并挂载到/newpart10
> - [ ] 如果磁盘空间不够，给出如下提示信息
>
> ```
> Could not create partition of that size
> ```
>
> - [ ] 改为创建800M分区
> - [ ] 如果vde不存在，应显示提示信息
>
> ```
> this disk is not exist
> ```

```BASH
$ vim /home/greg/ansible/partition.yml
```

```yaml
---
- name: create and use partition
  hosts: prod
  tasks:
    - block:
        - name: Create a partition with 1500m
          community.general.parted:
            device: "{{ item }}"
            number: 1
            state: present
            part_end: 1500MiB
          loop:
            - /dev/vdd
            - /dev/vde
      rescue:
        - name: report no 1500m
          ansible.builtin.debug:
            msg: Could not create partition of that size
        - name: Create a partition with 800m
          community.general.parted:
            device: "{{ item }}"
            number: 1
            state: present
            part_end: 800MiB
          loop:
            - /dev/vdd
            - /dev/vde
          ignore_errors: yes
      always:
        - name: Create a ext4 filesystem
          community.general.filesystem:
            fstype: ext4
            dev: "{{ item }}"
          loop:
            - /dev/vdd1
            - /dev/vde1
          ignore_errors: yes
        - name: Mount newpart
          ansible.posix.mount:
            path: /newpart
            src: /dev/vdd1
            fstype: ext4
            state: mounted
              when: ansible_devices.vdd is defined
        - name: Mount newpart1
          ansible.posix.mount:
            path: /newpart10
            src: /dev/vde1
            fstype: ext4
            state: mounted
          when: ansible_devices.vde is defined
        - name: report no vde
          ansible.builtin.debug:
            msg: this disk is not exist
          when: ansible_devices.vde is not defined
```

```shell
$ ansible-navigator run -m stdout partition.yml
```

```
[greg@control ansible]$  ansible-navigator run -m stdout partition.yml

PLAY [create and use partition] ****************************************************************

TASK [Gathering Facts] *************************************************************************
ok: [node3]
ok: [node4]

TASK [Create a partition with 1500m] ***********************************************************
changed: [node4] => (item=/dev/vdd)
changed: [node3] => (item=/dev/vdd)
failed: [node4] (item=/dev/vde) => {"ansible_loop_var": "item", "changed": false, "err": "Error: Could not stat device /dev/vde - No such file or directory.\n", "item": "/dev/vde", "msg": "Error while getting device information with parted script: '/sbin/parted -s -m /dev/vde -- unit 'KiB' print'", "out": "", "rc": 1}
failed: [node3] (item=/dev/vde) => {"ansible_loop_var": "item", "changed": false, "err": "Error: The location 1500MiB is outside of the device /dev/vde.\n", "item": "/dev/vde", "msg": "Error while running parted script: /sbin/parted -s -m -a optimal /dev/vde -- unit KiB mklabel msdos mkpart primary 0% 1500MiB", "out": "", "rc": 1}

TASK [report no 1500m] *************************************************************************
ok: [node3] => {
    "msg": "Could not create partition of that size"
}
ok: [node4] => {
    "msg": "Could not create partition of that size"
}

TASK [Create a partition with 800m] ************************************************************
ok: [node4] => (item=/dev/vdd)
ok: [node3] => (item=/dev/vdd)
failed: [node4] (item=/dev/vde) => {"ansible_loop_var": "item", "changed": false, "err": "Error: Could not stat device /dev/vde - No such file or directory.\n", "item": "/dev/vde", "msg": "Error while getting device information with parted script: '/sbin/parted -s -m /dev/vde -- unit 'KiB' print'", "out": "", "rc": 1}
...ignoring
changed: [node3] => (item=/dev/vde)

TASK [Create a ext4 filesystem] ****************************************************************
changed: [node3] => (item=/dev/vdd1)
changed: [node4] => (item=/dev/vdd1)
changed: [node3] => (item=/dev/vde1)
failed: [node4] (item=/dev/vde1) => {"ansible_loop_var": "item", "changed": false, "item": "/dev/vde1", "msg": "Device /dev/vde1 not found."}
...ignoring

TASK [Mount newpart] ***************************************************************************
changed: [node4]
changed: [node3]

TASK [Mount newpart1] **************************************************************************
skipping: [node4]
changed: [node3]

TASK [report no vde] ***************************************************************************
skipping: [node3]
ok: [node4] => {
    "msg": "this disk is not exist"
}

PLAY RECAP *************************************************************************************
node3                      : ok=6    changed=4    unreachable=0    failed=0    skipped=1    rescued=1    ignored=0   
node4                      : ok=6    changed=2    unreachable=0    failed=0    skipped=1    rescued=1    ignored=2   

```



## 10. [生成主机文件]()

> **生成主机文件**
>
> - [x] 将一个初始模板文件从 `http://classroom/materials/hosts.j2` 下载到 `/home/greg/ansible`
> - [x] 完成该模板，以便用它生成以下文件：针对每个清单主机包含一行内容，其格式与 `/etc/hosts` 相同
> - [x] 将一个剧本从`http://classroom/materials/hosts.yml` 下载到`/home/greg/ansible` ，它将使用此模板在 `dev` 主机组中的主机上生成文件 `/etc/myhosts` 。
>
> 该 playbook 运行后， `dev` 主机组中主机上的文件 `/etc/myhosts` 应针对每个受管主机包含一行内容：
>
> ```
> 127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
> ::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
> 
> 172.25.250.9    node1.lab.example.com node1
> 172.25.250.10   node2.lab.example.com node2
> 172.25.250.11   node3.lab.example.com node3
> 172.25.250.12   node4.lab.example.com node4
> 172.25.250.13   node5.lab.example.com node5
> ```
>
> **注：清单主机名称的显示顺序不重要。**

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
  <li><b>endfor</b></li>
  User Guide / Working With Playbooks/ Using Variables<br>
  <li><b>when</b></li>
  Docs » User Guide » Working With Playbooks » Conditionals
  <li>hostvars == setup - "<b>ansible_</b>"</li>
  <li> hosts.yml 文件已存在
</div>


|  ID  | hostvars ansible_facts |               -m setup               |
| :--: | :--------------------: | :----------------------------------: |
|  1   |  default_ipv4.address  | ==~~ansible_~~==default_ipv4.address |
|  2   |        nostname        |       ==~~ansible_~~==nodename       |
|  3   |        hostname        |       ==~~ansible_~~==hostname       |

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ ansible dev -m debug -a 'var=groups'
$ ansible dev -m debug -a "var=groups['all']"

*$ wget http://classroom/materials/hosts.j2
```
```bash
$ vim hosts.j2
```

```jinja2
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6

{% for host in groups.all %}
{{ hostvars[host].ansible_default_ipv4.address }} {{ hostvars[host].ansible_fqdn }} {{ hostvars[host].ansible_hostname }} 
{% endfor %}
```

```bash
$ vim hosts.yml    #考试中该文件已存在，模拟练习环境中没有，我们自己手动编辑一个。
```
```yaml
---
- name: 生成主机文件
  hosts: all 
  tasks:
  - name: Template a file to /etc/myhosts
    ansible.builtin.template:
      src: /home/greg/ansible/hosts.j2
      dest: /etc/myhosts
    when: inventory_hostname in groups.dev
#   另外一种条件的方法
#   when: '"dev" in group_names'
```
```bash
*$ ansible-navigator run hosts.yml -m stdout
```
```bash
$ ansible dev -m shell -a 'cat /etc/myhosts'

#验证：符合题意即可
```



## 11. 修改文件内容
> **修改文件内容**
>
> 按照下方所述，创建一个名为 `/home/greg/ansible/issue.yml` 的 playbook ：
>
> - [ ] 该 playbook 将在`所有清单主机`上运行
> - [ ] 该 playbook 会将 `/etc/issue` 的内容替换为下方所示的一行文本：
>   - [ ] 在 `dev` 主机组中的主机上，这行文本显示 为：`Development
>   
>   - [ ] 在 `test` 主机组中的主机上，这行文本显示 为：`Test`
>   - [ ] 在 `prod` 主机组中的主机上，这行文本显示  ： Production

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
Docs » User Guide » Working With Playbooks » Conditionals<br>
  Docs » Special Variables
</div>
<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ ansible dev -m debug -a 'var=inventory_hostname'
$ ansible dev -m debug -a 'var=groups'
$ ansible dev -m debug -a 'var=groups.dev'

$ ansible-doc copy
$ ansible-doc stat

*$ vim /home/greg/ansible/issue.yml
```
```yaml
---
- name: 修改文件内容
  hosts: all
  tasks:
  - name: Copy using inline content 1
    ansible.builtin.copy:
      content: 'Development'
      dest: /etc/issue
    when: inventory_hostname in groups.dev
    
  - name: Copy using inline content 2
    ansible.builtin.copy:
      content: 'Test'
      dest: /etc/issue
    when: inventory_hostname in groups.test
    
  - name: Copy using inline content 3
    ansible.builtin.copy:
      content: 'Production'
      dest: /etc/issue
    when: inventory_hostname in groups.prod
```
```bash
*$ ansible-navigator run issue.yml -m stdout
```
```bash
验证A
$ ansible dev,test,prod -a 'cat /etc/issue'


验证B
$ ansible-inventory --graph
$ ansible all -a 'cat /etc/issue'

#验证：符合题意即可
```

检查是否设置了上下文 所属组为

## 12. 创建 Web 内容目录
> **创建 Web 内容目录**
>
> 按照下方所述，创建一个名为 `/home/greg/ansible/webcontent.yml` 的 playbook ：
>
> - [ ] 该 playbook 在 `dev` 主机组中的受管节点上运行
>
> - [ ] 创建符合下列要求的目录` /webdev `：
>
>   - [ ] 所有者为 `webdev` 组
>   - [ ] 具有常规权限：`owner=read+write+execute， group=read+write+execute，other=read+execute`
>   - [ ] 具有`特殊权限`：设置组 ID 
>
> - [ ] 用符号链接将 `/var/www/html/webdev` 链接到 `/webdev`
>
> - [ ] 创建文件 `/webdev/index.html` ，其中包含如下所示的单行文件： `Development`
>
> - [ ] 在 `dev` 主机组中主机上浏览此目录（例如 `http://172.25.250.9/webdev/` ）将生成以下输出：
>
>     ```
>     Development
>     ```

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
  <li> 隐藏考点<b>setype</b>，文件的上下文关系
  <li> 需完成第3题 == install php -=> install httpd
  <li> 需完成第4.A题 == setenforce 1
  <li> 需完成第6题 == start httpd
</div>

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ ansible-doc file
$ ansible-doc copy

$ ansible dev -a 'ls -ldZ /var/www/html'
-rw-r--r--. 1 root root system_u:object_r:`httpd_sys_content_t`:s0 11 Apr 14 07:11 /var/www/html
```

```bash
*$ vim /home/greg/ansible/webcontent.yml
```
```yaml
---
- name: 创建 Web 内容目录
  hosts: dev
## 完成第 3 题，才存在 httpd 服务；才存在主目录 /var/www/html
## 完成第 7 题，可以使用『角色』启动服务
  roles:
  - apache
  tasks:
## 未完成第 7 题，可以使用『模块』启动服务
#  - name: Start service httpd, if not started
#    ansible.builtin.service:
#      name: httpd
#      state: started
#      enabled: yes
  - name: Create a directory if it does not exist
    ansible.builtin.file:
      path: /webdev
      state: directory
      group: webdev
      mode: u=rwx,g=rwxs,o=rx
#     mode: '2775'
  - name: Create a symbolic link
    ansible.builtin.file:
      src: /webdev
      dest: /var/www/html/webdev
      state: link
  - name: Copy using inline content
    ansible.builtin.copy:
      content: 'Development'
      dest: /webdev/index.html
      setype: httpd_sys_content_t
```
```bash
*$ ansible-navigator run webcontent.yml -m stdout
```

<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[node1]**

```bash
$ curl http://node1/webdev/
Development

#验证： 可查看到网页内容为 Development 即可。
```



## 13. 生成硬件报告

> **生成硬件报告**
>
> 创建一个名为 `/home/greg/ansible/hwreport.yml` 的 playbook ，它将在所有受管节点上生成含有以下信息的输出文件 `/root/hwreport.txt` ：
>
> - [ ] `显示节点清单中的主机名称`
> - [ ] 以 `MB` 表示的`总内存大小`
> - [ ] `BIOS 版本`
> - [ ] 磁盘设备 `vda 的大小`
> - [ ] 磁盘设备 `vdb 的大小`
> - [ ] 输出文件中的每一行含有一个 key=value 对
>
> 您的 playbook 应当：
>
> - [ ] 从 `http://classroom/materials/hwreport.empty` 下载文件，并将它保存为 `/root/hwreport.txt`
> - [ ] 使用`正确的值`改为 /root/hwreport.txt
> - [x] 如果硬件项不存在，相关的值应设为 NONE

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
<li><b>inventory_hostname</b></li>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;REFERENCE & APPENDICES / Special Variables / Magic<br>
<li><b>default</b></li>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Docs » User Guide » Working With Playbooks » Templating (Jinja2) » Filters
</div>


<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
方法一、
$ ansible all -m setup > setup.txt
$ vim setup.txt
/mem<Enter>
/bios<Enter>
/vda<Enter>
/vdb<Enter>
方法二、
$ ansible all -m setup | grep mem
$ ansible all -m setup | grep bios
$ ansible all -m setup -a 'filter=*device*'

$ ansible-doc -l | grep -i download
$ ansible-doc get_url
$ ansible-doc lineinfile

$ curl http://materials/hwreport.empty

$ vim /home/greg/ansible/hwreport.yml
```
- 方法一
```yaml
---
- name: 生成硬件报告
  hosts: all 
  tasks:
  - name: Download foo.conf
    ansible.builtin.get_url:
      url: http://materials/hwreport.empty
      dest: /root/hwreport.txt
  - name: Ensure 1
    ansible.builtin.lineinfile:
      path: /root/hwreport.txt
      regexp: '^HOST='
      line: HOST={{ inventory_hostname }}
  - name: Ensure 2
    ansible.builtin.lineinfile:
      path: /root/hwreport.txt
      regexp: '^MEMORY='
      line: MEMORY={{ ansible_memtotal_mb }}
  - name: Ensure 3
    ansible.builtin.lineinfile:
      path: /root/hwreport.txt
      regexp: '^BIOS='
      line: BIOS={{ ansible_bios_version }}
  - name: Ensure 4
    ansible.builtin.lineinfile:
      path: /root/hwreport.txt
      regexp: '^DISK_SIZE_VDA='
      line: DISK_SIZE_VDA={{ ansible_devices.vda.size }}
  - name: Ensure 5
    ansible.builtin.lineinfile:
      path: /root/hwreport.txt
      regexp: '^DISK_SIZE_VDB='
      line: DISK_SIZE_VDB={{ ansible_devices.vdb.size | default('NONE', true) }}
```
- 方法二
```yaml
---
- name: 生成硬件报告
  hosts: all 
  vars:
    hw_all:
    - hw_name: HOST
      hw_cont: "{{ inventory_hostname }}"
    - hw_name: MEMORY
      hw_cont: "{{ ansible_memtotal_mb }}"
    - hw_name: BIOS
      hw_cont: "{{ ansible_bios_version }}"
    - hw_name: DISK_SIZE_VDA
      hw_cont: "{{ ansible_devices.vda.size }}"
    - hw_name: DISK_SIZE_VDB
      hw_cont: "{{ ansible_devices.vdb.size | default('NONE', true) }}"
  tasks:
  - name: 1
    get_url:
      url: http://materials/hwreport.empty
      dest: /root/hwreport.txt 
  - name: 2
    lineinfile:
      path: /root/hwreport.txt
      regexp: '^{{ item.hw_name }}='
      line: "{{ item.hw_name }}={{ item.hw_cont }}"
    loop: "{{ hw_all }}"
```
```bash
*$ ansible-navigator run hwreport.yml -m stdout
```
```bash
$ ansible all -a 'cat /root/hwreport.txt'

# 验证： 收集到的报告应和各个主机的事实对比结果一致即可，请自行核对。

访问 失败
```



## 14. 创建密码库
> **创建密码库**
>
> 按照下方所述，创建一个 Ansible 库来存储用户密码：
>
> - [ ] 库名称为 `/home/greg/ansible/locker.yml`
> - [ ] 库中含有两个变量，名称如下：
>   - [ ] `pw_developer`，值为 `Imadev`
>   - [ ] `pw_manager`，值为 `Imamgr`
> - [ ] 用于加密和解密该库的密码为 `whenyouwishuponastar`
>   - [ ] 密码存储在文件 `/home/greg/ansible/secret.txt` 中

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
  <li> 做完16题，改密码，再做这个题
</div>
<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
*$ echo whenyouwishuponastar > /home/greg/ansible/secret.txt
```

```bash
*$ vim ansible.cfg
```

```ini
...内容省略...
vault_password_file = /home/greg/ansible/secret.txt
```

```bash
$ ansible-vault create /home/greg/ansible/locker.yml
```
```yaml
---
pw_developer: Imadev
pw_manager: Imamgr
```
```bash
$ ansible-vault view /home/greg/ansible/locker.yml
---
pw_developer: Imadev
pw_manager: Imamgr

$ cat /home/greg/ansible/locker.yml
$ANSIBLE_VAULT;1.1;AES256
38636666623761326536363836663066646336656361663664653235333961306634313939326631
3038366162383733643633383935663431376163646639350a393436663566366631303064653933
63373030636333343362623130653633363630336461646262363239643335353466653534643135
6162383733353561640a383333623137646565666636363064343361346537396633653234306263
33326138366135383430643763653931663738656633393933343466616235333631383431373135
6661613037633237613837313431383731666537306139323435

# 验证：根据推荐指令验证到文件内容被加密，且可以通过配置文件secret.txt文件自动解密即可。
```



## 15. 创建用户帐户
> **创建用户帐户**
>
> - [ ] 从 `http://classroom/materials/user_list.yml` 下载要创建的用户的列表，并将它保存到 `/home/greg/ansible`
> - [ ] 在本次练习中使用在其他位置创建的密码库 `/home/greg/ansible/locker.yml` 。创建名为 `/home/greg/ansible/users.yml` 的 playbook ，从而按以下所述创建用户帐户：
>   - [x] 职位描述为 `developer` 的用户应当：
>     - [x] 在 `dev` 和 `test` 主机组中的受管节点上创建
>     - [x] 从 `pw_developer` 变量分配密码
>     - [x] 指定用户UID
>     - [x] 密码最大有效期`30`天
>     - [x] 是补充组 `devops` 的成员
>   - [x] 职位描述为 `manager` 的用户应当：
>     - [ ] 在 `prod` 主机组中的受管节点上创建
>     - [ ] 从 `pw_manager` 变量分配密码
>     - [ ] 密码最大有效期`30`天
>     - [ ] 是补充组 `opsmgr` 的成员
> - [ ] 密码采用 `sha512` 哈希格式。
> - [ ] 您的 playbook 应能够在本次练习中使用在其他位置创建的库密码文件 `/home/greg/ansible/secret.txt` 正常运行。

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
- <b>vars_files</b><br>
  Docs » User Guide » Working With Playbooks » Using Variables<br>
- <b>loop</b><br>
  Docs » User Guide » Working With Playbooks » Loops<br>
- <b>when</b><br>
  Docs » User Guide » Working With Playbooks » Conditionals<br>
- <b>password</b><br>
  Docs » User Guide » Working With Playbooks » Templating (Jinja2) » Filters
</div>



<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
*$ wget http://classroom/materials/user_list.yml

$ cat user_list.yml

*$ vim /home/greg/ansible/users.yml
```
```yaml
---
- name: 创建用户帐户 1
  hosts: dev,test
  vars_files:
  - /home/greg/ansible/locker.yml
  - /home/greg/ansible/user_list.yml
  tasks:
  - name: Ensure group 1
    ansible.builtin.group:
      name: devops
      state: present
  - name: Add the user 1
    ansible.builtin.user:
      name: "{{ item.name }}"
      groups: devops
      password: "{{ pw_developer | password_hash('sha512') }}"
      password_expire_max: "{{ item.password_expire_max }}"
    loop: "{{ users }}"
    when: item.job == 'developer'

- name: 创建用户帐户 2
  hosts: prod
  vars_files:
  - /home/greg/ansible/locker.yml
  - /home/greg/ansible/user_list.yml
  tasks:
  - name: Ensure group 2
    ansible.builtin.group:
      name: opsmgr
      state: present
  - name: Add the user 2
    ansible.builtin.user:
      name: "{{ item.name }}"
      groups: opsmgr
      password: "{{ pw_manager | password_hash('sha512') }}"
      password_expire_max: "{{ item.password_expire_max }}"
    loop: "{{ users }}"
    when: item.job == 'manager'

```
```bash
*$ ansible-navigator run users.yml -m stdout
```

```bash
$ ansible dev,test -m shell -a 'id bob; id sally; id fred'
$ ssh bob@172.25.250.9
bob@172.25.250.9\'s password: `Imadev`
<Ctrl-D>

$ ansible prod -m shell -a 'id fred; id bob;  id sally'
$ ssh sally@172.25.250.12
sally@172.25.250.12\'s password: `Imamgr`
<Ctrl-D>

# 验证： 确保 dev，test          上有bob、fred  且与题目要求一致
#       确保 pord(node3 node4)  上有 sally    且与题目要求一致

#验证密码：
$ sudo yum install -y sshpass-1.09-4.el9ap.x86_64
$ sshpass -p Imade ssh bob@node1 hostname
$ sshpass -p Imamgr ssh sally@node4 hostname
```



## 16. 更新 Ansible 库的密钥

> **更新 Ansible 库的密钥**
>
> 按照下方所述，更新现有 Ansible 库的密钥：
>
> - [ ] 从 `http://classroom/materials/salaries.yml` 下载 Ansible 库到 `/home/greg/ansible`
> - [ ] 当前的库密码为 `insecure8sure`
> - [ ] 新的库密码为 `bbs2you9527`
> - [ ] 库使用`新密码`保持加密状态

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
Docs » User Guide » Ansible Vault
</div>
<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ wget http://classroom/materials/salaries.yml

先做这道题，后做13题(配置文件中未定义密码文件，这么做可以)
$ ansible-vault rekey salaries.yml
Vault password: `insecure8sure`
New Vault password: `bbs2you9527`
Confirm New Vault password: `bbs2you9527`
Rekey successful
```

```bash
$ ansible-vault view salaries.yml
Vault password: `bbs2you9527`
haha

$ ansible-vault view --ask-vault-pass salaries.yml
Vault password: `bbs2you9527`
haha

# 验证： 用新密码验证是否可以解密查看内容。
```

<div style="background: #f0f0f0; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #ee0000; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Important - 重要</dt>
<li>vault 实验，先做 15 题，再做倒数 13`创建密码库`
<li>如果已经修改了配置文件，验证时需使用<b>--ask-vault-pass</b>
</div>



## 17. 配置 cron 作业

> **配置 cron 作业**
>
> 创建一个名为 `/home/greg/ansible/cron.yml` 的 playbook :
>
> - [ ] 该 playbook 在 `test` 主机组中的受管节点上运行
>
> - [ ] 配置 `cron` 作业，该作业`每隔 2 分钟`运行并执行以下命令：
>
>   `logger "EX200 in progress"`，以用户 `natasha` 身份运行
>

<div style="background: #dbfaf4; padding: 12px; line-height: 24px; margin-bottom: 24px;">
<dt style="background: #1abc9c; padding: 6px 12px; font-weight: bold; display: block; color: #fff; margin: -12px; margin-bottom: -12px; margin-bottom: 12px;" >Hint - 提示</dt>
natasha 默认存在
</div>


<img width=35 src="https://i0.hdslb.com/bfs/album/41d94fd91de77380abe6f8639e7735e0b7947121.png">**[172.25.250.254|control]**

```bash
$ ansible-doc -l | grep cron
$ ansible-doc cron

*$ vim /home/greg/ansible/cron.yml
```

```yaml
---
- name: cron
  hosts: test
  tasks:
  - name: Ensure a job 
    cron:
      name: "check dirs"
      minute: "*/2"
      job: 'logger "EX200 in progress"'
      user: natasha
```

```bash
*$ ansible-navigator run cron.yml -m stdout
```

```bash
$ ansible test -a 'crontab -l -u natasha'
node2 | CHANGED | rc=0 >>
#Ansible: check dirs
*/2 * * * * logger "EX200 in progress"

$ sleep 4m; \
ansible test -a 'grep EX200 /var/log/messages'
node2 | CHANGED | rc=0 >>
...
Aug 27 20:26:02 node2 `natasha`[3626]: `EX200 in progress`
Aug 27 20:28:01 node2 `natasha`[3867]: `EX200 in progress`

# 验证：通过crontab -l -u natasha命令验证任务是否存在并正确。
```

```bash
#1-17题全部做完。
重点关注：
1 第一题：
	1 一定登录镜像服务器podman login
	2 清单配置文件内容
	3 ansible-navigator collections 验证集合
2 第五题： collection
	1 影响其他使用集合角色、防火墙、逻辑卷
	
3 角色所有题+第三题基本都有连带关系，最后通过第8题，使用角色，实现所有目标。

4 用户题，密码最大过期时间，看题目要求，直接写30，还是写变量。
```

