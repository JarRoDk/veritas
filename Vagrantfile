# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "primary" do |primary|

    primary.vm.box = "centos/7"
    primary.vm.hostname = "primary"
	
    primary.vm.provider :virtualbox do |vb|
	    vb.customize ["modifyvm", :id, "--memory", 1024, "--cpus", "2"]
    end

    primary.vm.provision :hosts do |pro| 
		pro.add_host '10.100.50.2', ['primary']
		pro.add_host '10.100.0.2', ['primary']
		pro.add_host '10.100.50.4', ['secondary']
		pro.add_host '10.100.0.4', ['secondary']
	end


    primary.vm.synced_folder "data", "/data"	
	    
	
	
    primary.vm.network "private_network", ip: "10.100.50.2",
       virtualbox__intnet: "mynetwork"

    primary.vm.network "private_network", ip: "10.100.0.2",
       virtualbox__intnet: "mynetwork2"

    # my private tools
    primary.vm.provision :shell, inline: "yum -y install vim expect tmux"
    
    # tools from -precheck 
    primary.vm.provision :shell, inline: "yum -y install net-tools ksh bc ed"

	# install share keys 
    
    primary.vm.provision "shell" do |s|
      ssh_prv_key = ""
      ssh_pub_key = ""
      if File.file?("data/id_rsa_my")
        ssh_prv_key = File.read("data/id_rsa_my")
        ssh_pub_key = File.readlines("data/id_rsa_my.pub").first.strip
      else
       puts "No SSH key found. You will need to remedy this before pushing to the repository."
      end
      s.inline = <<-SHELL
        echo "SSH key provisioning."
    	mkdir /root/.ssh
        touch /root/.ssh/authorized_keys
        echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
    	echo "StrictHostKeyChecking no" > /root/.ssh/config
        echo #{ssh_pub_key} > /root/.ssh/id_rsa.pub
        chmod 644 /root/.ssh/id_rsa.pub
        echo "#{ssh_prv_key}" > /root/.ssh/id_rsa
        chmod 600 /root/.ssh/id_rsa
        #chown -R root:root /root/.ssh
		if [[ ! $(grep '/opt/VRTS/bin/' /root/.bash_profile) ]]; then
          echo 'export PATH=$PATH:/opt/VRTS/bin/' >> /root/.bash_profile
        fi
        exit 0
      SHELL
      s.privileged = true
       
    end






 
    ############################################################
    # Oh My ZSH Install section

    # Install git and zsh prerequisites 
    primary.vm.provision :shell, inline: "yum -y install git"
    primary.vm.provision :shell, inline: "yum -y install zsh"

    # Clone Oh My Zsh from the git repo
    primary.vm.provision :shell, privileged: false,
      inline: "git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh"

    # Copy in the default .zshrc config file
    primary.vm.provision :shell, privileged: false,
      inline: "cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc"

    # Change the vagrant user's shell to use zsh
    primary.vm.provision :shell, inline: "chsh -s /bin/zsh vagrant"

    ############################################################

  ## to unpack vcs in vm make this 
  primary.vm.provision "shell", inline: <<-SHELL
   tar -zxf /vagrant/data/Veritas_InfoScale_7.3.1_CentOS.tar.gz -C /tmp/
  SHELL
  end


  config.vm.define "secondary" do |secondary|

    secondary.vm.box = "centos/7"
    secondary.vm.hostname = "secondary"
	
    secondary.vm.provider :virtualbox do |vb|
	    vb.customize ["modifyvm", :id, "--memory", 1024, "--cpus", "2"]
    end
    
    secondary.vm.synced_folder "data", "/data"	
	
    secondary.vm.provision :hosts do |pro| 
		pro.add_host '10.100.50.2', ['primary']
		pro.add_host '10.100.0.2', ['primary']
		pro.add_host '10.100.50.4', ['secondary']
		pro.add_host '10.100.0.4', ['secondary']
	end
	
    secondary.vm.network "private_network", ip: "10.100.50.4",
       virtualbox__intnet: "mynetwork"

    secondary.vm.network "private_network", ip: "10.100.0.4",
       virtualbox__intnet: "mynetwork2"
    
    # my private tools
    secondary.vm.provision :shell, inline: "yum -y install vim expect tmux"
    
    # tools from -precheck 
    secondary.vm.provision :shell, inline: "yum -y install net-tools ksh bc ed"
    
    ############################################################
    # Oh My ZSH Install section

    # Install git and zsh prerequisites 
    secondary.vm.provision :shell, inline: "yum -y install git"
    secondary.vm.provision :shell, inline: "yum -y install zsh"

    # Clone Oh My Zsh from the git repo
    secondary.vm.provision :shell, privileged: false,
      inline: "git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh"

    # Copy in the default .zshrc config file
    secondary.vm.provision :shell, privileged: false,
      inline: "cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc"

    # Change the vagrant user's shell to use zsh
    secondary.vm.provision :shell, inline: "chsh -s /bin/zsh vagrant"

    ############################################################

    secondary.vm.provision "shell" do |s|
      ssh_prv_key = ""
      ssh_pub_key = ""
      if File.file?("data/id_rsa_my")
        ssh_prv_key = File.read("data/id_rsa_my")
        ssh_pub_key = File.readlines("data/id_rsa_my.pub").first.strip
      else
       puts "No SSH key found. You will need to remedy this before pushing to the repository."
      end
      s.inline = <<-SHELL
        echo "SSH key provisioning."
    	mkdir /root/.ssh
        touch /root/.ssh/authorized_keys
        echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
    	echo "StrictHostKeyChecking no" > /root/.ssh/config
        echo #{ssh_pub_key} > /root/.ssh/id_rsa.pub
        chmod 644 /root/.ssh/id_rsa.pub
        echo "#{ssh_prv_key}" > /root/.ssh/id_rsa
        chmod 600 /root/.ssh/id_rsa
        #chown -R root:root /root/.ssh
		if [[ ! $(grep '/opt/VRTS/bin/' /root/.bash_profile) ]]; then
          echo 'export PATH=$PATH:/opt/VRTS/bin/' >> /root/.bash_profile
        fi
        exit 0
      SHELL
      s.privileged = true
       
    end
  end


  
end
