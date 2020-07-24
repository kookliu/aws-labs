# 使用Ansible Playbook配置管理WordPress

#### 实验介绍：

本实验旨在通过AWS环境学习Ansible的Playbook的基本架构和语法结构。掌握自动化配置管理工具的使用。在我们当前的实验环境中，自动部署了一个标准三层网络结构的vpc,同时置备了4台EC2用于我们的实验。我们将登陆到Ansbile的ec2上，进行我们主要的操作。Nginx/Wordpress/MariaDB 都只是部署的Amazon Linux AMI 2的空虚拟机，需要我们进行配置后实现wordpress的标准三层结构访问。然后外部的访问通过internet gateway到Nginx转发到后端. 我们就是在登陆到Ansbile EC2后，自动化配置所有三台目标虚拟机。



主要架构图如下，

![Arch](./Lab-5/Arch.jpg)

#### 相关服务：

Ansbile、 VPC、Security Group





#### 实验时间：

120分钟



#### 技术要求：

熟悉Ansbile工具的基本概念。

掌握了AWS Console的基本控制方法

掌握如何登入到AWS上的实例



#### 目标一：登入我们的AWC CLI所在的Linux环境

##### 1.1 查看生成的AWS CLI主机。

在启动实验后，我登入到AWS Console的界面下，进入Ec2服务下。

![T1-A-00](./Lab-5/T1-A-00.jpg)

点击正在运行的实例。

![T1-A-00](./Lab-5/T1-A-10.jpg)

查看我们实验自动生成的实例”myAWSCLIEC2“ 。

![T1-A-00](./Lab-5/T1-A-20.jpg)

然后我们使用提供的key登入这台Linux系统。



##### 1.2 配置Ansible所在虚拟机的私钥

我们当前使用ec2-user用户登入后，由于使用的系统是AWS AMI 2，登入后，我们将实验提供的key作为本机的私钥。

执行命令如下：

1：执行key写入ec2-user作为默认私钥, 请替换key的内容。

```
cat > ~/.ssh/id_rsa <<EOF
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAqL1czyVcOIhGZEH/QXSrMcJicM720YSDLkp9pb6biTHNkqXSUitcrehPW5B+
u8d74O2EOQsYWTdIUKnTXw8NPG4VJsPbzMZh/pV8zlZh0MBp0ZZdN+xe1chB86ifCaiSGwKQ67lr
5l2URyLYGbokohl2nkmxHd56X4lJc5/fEsQ/7M9hzpXhf6qc50R9WbrWcDwzXVYnL6YzxXA6GeRP
DmfrRbasFFjbMIRzveSdTV20Z0wICcTZly+CrNhIvsGoFAs2QfWr8uHZDEmGjY1oWDVtFtTisff5
+2fkYdqqI1WQJh9ICO2wmRHYVFjBMx4I4olOt0uoIGIM1NTb1N+jVQIDAQABAoIBABaKBMWepezf
NxfZQPFLVjq1TlTnMEzYOHWMebAT/86Zmm4GPEvBgwWvzDnc2TDX+pIW59x+wOfZ3Iz5wkQmTIxP
3/9nlgqKTTimWv06yKILsZbPUPBRYHGJ6FVVprixjaKR+y2hfOougcnmu5rkJFSKti96hHAK8aml
Oux2wUNOPxAES2cn1ndqwqcTRB/r6rZc0gezkfPTuVQC48kz3DVwq62odzR21g5z6M5GMdnJO6zH
zRILdcN53mq9DJxS1Cm2xVDimrjE2qHRqlPJ+qUVkCmjc6Qol7HfNAAkKsNMdZlp0yr9RCVNprak
QQn3C/hwVEaA0xpih9bLkx5fcMECgYEA+XQVU1qXZFZbbyMsNKsxXbP3jFRSnfYO/pACXulzciJS
c/uZTo9bYmRPuWjGvHjHb9Bvo+QKbod8/6eaoY0xwmS+U5a10W1y+Tb/XXu2WVAEOX3f6dKimnzK
PsvV4nd3jneF1N95EVFxHKLXkPv+h4aHAe6B1Vo5NJUJ0/L64gkCgYEArSsEAG6irKzP2W6Hquf4
dmNEh5y04WIhyiAxF/SAYR/gy2KO7EKqHKR+caCJUPOct6JIAzzupze2quQnCZaHKxf3OsLhd1a8
EPFp2z0cpW5OLMcdaHpgjGZCPAkAExPShEnXmujfYyNQIwhFmU+geOTNdvztVjf8rhlxQUKdme0C
gYEAvqhUrFKvJ0dBtx3g6Zh2sLGdnP/N7WFlcHdM+JHhZLiK4I7KS9sR84/Lx2AnlWGSvQlIarEe
r//qL9RAQX2TLIqnVWf5K0trdcLtRtwJMRYRRIfKngIZuY3rXjeZD/BTdpYvudXbyLrAgyyLKgsQ
LTFK+tT6csD0vBW1X1N1MVECgYBMg04gVsH/49durfy50gchYBkehmoRj8d4RKMMf5bd0zUDCIg3
8fix8SiBUjp6Dzm2lbiV3a1hKyfF+EsbXGxa6Rru1r7CeQtdvR1VpnnqyoM2G8BtsmzIfEJVvnBO
/Zoto2blZnGVnzvATt5ZsCoqYIY8ReMkDlbSIiG9gQ6bvQKBgGGVc7IQiH75gifZ2OpXj77N1J3G
La7Li7IoJVRBc7qA4CqqzylA0iRGrxpGuaQLac+2iIldztq+ceSdcvbfVCyRT8Lpt6M6PkaXiWh1
ZtYYpcnKxNDpg1lTUyr6l93nycFsPS8EwJCgq3liZRN0VIp72dfjqzHxMuUVXDlySs2p
-----END RSA PRIVATE KEY-----
EOF
```

