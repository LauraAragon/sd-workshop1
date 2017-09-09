 #Taller # 1

**Nombre:** Laura Aragón
**Código:** A00268532
**Github URL:**https://github.com/LauraAragon

### Descripción
Este taller consiste en el levantamiento de 4 máquinas virtuales: dos servidores web, un servidor de base de datos
y un balanceador de carga, utilizando un único Vagrantfile y realizando el aprovisinamiento de cada una de las
máquinas virtuales utilizando recetas de chef.

###Comandos necesarios para el aprovisinamiento de las máquinas virtuales

###Servidores Web
yum -y install httpd
yum -y install php
yum -y install php-mysql
yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
yum -y install mysql-community-server
systemctl stop firewalld
systemctl mask firewalld
yum -y install iptables-services
systemctl enable iptables
service network restart
iptables -I INPUT  -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
service iptables save
setsebool -P httpd_can_network_connect=1


###Servidor de base de datos
yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
yum -y install mysql-community-server
systemctl stop firewalld
systemctl mask firewalld
yum -y install iptables-services
systemctl enable iptables
service network restart
iptables -I INPUT 5 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
service iptables save


###Vagrantfile
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


### Recetas
httpd | Información de instalación y configuración para los servidores web
mysql | Información de instalación y configuración para el servidor de base de datos
haproxy | Información de instalación y configuración para el balanceador de carga


### Funcionamiento
![][1]
![][2]
![][4]
![][3]



[1]: Images/Captura1PNG
[2]: Images/Captura2.PNG
[3]: Images/Captura3.PNG
[4]: Images/Captura4.PNG
