# TODO: set vagrant ssh to 0.0.0.0 https://www.vagrantup.com/docs/vagrantfile/ssh_settings

Vagrant.configure("2") do |config|
  
  #
  # Upgrading the servers to the latest versions
  #
  config.vm.provision "shell", inline: <<-SHELL
    yum upgrade -y
    yum install -y vim
  SHELL

  #
  # Run Ansible from the Vagrant Host ron install redis
  #
  config.vm.provision "ansible" do |ansible|
    ansible.groups = {
      "redis_replica" => ["redis_replica_1", "redis_replica_2"]
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
  config.vm.network "public_network", type: "dhcp", bridge: "enp3s0"

  #
  # Main node
  #
  config.vm.define "redis_main", primary: true do |redis_main|
    redis_main.vm.hostname = "redis4main.local"
    redis_main.vm.network "private_network", ip: "192.168.56.10"
    redis_main.vm.network "forwarded_port", guest: 22, host: 4420, protocol: "tcp"
  end

  #
  # Replica 1
  #
  config.vm.define "redis_replica_1" do |redis_replica_1|
    redis_replica_1.vm.hostname = "redis4replica1.local"
    redis_replica_1.vm.network "private_network", ip: "192.168.56.11"
    redis_replica_1.vm.network "forwarded_port", guest: 22, host: 4421, protocol: "tcp"
  end

  #
  # Replica 2
  #
  config.vm.define "redis_replica_2" do |redis_replica_2|
    redis_replica_2.vm.hostname = "redis4replica2.local"
    redis_replica_2.vm.network "private_network", ip: "192.168.56.12"
    redis_replica_2.vm.network "forwarded_port", guest: 22, host: 4422, protocol: "tcp"
  end

end
