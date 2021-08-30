# Configurando-senha-do-root-MariaDB


Para atribuir uma senha ao usuário root de uma instalação recente:

[root@centos7 ~]# mysqladmin -u root password '123456' 

Trocando a senha já existente:

[root@centos7 ~]# mysqladmin -u root -p123456 password 'teste123'

Podemos testá-la com o comando:

[root@centos7 my.cnf.d]# mysqladmin -u root -p version 
Enter password: 
mysqladmin  Ver 9.1 Distrib 10.1.21-MariaDB, for Linux on x86_64
Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Server version		10.1.21-MariaDB
Protocol version	10
Connection		Localhost via UNIX socket
UNIX socket		/var/lib/mysql/mysql.sock
Uptime:			12 min 55 sec

Threads: 1  Questions: 8  Slow queries: 0  Opens: 16  Flush tables: 1  Open tables: 9  Queries per second avg: 0.010

Também podemos trocar a senha do usuário root utilizando as tabelas do banco:

[root@centos7 ~]# systemctl stop mariadb.service 

[root@centos7 ~]# mysqld_safe --skip-grant-tables & 
[1] 10198

[root@centos7 ~]# mysql -u root 
MariaDB [(none)]> use mysql; 

MariaDB [mysql]> update user set password=PASSWORD("teste333") where User='root'; 
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

MariaDB [mysql]> flush privileges; 
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> quit 
Bye

Matamos os processos em background do mysqld_safe:

[root@centos7 ~]# kill %1

E startamos o banco:

[root@centos7 ~]# systemctl start mariadb

E já podemos conectar no banco com a nova senha.

[root@centos7 ~]# mysql -u root -p 
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 5
Server version: 10.1.21-MariaDB MariaDB Server

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>

Algumas vezes podemos nos deparar com problemas com a senha do banco ao tentar conectar mesmo tendo setado a mesma e trocando-a com o mysqladmin. Para esses casos estes procedimentos foram úteis:

[root@centos7 ~]# systemctl stop mariadb.service 

[root@centos7 ~]# mysqld_safe --skip-grant-tables &

[root@centos7 ~]# mysql_secure_installation
