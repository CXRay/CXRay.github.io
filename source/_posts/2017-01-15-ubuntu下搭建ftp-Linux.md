---
layout: post
date: 2017-01-15
title: ubuntu下搭建ftp
categories: Linux
---


为了抛弃百度云，限速太恶心了，自己搭建ftp

1.下载vsftpd
---

sudo apt-get install vsftpd

2.修改vsftpd.conf
---

sudo vi /etc/vsftpd.conf 

将vsftpd.conf修改为以下内容

listen=YES

anonymous_enable=NO

local_enable=YES

write_enable=YES

dirmessage_enable=YES

use_localtime=YES

xferlog_enable=YES

connect_from_port_20=YES

secure_chroot_dir=/var/run/vsftpd/empty

pam_service_name=vsftpd

rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem

rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key


3.为了让root账号直接登录ftp，需要删除/etc/ftpusers中的root，这个文件是禁止该Linux上的用户登录
---

sudo vi /etc/ftpusers 

4.重启vsftpd
---

sudo service vsftpd restart

然后就能通过ubuntu账户登录了

