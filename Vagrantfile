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
  config.vm.box = "ubuntu/xenial32"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"
  config.vm.network "private_network", type: "dhcp"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "512"
    vb.cpus = 1
    # vb.customize ["modifyvm", :id, "--hwvirtex", "off"]
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y vim tmux wget
  SHELL

  # SMSC machine specific stuff
  config.vm.define "smsc" do |smsc|
    smsc.vm.synced_folder "smppsim/", "/vagrant"
    smsc.vm.provision "shell", privileged: false, inline: <<-SHELL
      # setup SMPPSIM
      sudo apt-get install -y openjdk-9-jdk

      sudo rm -vrf /opt/smppsim

      wget -qc 'https://github.com/ngash/smppsim/archive/master.tar.gz'
      tar xf master.tar.gz
      sudo mv -v smppsim-master /opt/smppsim

      sudo cp -v /vagrant/smppsim.props /opt/smppsim/conf/
      sudo cp -v /vagrant/smppsim.service /etc/systemd/system
      sudo cp -v /vagrant/smppsim.path /etc/systemd/system
      sudo systemctl daemon-reload
      sudo systemctl enable smppsim.service
      sudo systemctl enable smppsim.path
      sudo systemctl start smppsim.service
      sudo systemctl start smppsim.path
    SHELL
  end

  # ESME machine specific stuff
  config.vm.define "esme" do |esme|
    esme.vm.synced_folder "kannel/", "/vagrant"

    esme.vm.provision "shell", privileged: false, inline: <<-SHELL
      sudo apt-get install -y kannel
      sudo cp -v /vagrant/bearerbox.service /etc/systemd/system/
      sudo cp -v /vagrant/smsbox.service /etc/systemd/system/
      sudo cp -v /vagrant/smsbox.path /etc/systemd/system/
      sudo systemctl daemon-reload
      sudo systemctl enable bearerbox.service
      sudo systemctl enable smsbox.service
      sudo systemctl enable smsbox.path
      sudo systemctl start bearerbox.service
      sudo systemctl start smsbox.service
      sudo systemctl start smsbox.path
    SHELL

  end
end
