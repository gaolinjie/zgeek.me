HOWTO deploy on Linode
======================

###Build Ubuntu 12.10 on Linode and access the server
	$ ssh root@106.187.37.xxx
	# ssh to server
	# if encounter 'Host key verification failed', just delete ~/.ssh/known_hosts file

###Install Mysql
	$ apt-get update
	$ apt-get install mysql-server mysql-client

###Installing tools and dependencies
	$ apt-get install python-setuptools 
	$ easy_install pip 
	$ apt-get install git 
	$ apt-get install nginx 
	$ pip install supervisor 

###Config Git
	$ ssh-keygen -t rsa -C "3n1b.com@gmail.com"
	$ cat ~/.ssh/id_rsa.pub
	# copy and paste the RSA key to the Deploy keys setting
	$ git config --global user.name "3n1b.com"  
	$ git config --global user.email 3n1b.com@gmail.com  

###Make directories for your app
	$ mkdir /srv/www

###Pull in source code
	$ cd /srv/www/
	$ git clone git@github.com:gaolinjie/3n1b.com.git
	$ cd 3n1b.com

###Install web app required modules
	$ pip install -r requirements.txt

###Install python mysql
	$ easy_install -U distribute
	$ apt-get install libmysqld-dev libmysqlclient-dev
        $ apt-get install python-dev
	$ pip install mysql-python
	$ apt-get install python-MySQLdb

###Install PIL
	$ apt-get build-dep python-imaging 
	$ apt-get install libjpeg8 libjpeg62-dev libfreetype6 libfreetype6-dev
	$ ln -s /usr/lib/x86_64-linux-gnu/libjpeg.so /usr/lib
	$ ln -s /usr/lib/x86_64-linux-gnu/libfreetype.so /usr/lib
	$ ln -s /usr/lib/x86_64-linux-gnu/libz.so /usr/lib
	$ pip install -U PIL
	# pip install http://effbot.org/downloads/Imaging-1.1.7.tar.gz

###Create database and then execute sql file in dbstructure/
	$ mysql -u root -p
	mysql> CREATE DATABASE zgeek;
	mysql> GRANT ALL PRIVILEGES ON zgeek.* TO 'zgeek'@'localhost' IDENTIFIED BY 'zgeek';
	mysql> exit
	$ mysql -u zgeek -p --database=zgeek < dbstructure/zgeek.sql
	$ mysql -u zgeek -p --database=zgeek < dbstructure/node.sql
	$ mysql -u zgeek -p --database=zgeek < dbstructure/plane.sql
	$ mysql -u zgeek -p --database=zgeek < dbstructure/interest.sql
	$ mysql -u zgeek -p --database=zgeek < dbstructure/follow.sql
	$ mysql -u zgeek -p --database=zgeek < dbstructure/message.sql

###Install Torndb
        $ cd /
        $ git clone git://github.com/bdarnell/torndb.git
        $ cd /torndb
        $ python setup.py build
        $ python setup.py install

###Create symbolic links to conf files
	$ cd /etc/nginx 
	$ rm nginx.conf
	$ ln -s /srv/www/zgeek.me/conf/nginx.conf nginx.conf 
	$ cd
	$ ln -s /srv/www/zgeek.me/conf/supervisord.conf supervisord.conf  

###Create nginx user
	$ adduser --system --no-create-home --disabled-login --disabled-password --group nginx 

###Create a logs directory:
	$ mkdir ~/logs 

###Start Supervisor and Nginx
	$ supervisord
	$ /etc/init.d/nginx start

###Visit your public IP address and enjoy!

###Update your web app
	$ cd /srv/www/zgeek.me
	$ git pull
