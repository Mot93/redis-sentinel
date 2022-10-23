# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
cluster = YAML.load_file('cluster.yaml')

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  
  #
  # Upgrading the servers to the latest release
  #
  config.vm.provision "shell", inline: <<-SHELL
    yum upgrade -y
    yum install -y vim
  SHELL

  #
  # Creating the list of replica servers
  #
  leader = ""
  replicas = Array.new
  cluster['servers'].each do |server|
    # Creating the list of replicas
    if server['is_replica']
      replicas << server['hostname']
    else
      # If it's not a replica, it has to be a leader
      leader = server['ip']
    end
  end

  #
  # Run Ansible from the Vagrant host to install and configure both redis and sentinel
  #
  config.vm.provision "ansible" do |ansible|
    # Creating the group redis_replica ans setting in the inventory the variable redis_leader_ip
    ansible.groups = {
      "redis_replica" => replicas,
      "all:vars" => {
        "redis_leader_ip" => leader,
      }
    }
    ansible.playbook = "playbook.yml"
  end

  #
  # Globa config
  #
  config.vm.box = "centos/7"
  # config.vm.network "private_network", ip: "dhcp"
  # To bridge the machine onto your network
  # -----> REMEMBER to set the "bridge" field to your desired interface
  # config.vm.network "public_network", type: "dhcp", bridge: "enp3s0"

  #
  # Configuring all servers
  #
  cluster['servers'].each do |server|
    config.vm.define server["hostname"] do |srv|
      srv.vm.network "private_network", ip: server["ip"]
      srv.vm.hostname = server["hostname"]
    end
  end

end
