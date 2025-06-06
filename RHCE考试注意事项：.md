#### 一 环境部署

考试RHCSA9.3   CE9.0



#### 二 RHCSA练习

#### 三 RHCE练习

#### 四 重点注意事项



考试重点关注：

RHCSA：

#### node1

1.远程登录node1、2都要先修改ssh配置文件 #PermitRootLogin prohibit-password #PasswordAuthentication yes，登录需要输入密码

2.IP地址不建议用vim，因为默认配置文件里无信息，建议用nmcli或nmtui，nmtui使用时改为手动设置。

3.autofs的题，只要能用普通用户远程ssh remoteuserX@localhost，并能查看到挂载状态就行了，不能用cd



#### node2

1.重启进入内核选择阶段，不要记顺序，选择带rescue单词的那一行按e，后面操作正常

2.touch /.autorelabel这个单词记住了，否则破密码失败，把它记在心里的某个位置