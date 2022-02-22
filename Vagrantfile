# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/focal64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 8080, host: 28080
  config.vm.network "forwarded_port", guest: 9443, host: 29443
  config.vm.network "forwarded_port", guest: 80, host: 20080  
  config.vm.network "forwarded_port", guest: 443, host: 20443
  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./http", "/var/www/html"
  config.vm.synced_folder "./portainer_data", "/opt/portainer_data"
  config.vm.synced_folder "./nginx", "/opt/nginx"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
      vb.name = "lab-1-1"
  
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.


  config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  #   systemctl reload apache2
  SHELL

  config.vm.provision :docker
  config.vm.provision "shell", inline: <<-SHELL
    usermod -aG docker vagrant
   
    docker network create --driver bridge docker-net

    docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
      --restart=unless-stopped \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v /opt/portainer_data:/data \
      --network docker-net \
      portainer/portainer-ce

    docker run -d -p 8080:80 --name apache2 \
      --restart=unless-stopped \
      -v /var/www/html:/usr/local/apache2/htdocs/ \
      --network docker-net \
      httpd

    docker run -d -p 80:80 -p 443:443 --name nginx \
      --restart=unless-stopped \
      -v /opt/nginx/nginx.conf:/etc/nginx/nginx.conf \
      -v /opt/nginx/conf.d:/etc/nginx/conf.d/ \
      -v /etc/ssl:/etc/ssl/ \
      --network docker-net \
      nginx

SHELL
end