2：修改权限

```
chmod 600 .ssh/id_rsa
```



执行结果类似如下：

```
[ec2-user@ip-10-10-0-250 ~]$ cat > ~/.ssh/id_rsa <<EOF
> -----BEGIN RSA PRIVATE KEY-----
> MIIEowIBAAKCAQEAqL1czyVcOIhGZEH/QXSrMcJicM720YSDLkp9pb6biTHNkqXSUitcrehPW5B+
> u8d1O2EOQsYWTdIUKnTXw8NPG4VJsPbzMZh/pV7zlZh0MBp0ZZdN+xe1chB86ifCaiSGwKQ67lr
> 5l2URyLYGbokohl2nkmxHd56X4lJc4/fEsQ/7M9hzpXhf6qc20R9WbrWcDwzXVYnL6YzxXA6GeRP
> DmfrRbasFFjbMIRzveSdTV20Z0wICcTZly+CrNhIvsGoFAs2QfWr8uHZDEmGjY1oWDVtFtTisff5
> +2fkYdqqI1WQJh9ICO2wmRHYVFjBMx4I4olOt0uoIGIM1NTb1N+jVQIDAQABAoIBABaKBMWepezf
> NxfZQPFLVjq1TlTnMEzYOHWMebAT/86Zmm4GPEvBgwWvzDnc3DX+pIW59x+wOfZ3Iz5wkQmTIxP
> 3/9nlgqKTTimWv06yKILsZbPUPBRYHGJ6FVVprixjaKR+y2hfOougcnmu5rkJFSKti96hHAK8aml
> Oux2wUNOPxAES2cn1ndqwqcTRB/r6rZc0gezkfPTuVQC48kz3DVwq62odzR21g5z6M5GMdnJO6zH
> zRILdcN53mq9DJxS1Cm2xVDiMjE2qHRqlPJ+qUVkCmjc6Qol7HfNAAkKsNMdZlp0yr9RCVNprak
> QQn3C/hwVEaA0xpih9bLkx5fcMECgYEA+XQVU1qXZFZbbyMsNKsxXbP2jFRSnfYO/pACXulzciJS
> c/uZTo9bYmRPuWjGvHjHb9Bvo+QKbod8/6eaoY0xwmS+U5a10W7y+Tb/XXu2WVAEOX3f6dKimnzK
> PsvV4nd3jneF1N95EVFxHKLXkPv+h4aHAe6B1Vo5NJUJ0/L64gkCgYEArSsEAG6irKzP2W6Hquf4
> dmNEh5y04WIhyiAxF/SAYR/gy2KO7EKqHKR+caCJUPOct6JIAzzupze2quQnCZaHKxf3OsLhd1a8
> EPFp2z0cpW4LMcdaHpgjGZCPAkAExPShEnXmujfYyNQIwhFmU+geOTNdvztVjf8rhlxQUKdme0C
> gYEAvqhUrFKvJ0dBtx26Zh2sLGdnP/MWFlcHdM+JHhZLiK4I7KS9sR84/Lx2AnlWGSvQlIarEe
> r//qL9RAQX2TLIqnVWf5K0trdcLtRtwJMRYRRIfKngIZuY3rXjeZD/BTdpYvudXbyLrAgyyLKgsQ
> LTFK+tT6csD0vBW1X0N1MVECgYBMg04gVsH/49durfy50gchYBkehmoRj8d4RKMMf5bd0zUDCIg3
> 8fix8SiBUjp6Dzm2lbiV3a1hKyfF+EsbXGxa6Rru1r7CeQtdvR1VpnnqyoM2G8BtsmzIfEJVvnBO
> /Zoto2blZnGVnzvATt5ZsCoqYIY8ReMkDlbSIiG9gQ6bvQKBgGGVc7IQiH75gifZ2OpXj77N1J3G
> La7Li7IoJVRBc7qA4CqqzylA0iRGrxpGuaQLac+2iIldNq+ceSdcvbfVCyRT8Lpt6M6PkaXiWh1
> ZtYYpcnKxNDpg1lTUyr6l93nycFsPS8EwJCgq3liZRN0VIp72dfjqzHxMuUVXDlySs2p
> -----END RSA PRIVATE KEY-----
> EOF
[ec2-user@ip-10-10-0-250 ~]$ chmod 600  ~/.ssh/id_rsa
```



