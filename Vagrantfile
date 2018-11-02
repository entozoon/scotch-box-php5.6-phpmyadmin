# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

settings = YAML.load_file 'vagrant.yml'

Vagrant.configure("2") do |config|

    config.vm.box = "scotch/box"
    config.vm.box_version = "2.0"
    config.vm.network "private_network", ip: settings['site']['ip']
    config.vm.hostname = settings['site']['sitename']
    config.vm.synced_folder "./", "/var/www/public", :mount_options => ["dmode=777", "fmode=666"]

    # Optional NFS. Make sure to remove other synced_folder line too
    #config.vm.synced_folder "./", "/var/www/public", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

    config.vm.provider "virtualbox" do |vb|
         vb.memory = "1024"
         vb.name = settings['site']['sitename']
    end

    config.vm.provision "shell", :args => [settings['site']['ip'], settings['site']['sitename'], settings['site']['mysqlpassword']], inline: <<-SHELL

        ip_address=$1
        sitename=$2
        mysqlpassword=$3

        scotchbox="/etc/apache2/sites-enabled/scotchbox.local.conf"
        if [ -f "$scotchbox" ]
        then
            echo "Disabling scotchbox.local.conf. Will probably tell you to restart Apache..."
            sudo a2dissite scotchbox.local.conf
        else
            echo "scotchbox.local.conf not found. No cleanup needed."
        fi

        phpmyadmin="/etc/phpmyadmin"
        if [ -d "$phpmyadmin" ]
        then
            echo "PHPMyAdmin already installed."
        else
            echo "Installing PHPMyAdmin..."
            mysql -uroot -proot -e "SET PASSWORD FOR 'root'@'localhost' = PASSWORD('$mysqlpassword'); FLUSH PRIVILEGES;"
            sudo debconf-set-selections <<< "phpmyadmin phpmyadmin/dbconfig-install boolean true"
            sudo debconf-set-selections <<< "phpmyadmin phpmyadmin/app-password-confirm password $mysqlpassword"
            sudo debconf-set-selections <<< "phpmyadmin phpmyadmin/mysql/admin-pass password $mysqlpassword"
            sudo debconf-set-selections <<< "phpmyadmin phpmyadmin/mysql/app-pass password $mysqlpassword"
            sudo debconf-set-selections <<< "phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2"
            sudo apt-get -y install phpmyadmin
        fi

        echo "Creating database, if it doesn't already exist"
        mysql -uroot -p$mysqlpassword -e "CREATE DATABASE IF NOT EXISTS $sitename;"

        echo "Updating some PHP Variables"
        sudo sed -i 's/upload_max_filesize\s*=.*/upload_max_filesize=16M/g' /etc/php5/apache2/php.inline

        echo "Restarting Apache after all the changes"
        sudo service apache2 restart

        echo "Provisioning complete."

    SHELL

end