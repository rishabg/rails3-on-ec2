rails3-on-ec2
=============

Instructions for create a ubuntu ec2 instance for rails

Host: ec2-54-221-156-94.compute-1.amazonaws.com

ami for Ubuntu LTS 12.04
-------------
    ami-e1e8d395

system update (SG DONE)
-------------
    sudo apt-get update - DONE 1/20
    sudo apt-get upgrade - DONE 1/20

configure timezone (SG DONE)
-------------
    sudo dpkg-reconfigure tzdata - DONE 1/20

rvm (JM Done)
-------------
    #Used same versions as in utility
    \curl -sSL https://get.rvm.io | bash
    rvm install ruby-1.9.3-p484 
    rvm use 1.9.3-p484 --default

rubygems/passenger (JM Done)
-------------
    #Used same versions as in utility
    rvm rubygems 2.1.11
    gem install passenger -v=4.0.26

nginx (RG done)
-------------
    sudo apt-get install libcurl4-openssl-dev 
    rvmsudo passenger-install-nginx-module

nginx configuration (RG done)
-------------
    copy server config from nginx.conf in the repo to /opt/nginx/conf

nginx init script (RG done)
-------------
    git clone https://github.com/hulihanapplications/nginx-init-debian.git
    cd nginx-init-debian
    sudo cp etc/init/nginx.conf /etc/init
    sudo start nginx

java and solr (RG done)
-------------
    wget http://mirrors.ibiblio.org/apache/tomcat/tomcat-6/v6.0.37/bin/apache-tomcat-6.0.37.tar.gz
    unpack to /opt/tomcat
    copy solr/tomcat in this repo to /etc/init.d/

    wget http://archive.apache.org/dist/lucene/solr/1.4.1/apache-solr-1.4.1.tgz
    unpack to /opt/solr
    cp -rf /opt/solr/example/solr /opt/solr/production
    cp /opt/solr/example/webapps/solr.war /opt/solr/production/apache-solr-1.4.1.war
    copy xml files from ./solr into /opt/solr/production/conf
    copy ./solr/tomcat into /etc/init.d/tomcat
    copy ./solr/tomcat.conf into /etc/init/tomcat.conf
    

fog (RG TODO)
-------------
    last priority

mysql (SG DONE)
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


mysql configuration (SG TODO)
-------------
    add to [client]
        default-character-set=utf8

    add to [mysqld]
        character-set-server=utf8
        collation-server=utf8_general_ci
        init-connect='SET NAMES utf8'
    
    to comment
        bind-address           = 127.0.0.1

database and users (SG TODO)
-------------
    mysql -u root -p
    create database db1
    CREATE USER 'user1'@'localhost' IDENTIFIED BY '123456';
    GRANT ALL PRIVILEGES ON db1.* TO 'user1'@'localhost' WITH GRANT OPTION;


database data restore (SG)
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
    
    > ls | xargs gunzip -c | mysql -uroot ilab_ondemand_db



