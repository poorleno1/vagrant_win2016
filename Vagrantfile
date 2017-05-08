# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
    config.vm.network "private_network", ip: "192.168.33.30"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  
  # if Vagrant.has_plugin?("vagrant-proxyconf")
  #  config.proxy.http     = "http://145.247.13.164:3128/"
  #  config.proxy.https    = "http://145.247.13.164:3128/"
  #  config.proxy.no_proxy = "localhost,127.0.0.1,.statoilfuelretail.com"
  # end

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
     vb.gui = false
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "256"
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
	apt-get -y upgrade
	debconf-set-selections <<< "postfix postfix/mailname string your.hostname.com"
	debconf-set-selections <<< "postfix postfix/main_mailer_type string 'Internet Site'"
	apt-get install -y postfix mailutils opendkim opendkim-tools
	
	mv /etc/postfix/main.cf /etc/postfix/main.cf.old
	cp /vagrant/main.cf /etc/postfix/
	cp /vagrant/internal_networks /etc/postfix/
	cp /vagrant/whitelist /etc/postfix/
	cp /vagrant/header_checks /etc/postfix/
	cp /vagrant/mail.private /etc/postfix/dkim.key
	echo "Domain    *" >> /etc/opendkim.conf
	echo "KeyFile    /etc/postfix/dkim.key" >> /etc/opendkim.conf
	echo "Selector    dkim" >> /etc/opendkim.conf
	echo "SOCKET    inet:8891@localhost" >> /etc/opendkim.conf
	echo 'SOCKET="inet:8891@localhost"' >> /etc/default/opendkim
	
	echo "# DKIM" >> /etc/postfix/main.cf
	echo "milter_default_action = accept" >> /etc/postfix/main.cf
	echo "milter_protocol = 2" >> /etc/postfix/main.cf
	echo "smtpd_milters = inet:localhost:8891" >> /etc/postfix/main.cf
	echo "non_smtpd_milters = inet:localhost:8891" >> /etc/postfix/main.cf
	chown root.opendkim /etc/postfix/dkim.key
	chmod 750 /etc/postfix/dkim.key
	
	#in case key is not generated:
	#opendkim-genkey -t -s mail -d circlekeurope.com
	# copy key to /etc/postfix/dkim.key
	
	sudo service opendkim restart
	sudo service postfix restart
  #   sudo apt-get install -y apache2
   SHELL
end
