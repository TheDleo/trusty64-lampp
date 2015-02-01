# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = "ubuntu/trusty64"

    # Forward ports to Apache and MySQL
    config.vm.network "forwarded_port", guest: 80, host: 8080
    config.vm.network "forwarded_port", guest: 443, host: 8443
    config.vm.network "forwarded_port", guest: 3306, host: 3306
    config.vm.network "forwarded_port", guest: 5432, host: 5432

    config.vm.synced_folder "htdocs", "/var/www/html"

    config.vm.provision "shell", path: "provision.sh"

    config.vm.provider "virtualbox" do |vb|
	  #   # Don't boot with headless mode
	  #   vb.gui = true
	  #
	  #   # Use VBoxManage to customize the VM. For example to change memory:
        vb.customize ["modifyvm", :id, "--memory", "1024"]
    end

    # This section forwards ports 80 and 443 to the vm and requires the vagrant-triggers plugin. 
    # Comment it out if you don't want it.
    # To install it run "vagrant plugin install vagrant-triggers
    # Forward local 80 and 443 since security settings won't allow it.
    config.trigger.after [:provision, :up, :reload] do
        system('echo "
rdr pass on lo0 inet proto tcp from any to 127.0.0.1 port 80 -> 127.0.0.1 port 8080
rdr pass on lo0 inet proto tcp from any to 127.0.0.1 port 443 -> 127.0.0.1 port 8443
" | sudo pfctl -ef -  > /dev/null 2>&1; echo "==> Fowarding Ports: 80 and 443"')
    end

    config.trigger.after [:halt, :destroy] do
        system("sudo pfctl -df /etc/pf.conf > /dev/null 2>&1; echo '==> Removing Port Forwarding'")
    end
end
