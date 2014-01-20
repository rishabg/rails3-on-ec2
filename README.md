rails3-on-ec2
=============

Instructions for create a ubuntu ec2 instance for rails

ami for Ubuntu LTS 12.04
-------------
    ami-e1e8d395

system update
-------------
    sudo apt-get update - DONE 1/19
    sudo apt-get upgrade - DONE 1/19

configure timezone
-------------
    sudo dpkg-reconfigure tzdata - DONE 1/19

rvm
-------------
    curl -L get.rvm.io | sudo bash -s stable - DONE 1/19
    sudo usermod -a -G rvm ubuntu - DONE 1/19
    exit
    rvm install 1.9.3 - DONE 1/19 (as ubuntu)
    rvm use 1.9.3 --default - DONE 1/19 (as ubuntu)
    exit

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
    sudo apt-get install mysql-server libmysqlclient-dev - DONE 1/19

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

