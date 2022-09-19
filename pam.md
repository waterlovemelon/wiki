# PAM(Pluggable Authentication Modules)可插拔式认证模块

## 简介

Linux-PAM是一套共享库，程序根据自己的需要去加载相应的共享库，去进行鉴权、修改密码、账户认证等操作。

## 相关路径

配置文件在/etc/pam.d/文件夹下面
共享库在/usr/lib/x86_64-linux-gnu/security 、/lib/security 下面

## 配置文件

PAM使用配置文件来管理对程序的认证⽅式，应⽤程序调⽤相应的配置⽂件，再根据配置文件的内容去加载对应的动态库

```shell
auth        requisite           deepin_security_verify.so
auth        sufficient          pam_rootok.so
session     required            pam_env.so readenv=1
session     required            pam_env.so readenv=1 envfile=/etc/default/locale
session     optional            pam_mail.so nopen
session     required            pam_limits.so

# The standard Unix authentication modules, used with
# NIS (man nsswitch) as well as normal /etc/passwd and
# /etc/shadow entries.
@include common-auth
@include common-account
@include common-session

```