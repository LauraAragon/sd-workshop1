# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.insert_key = false



  config.vm.define :web_server do |web|
    web.vm.box = "centos6"
    web.vm.network "private_network", ip: "192.168.56.101"
    web.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024","--cpus", "1", "--name", "web_server" ]
    end
    config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = "cookbooks"
      chef.add_recipe "httpd"
    end
  end

  config.vm.define :web_server2 do |web2|
    web2.vm.box = "centos6"
    web2.vm.network "private_network", ip: "192.168.56.102"
    web2.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024","--cpus", "1", "--name", "web_server2" ]
    end
    config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = "cookbooks"
      chef.add_recipe "httpd"
    end
  end
  
  config.vm.define :db_server do |db|
    db.vm.box = "centos6"
    db.vm.network "private_network" , ip: "192.168.56.103"
    db.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024","--cpus", "1", "--name", "db_server" ]
    end
    config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = "cookbooks"
      chef.add_recipe "mysql"
    end
  end

 config.vm.define :balance_server do |b|
   b.vm.box = "centos6"
   b.vm.network :private_network, ip: "192.168.56.104"
   b.vm.provider :virtualbox do |vb|
     vb.customize ["modifyvm", :id, "--memory", "1024","--cpus", "1", "--name", "balanceador" ]
   end
   b.vm.provision :chef_solo do |chef|
     chef.install = false
     chef.cookbooks_path = "cookbooks"
     chef.add_recipe "haproxy"
     chef.json = {
      "web_servers" => [
         {"ip":"192.168.56.101"},
         {"ip":"192.168.56.103"}
     ]}
   end
 end
end
