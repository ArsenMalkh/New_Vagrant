# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know wha# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/focal64"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "Vagrantfile"

  config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"


  config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get install -y apache2
     apt-get install nlohmann-json-dev
     apt-get install -y gcc-c++ cmake3 make rpm-build valgrind pam-devel ncurses-devel openssl-devel 
   SHELL
  
  config.vm.provision :salt do |salt|
		salt.masterless = false
		salt.run_highstate = false
		config.ssh.pty = false
		
	end
  config.vm.define "master" do |master|
		master.vm.synced_folder "salt-files", "/srv/salt/"
		# "/srv/salt"
		master.vm.network "private_network", ip: "192.168.50.10"

		master.vm.provision :salt do |saltmaster|
			saltmaster.install_master = true
			saltmaster.minion_config = "configs/master"
			#pem
			saltmaster.minion_key = "keys/master/master.pem"
			saltmaster.minion_pub = "keys/master/master.pub"
			saltmaster.master_key = "keys/master/master.pem"
			saltmaster.master_pub = "keys/master/master.pub"
			saltmaster.seed_master = {
				master: 'keys/master/master.pub',
				minion: 'keys/minion/minion.pub'
			}
			saltmaster.install_type = "stable"
			saltmaster.verbose = true
			saltmaster.colorize = true
			saltmaster.bootstrap_options = "-F -P -c /tmp"
		end
	end
  
  config.vm.define "minion" do |minion|
		minion.vm.synced_folder "salt-files", "/srv/salt"
		minion.vm.network "private_network", ip: "192.168.50.20"

		minion.vm.provision :salt do |saltminion|
			saltminion.minion_config = "configs/minion"
			saltminion.minion_key = "keys/minion/minion.pem"
			saltminion.minion_pub = "keys/minion/minion.pub"
			saltminion.install_type = "stable"
			saltminion.verbose = true
			saltminion.colorize = true
			saltminion.bootstrap_options = "-F -P -c /tmp"
		end
	end 
end