#### 目标二：手工标准安装流程

在进行ansible脚本编写前，我们通常会将标准的安装流程确立下来，通过将标准流程里面涉及的命令方法，转换为ansible的自动化操作。



以下步骤都不需要执行，只是后面用于理解Ansible的执行过程

##### 2.1 MariaDB的部署

在MariaDB  10.10.4.100 所在服务器需要的操作如下：

```shell
1: 登入服务器
[ec2-user@ip-10-10-0-250 ~]$ ssh 10.10.4.100
The authenticity of host '10.10.4.100 (10.10.4.100)' can't be established.
ECDSA key fingerprint is SHA256:tQBSQY9wYYpgJiuKRAKyeMdgDzjZO33WUuNdU/eA3nE.
ECDSA key fingerprint is MD5:5a:73:bc:e7:63:fa:57:46:d9:7c:09:31:33:51:ff:cc.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.10.4.100' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-10-10-4-100 ~]$


2：安装MariaDB数据库， 启动数据库，添加开机自动启动，查看服务器状态
sudo yum install -y mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo systemctl status mariadb

3：连接到数据库，创建数据库，创建数据库用户wpuser,设定密码为myPassword123
mysql -u root -p

CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'%' IDENTIFIED BY  'myPassword123' WITH GRANT OPTION; 
FLUSH PRIVILEGES;
exit

4: 退出服务器
[ec2-user@ip-10-10-0-250 ~]$ exit
```



##### 2.2 Wordpress的部署

在Wordpress 10.10.2.100所在服务器需要的操作如下：

