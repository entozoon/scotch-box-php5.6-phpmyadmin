# Scotch-box LEGACY

- Vagrant
- PHP 5.6
- MySQL 5.5 (or 5.6 with optional extra steps; see end);
- PhpMyAdmin

## Usage

Start, SSH into it, and stop when done:

    vagrant up
    vagrant ssh
    vagrant halt

http://192.168.33.10

### PhpMyAdmin MySQL

    http://192.168.33.10/phpmyadmin
    root
    root

## Requirements

- [Vagrant](https://www.vagrantup.com)
- [Virtual Box](https://www.virtualbox.org/)

## Troubleshooting

Machines running all over the place? Open up virtual box and you should be able to start/stop/destroy them there

## MySQL 5.6

    vagrant ssh
    wget http://dev.mysql.com/get/mysql-apt-config_0.8.0-1_all.deb
    sudo dpkg -i mysql-apt-config_0.8.0-1_all.deb
    (Hit enter / ok to everything)
    sudo apt-get update
    sudo apt-get install mysql-server
    exit
    vagrant reload
