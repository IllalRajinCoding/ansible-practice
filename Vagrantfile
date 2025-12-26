# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Base image Ubuntu 22.04 (Jammy)
  config.vm.box = "ubuntu/jammy64"

  # Disable default synced folder (agar tidak nge-mount folder host)
  config.vm.synced_folder ".", "/dataset", disabled: true

  # Untuk mencegah error VirtualBox Guest Additions
  # (plugin ini sering bikin provisioning rusak)
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
    config.vbguest.no_remote = true
  end

  # ----------- COMMON PROVISION ------------
  $common_setup = <<-SCRIPT
    # Fix repository & update (menggunakan mirror Indonesia)
    sudo sed -i 's|http://archive.ubuntu.com|http://id.archive.ubuntu.com|g' /etc/apt/sources.list

    sudo apt-get clean
    sudo apt-get update -y --fix-missing
    sudo apt-get install -y openssh-server

    # Create cluster user
    if ! id "clusteruser" >/dev/null 2>&1; then
        sudo useradd -m -s /bin/bash clusteruser
    fi

    # Set password clusteruser
    echo "clusteruser:clusterpass" | sudo chpasswd
    sudo usermod -aG sudo clusteruser

    # Change password for default vagrant user
    echo "vagrant:vagrantpass" | sudo chpasswd
  SCRIPT
  # -----------------------------------------

  # ---------------------
  # MASTER NODE
  # ---------------------
  config.vm.define "master" do |master|
    master.vm.hostname = "master"
    master.vm.network "private_network", ip: "192.168.33.20"

    master.vm.provider "virtualbox" do |vb|
      vb.name = "cluster-master"
      vb.memory = 1024
      vb.cpus = 1
    end

    master.vm.provision "shell", inline: $common_setup
  end

  # ---------------------
  # WORKER NODE 1
  # ---------------------
  config.vm.define "worker1" do |worker1|
    worker1.vm.hostname = "worker1"
    worker1.vm.network "private_network", ip: "192.168.33.21"

    worker1.vm.provider "virtualbox" do |vb|
      vb.name = "cluster-worker1"
      vb.memory = 1024
      vb.cpus = 1
    end

    worker1.vm.provision "shell", inline: $common_setup
  end

  # ---------------------
  # WORKER NODE 2
  # ---------------------
  config.vm.define "worker2" do |worker2|
    worker2.vm.hostname = "worker2"
    worker2.vm.network "private_network", ip: "192.168.33.22"

    worker2.vm.provider "virtualbox" do |vb|
      vb.name = "cluster-worker2"
      vb.memory = 1024
      vb.cpus = 1
    end

    worker2.vm.provision "shell", inline: $common_setup
  end
end