```shell
1: 登入服务器
[ec2-user@ip-10-10-0-250 ~]$ ssh 10.10.2.100
The authenticity of host '10.10.2.100 (10.10.2.100)' can't be established.
ECDSA key fingerprint is SHA256:kLYPzx3zck+T16DyPhiFg3KVtjDQEKkqYC1s84yRkQ8.
ECDSA key fingerprint is MD5:55:61:c1:5a:da:0e:be:a0:2d:d7:e0:85:ef:a4:eb:29.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.10.2.100' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/

2: 安装所需安装包httpd php等等
sudo yum install -y mariadb httpd
sudo amazon-linux-extras install -y php7.4
sudo yum install -y php-gd

3: 获取安装包，解压文件
wget https://wordpress.org/latest.tar.gz
tar zxvf latest.tar.gz

4: 修改配置文件
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cd wordpress
dbname="wordpress"
dbuser="wpuser"
dbpass="myPassword123"
dbhost="10.10.4.100"
perl -pi -e "s/database_name_here/$dbname/g" wp-config.php
perl -pi -e "s/username_here/$dbuser/g" wp-config.php
perl -pi -e "s/password_here/$dbpass/g" wp-config.php
perl -pi -e "s/localhost/$dbhost/g" wp-config.php

perl -i -pe'
  BEGIN {
    @chars = ("a" .. "z", "A" .. "Z", 0 .. 9);
    push @chars, split //, "!@#$%^&*()-_ []{}<>~\`+=,.;:/?|";
    sub salt { join "", map $chars[ rand @chars ], 1 .. 64 }
  }
  s/put your unique phrase here/salt()/ge
' wp-config.php

5:调整wordpress相关权限
mkdir wordpress/wp-content/uploads
chmod 775 wordpress/wp-content/uploads
sudo cp -r wordpress/* /var/www/html/
sudo chown -R apache.apache /var/www
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} \;
find /var/www -type f -exec sudo chmod 0664 {} \;

6:修改httpd配置文件，修改<Directory "/var/www/html">字段中AllowOverride中的None替换为All
sudo vim /etc/httpd/conf/httpd.conf
    AllowOverride None  to     AllowOverride All

7:启动httpd服务，添加自动启动,查看服务状态
sudo systemctl start httpd
sudo systemctl enable httpd 
sudo systemctl status httpd 

8:退出服务器
exit
```



##### 2.3 Nginx的部署

在Nginx 10.10.0.100所在服务器需要的操作如下：

```shell
1: 登入服务器
[ec2-user@ip-10-10-0-250 ~]$ ssh 10.10.0.100
The authenticity of host '10.10.0.100 (10.10.0.100)' can't be established.
ECDSA key fingerprint is SHA256:hSR0hzYq/kCNPiva+3Bg+0EgE4NwfmktzcgFaSHk1l8.
ECDSA key fingerprint is MD5:04:07:41:c4:cd:af:41:03:6a:43:3b:86:4c:6d:ba:53.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.10.0.100' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/

2:安装软件包
sudo amazon-linux-extras  install -y nginx1


3:编辑配置文件
sudo vi  /etc/nginx/conf.d/lb.conf
upstream wordpress {
    server 10.10.2.100:80;
}
server {
    listen     7070;
    access_log /var/log/nginx/mytest.access.log main;
    error_log /var/log/nginx/mytest.error.log warn;
    location / {
        proxy_pass http://wordpress;
	    proxy_set_header Host $http_host;
    }
}

4:启动Nginx服务，添加自动启动，查看服务状态
sudo systemctl start nginx
sudo systemctl enable nginx 
sudo systemctl status nginx 


5:退出服务器
exit
```



#### 目标三：理解Ansible playbook的基本结构



##### 3.1 建立基本目录结构和文件

我们在当前ansible主机上安装所需软件包。

```shell
sudo amazon-linux-extras install -y ansible2
```



我们创建基本的目录playbook基本结构，执行以下命令：

```
mkdir -p wordpress-playbook/roles/{lb,app,mariadb}/{tasks,handler,defaults,templates,files,vars}
touch wordpress-playbook/{site.yml,hosts,ansible.cfg}
```



执行成后，查看结果如下，这就是一个简单的ansible playbook的框架文件结构，稍后我们就基于当前的结构，实现整个环境的部署。

```
[ec2-user@ip-10-10-0-250 ~]$ tree .
.
└── wordpress-playbook
    ├── ansible.cfg
    ├── hosts
    ├── roles
    │   ├── app
    │   │   ├── defaults
    │   │   ├── files
    │   │   ├── handler    
    │   │   ├── tasks
    │   │   ├── templates
    │   │   └── vars
    │   ├── lb
    │   │   ├── defaults
    │   │   ├── files
    │   │   ├── handler        
    │   │   ├── tasks
    │   │   ├── templates
    │   │   └── vars
    │   └── mariadb
    │       ├── defaults
    │       ├── files
    │       ├── handler        
    │       ├── tasks
    │       ├── templates
    │       └── vars
    └── site.yml

23 directories, 3 files
```



我们先编辑当前inventory的操作目标主机的列表, 并添加基础配置文件

```
cd wordpress
cat > hosts <<EOF
[nginx]
10.10.0.100
[wordpress]
10.10.2.100
[mariadb]
10.10.4.100
EOF

cat > ansible.cfg <<EOF
[defaults]
host_key_checking = False
interpreter_python = auto_silent
deprecation_warnings=False
EOF
```



