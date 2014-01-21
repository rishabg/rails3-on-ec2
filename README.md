rails3-on-ec2
=============

Instructions for create a ubuntu ec2 instance for rails

Host: ec2-54-221-156-94.compute-1.amazonaws.com

ami for Ubuntu LTS 12.04
-------------
    ami-e1e8d395

system update
-------------
    sudo apt-get update - DONE 1/20
    sudo apt-get upgrade - DONE 1/20

configure timezone
-------------
    sudo dpkg-reconfigure tzdata - DONE 1/20

rvm
-------------
    curl -L get.rvm.io | sudo bash -s stable - 
    sudo usermod -a -G rvm ubuntu - 
    rvm install 1.9.3 - 
    rvm use 1.9.3 --default - 

rubygems/rails/passenger
-------------
    gem update --system
    gem install rails
    gem install passenger

nginx
-------------
    sudo apt-get install libcurl4-openssl-dev - DONE 1/19
    rvmsudo passenger-install-nginx-module

nginx configuration
-------------
    /opt/nginx/conf/nginx.conf
    server {
        listen 80;
        server_name www.yourhost.com;
        root /somewhere/public;   # <--- be sure to point to 'public'!
        passenger_enabled on;
    }

guide to nginx
-------------
    located in /usr/local/rvm/gems/ruby-1.9.3-p194/gems/passenger-3.0.12/doc/Users guide Nginx.html

nginx init script
-------------
    git clone https://github.com/hulihanapplications/nginx-init-debian.git
    cd nginx-init-debian
    sudo cp etc/init/nginx.conf /etc/init
    sudo start nginx

mysql
-------------
    sudo apt-get install mysql-server libmysqlclient-dev - DONE 1/20
    sudo service mysql stop
    sudo mkdir /mnt/data
    sudo rsync -av /var/lib/mysql /mnt/data/

Then I edited the datadir line in /etc/mysql/my.cnf:

sudo vi /etc/mysql/my.cnf
-datadir     = /var/lib/mysql
+datadir     = /mnt/data/mysql
Then I edited /etc/apparmor.d/usr.sbin.mysqld:

sudo vi /etc/apparmor.d/usr.sbin.mysqld
-  /var/lib/mysql/ r,
-  /var/lib/mysql/** rwk,
+  /mnt/data/mysql/ r,
+  /mnt/data/mysql/** rwk,

    sudo service mysql start


mysql configuration
-------------
    add to [client]
        default-character-set=utf8

    add to [mysqld]
        character-set-server=utf8
        collation-server=utf8_general_ci
        init-connect='SET NAMES utf8'
    
    to comment
        bind-address           = 127.0.0.1

database and users
-------------
    mysql -u root -p
    create database db1
    CREATE USER 'user1'@'localhost' IDENTIFIED BY '123456';
    GRANT ALL PRIVILEGES ON db1.* TO 'user1'@'localhost' WITH GRANT OPTION;


database data restore
-------------

    r3> /home/deploy/scripts/backup_db.sh
    r3 /home/deploy/backups> tar cvf ilab_refresh.tar 012020141516/*
    r3> scp ilab_refresh.tar ubuntu@ec2-54-221-156-94.compute-1.amazonaws.com:ilab_refresh.tar

    on ec2:
    > sudo mkdir /mnt/data/restore_db
    > cd /mnt/data/restore_db
    > sudo tar xvf ~/ilab_refresh.tar
    > cd 012020141516/ilab_refresh_db
    > sudo mkdir ../versions
    > sudo mv *_version* ../versions/
    > sudo mv sessions.sql.gz ../versions/
    
    
    mysql> create database ilab_ondemand_db;