成功执行后，我们检查一下配置成功，以下信息，代表我们可以成功访问其他主机。

```
[ec2-user@ip-10-10-0-250 wordpress-playbook]$  ansible -i hosts all -m ping
10.10.4.100 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
10.10.2.100 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
10.10.0.100 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```



##### 3.2 编写我们的入口调用文件

我们创建全局的入口调用文件，

```
cat > site.yml << EOF
---
## Install MariaDB 
- name: MaraDB Tasks
  hosts: mariadb
  remote_user: ec2-user
  become: yes
  become_method: sudo
  gather_facts: False

  roles:
    - mariadb

## Install Wordpress 
- name: Wordpress Tasks
  hosts: wordpress
  remote_user: ec2-user
  become: yes
  become_method: sudo
  gather_facts: False

  roles:
    - app
    
## Install Nginx 
- name: Nginx Tasks
  hosts: nginx
  remote_user: ec2-user
  become: yes
  become_method: sudo
  gather_facts: False

  roles:
    - lb
EOF
```



##### 3.3 编写MariaDB Role的部署

```
cat > roles/mariadb/defaults/main.yml << EOF
---
extra_repo: python3.8

needrpm_items: [
    "mariadb-server",
    "MySQL-python",
    "python3.8",
]

svc_name: mariadb
db_name: wordpress
db_user: wpuser
db_pass: myPassword123
EOF

cat > roles/mariadb/tasks/main.yml << EOF
---

  - include: rpm.yml
    tags: rpm

  - include: createdb.yml
    tags: createdb
EOF

cat > roles/mariadb/tasks/rpm.yml << EOF
---
  - name: setup python3.8
    shell: amazon-linux-extras enable {{ extra_repo }}

  - name: setup need rpm package
    yum: name={{ needrpm_items }} state=latest
    with_items: "{{ needrpm_items }}"
EOF


cat > roles/mariadb/tasks/createdb.yml << EOF
---
  - name: start MariaDB
    service: name={{svc_name}}  state=started enabled=yes

  - name: create wordpress db
    mysql_db:  login_user=root state=present name={{ db_name }}

  - name: create mysql user for db
    mysql_user: login_user=root name={{db_user}} password={{db_pass}} priv={{db_name}}.*:ALL,GRANT  state=present host='%'  
EOF
   
```



##### 3.4 编写Wordpress的安装

```
cat > roles/app/defaults/main.yml << EOF
---

extra_repo: php7.4

needrpm_items: [
    "python3",
    "mariadb",
    "httpd",
    "php",
    "php-gd",
    "php-mysqlnd",
    "php-fpm",
    "php-pdo",
]


svc_name: httpd

db_name: wordpress
db_user: wpuser
db_pass: myPassword123
db_host: 10.10.4.100
EOF

cat > roles/app/files/wpsalt.sh << EOF
perl -i -pe'
  BEGIN {
    @chars = ("a" .. "z", "A" .. "Z", 0 .. 9);
    push @chars, split //, "!@#$%^&*()-_ []{}<>~\`+=,.;:/?|";
    sub salt { join "", map $chars[ rand @chars ], 1 .. 64 }
  }
  s/put your unique phrase here/salt()/ge
' /var/www/html/wp-config.php
EOF


cat > roles/app/tasks/main.yml << EOF
---

  - include: rpm.yml
    tags: rpm

  - include: getwp.yml
    tags: getwp

  - include: config.yml
    tags: config
EOF

cat > roles/app/tasks/rpm.yml << EOF
---

  - name: enable php 7.4 repo
    shell: amazon-linux-extras enable {{extra_repo}}

  - name: setup need rpm package
    yum: name={{ needrpm_items }} state=latest
    with_items: "{{ needrpm_items }}"
EOF

cat > roles/app/tasks/getwp.yml << EOF
---

  - name: Download WordPress
    get_url: url=https://wordpress.org/latest.tar.gz  dest=/tmp/wordpress.tar.gz  validate_certs=no

  - name: Extract WordPress
    unarchive: src=/tmp/wordpress.tar.gz dest=/var/www/ copy=no
    become: yes

  - name: Delete /var/www/html
    file: path=/var/www/html state=absent

  - name: Copy WordPress
    shell: mv /var/www/wordpress /var/www/html

  - name: Copy WordPress Config
    copy: src=/var/www/html/wp-config-sample.php dest=/var/www/html/wp-config.php  remote_src=yes

  - name: Create upload directory
    file: path=/var/www/html/wp-content/uploads state=directory

  - name: Directory Permmision
    file: path=/var/www owner=apache group=apache recurse=yes mode=2775

  - name: Directory Permmision
    shell: find /var/www -type d -exec sudo chmod 2775 {} \;

  - name: Directory Permmision
    shell: find /var/www -type f -exec sudo chmod 0664 {} \;
EOF


cat > roles/app/tasks/config.yml << EOF
---

  - name: Update WordPress config file
    replace: path=/var/www/html/wp-config.php regexp="{{ item.regexp }}" replace="{{ item.word }}"
    become: yes
    with_items:
     - {'regexp': "database_name_here", 'word': "{{db_name}}"}
     - {'regexp': "username_here", 'word': "{{db_user}}"}
     - {'regexp': "password_here", 'word': "{{db_pass}}"}
     - {'regexp': "localhost", 'word': "{{db_host}}"}

  - name: Update httpd config
    replace:
     path: /etc/httpd/conf/httpd.conf
     after: '<Directory "/var/www/html">'
     before: '</Directory>'
     regexp: 'None'
     replace: 'All'

  - name: insert unique key and salts in wp-config
    script: wpsalt.sh

  - name: start httpd
    service: name={{svc_name}}  state=started enabled=yes
EOF


```



##### 3.5 编写Nginx的安装

```
cat > roles/lb/defaults/main.yml << EOF
---

extra_repo: nginx1

needrpm_items: [
    "python3",
    "nginx",
]

wp_ip: 10.10.2.100
svc_name: nginx

cat > roles/lb/tasks/main.yml << EOF
---

  - include: rpm.yml
    tags: rpm

  - include: config.yml
    tags: config
EOF

cat > roles/lb/tasks/rpm.yml << EOF
---

  - name: enable nginx repo
    shell: amazon-linux-extras enable {{extra_repo}}

  - name: setup need rpm package
    yum: name={{ needrpm_items }} state=latest
    with_items: "{{ needrpm_items }}"
EOF


cat > roles/lb/tasks/config.yml << EOF
---

  - name: Template for nginx lb to wordpress
    template: src=lb.conf.j2 dest=/etc/nginx/conf.d/lb.conf

  - name: start nginx
    service: name={{svc_name}}  state=started enabled=yes
EOF

cat > roles/lb/templates/lb.conf.j2 << EOF
upstream wordpress {
    server {{wp_ip}}:80;
}
server {
    listen     7070;
    access_log /var/log/nginx/wp.access.log main;
    error_log /var/log/nginx/wp.error.log warn;
    location / {
        proxy_pass http://wordpress;
	    proxy_set_header Host $http_host;
    }
}
EOF
```





##### 3.6 检视当前playbook文件

```
[ec2-user@ip-10-10-0-250 wordpress-playbook]$ tree .
.
├── ansible.cfg
├── hosts
├── lb.yml
├── mariadb.yaml
├── roles
│   ├── app
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── files
│   │   │   └── wpsalt.sh
│   │   ├── handler
│   │   ├── tasks
│   │   │   ├── config.yml
│   │   │   ├── getwp.yml
│   │   │   ├── main.yml
│   │   │   └── rpm.yml
│   │   ├── templates
│   │   └── vars
│   ├── lb
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── files
│   │   ├── handler
│   │   ├── tasks
│   │   │   ├── config.yml
│   │   │   ├── main.yml
│   │   │   └── rpm.yml
│   │   ├── templates
│   │   │   └── lb.conf.j2
│   │   └── vars
│   └── mariadb
│       ├── defaults
│       │   └── main.yml
│       ├── files
│       ├── handler
│       ├── tasks
│       │   ├── createdb.yml
│       │   ├── main.yml
│       │   └── rpm.yml
│       ├── templates
│       └── vars
├── site.yml
└── wp.yml

22 directories, 21 files
```



如果上面的你自己没有把握每步都执行成功。你也可以下载整个playbook的所有的文件。

下载地址如下：

http://52.83.226.31:7070/lab-share/lab5/wordpress-playbook.tgz

下载方法：

```
wget http://52.83.226.31:7070/lab-share/lab5/wordpress-playbook.tgz
```



执行编写的playbook

```shell
ansible-playbook -i hosts site.yml
```



整个执行过程大概执行结果输出类似如下：

```shell
[ec2-user@ip-10-10-0-250 wordpress-playbook]$ ansible-playbook -i hosts site.yml

PLAY [MaraDB Tasks] ************************************************************************************************************************************************************

TASK [mariadb : setup python3.8] ***********************************************************************************************************************************************
changed: [10.10.4.100]

TASK [mariadb : setup need rpm package] ****************************************************************************************************************************************
changed: [10.10.4.100] => (item=mariadb-server)
ok: [10.10.4.100] => (item=MySQL-python)
ok: [10.10.4.100] => (item=python3.8)

TASK [mariadb : start MariaDB] *************************************************************************************************************************************************
changed: [10.10.4.100]

TASK [mariadb : create wordpress db] *******************************************************************************************************************************************
changed: [10.10.4.100]

TASK [mariadb : create mysql user for db] **************************************************************************************************************************************
[WARNING]: Module did not set no_log for update_password
changed: [10.10.4.100]

PLAY [Wordpress Tasks] *********************************************************************************************************************************************************

TASK [app : enable php 7.4 repo] ***********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : setup need rpm package] ********************************************************************************************************************************************
changed: [10.10.2.100] => (item=python3)
ok: [10.10.2.100] => (item=mariadb)
ok: [10.10.2.100] => (item=httpd)
ok: [10.10.2.100] => (item=php)
ok: [10.10.2.100] => (item=php-gd)
ok: [10.10.2.100] => (item=php-mysqlnd)
ok: [10.10.2.100] => (item=php-fpm)
ok: [10.10.2.100] => (item=php-pdo)

TASK [app : Download WordPress] ************************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Extract WordPress] *************************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Delete /var/www/html] **********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Copy WordPress] ****************************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Copy WordPress Config] *********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Create upload directory] *******************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Directory Permmision] **********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Directory Permmision] **********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Directory Permmision] **********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : Update WordPress config file] **************************************************************************************************************************************
changed: [10.10.2.100] => (item={u'regexp': u'database_name_here', u'word': u'wordpress'})
changed: [10.10.2.100] => (item={u'regexp': u'username_here', u'word': u'wpuser'})
changed: [10.10.2.100] => (item={u'regexp': u'password_here', u'word': u'myPassword123'})
changed: [10.10.2.100] => (item={u'regexp': u'localhost', u'word': u'10.10.4.100'})

TASK [app : Update httpd config] ***********************************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : insert unique key and salts in wp-config] **************************************************************************************************************************
changed: [10.10.2.100]

TASK [app : start httpd] *******************************************************************************************************************************************************
changed: [10.10.2.100]

PLAY [Nginx Tasks] *************************************************************************************************************************************************************

TASK [lb : enable nginx repo] **************************************************************************************************************************************************
changed: [10.10.0.100]

TASK [lb : setup need rpm package] *********************************************************************************************************************************************
changed: [10.10.0.100] => (item=python3)
ok: [10.10.0.100] => (item=nginx)

TASK [lb : Template for nginx lb to wordpress] *********************************************************************************************************************************
changed: [10.10.0.100]

TASK [lb : start nginx] ********************************************************************************************************************************************************
changed: [10.10.0.100]

PLAY RECAP *********************************************************************************************************************************************************************
10.10.0.100                : ok=4    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
10.10.2.100                : ok=15   changed=15   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
10.10.4.100                : ok=5    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```





#### 目标四：访问wordpres，并配置查看

经过上面的配置，我们查看一下nginx的公网地址：

![T1-A-00](./Lab-5/T4-A-00.jpg)

下面，我们访问一下Nginx， 中国区80端口需要备案，所以我们当前的前端访问配置使用的是7070

http://ec2-52-82-27-42.cn-northwest-1.compute.amazonaws.com.cn:7070

得到如下画面：

![T1-A-00](./Lab-5/T4-A-10.jpg)

我们选择中文，进行下一步:

这里按照图示，按照下面的参考，填入自己的信息。记得复制密码。下一步，直至完成。

![T1-A-00](./Lab-5/T4-A-20.jpg)

下一步，直至完成。

![T1-A-00](./Lab-5/T4-A-30.jpg)

最后我们就得到一个自己的标准的简单三层架构的wordpress.

