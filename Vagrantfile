# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # https://docs.vagrantup.com
  config.vm.box = "ubuntu/jammy64"

  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder "data", "/vagrant_data"

  # https://developer.hashicorp.com/vagrant/docs/providers/virtualbox/configuration
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024 * 4
    vb.cpus = 2
  end
  
  (1..2).each do |i|
    config.vm.define "k-#{i}" do |node|

      node.vm.hostname = "k-#{i}"

      node.vm.network "public_network",
        bridge: "enp6s0",
        ip: "192.168.1.#{36+i}"

      node.vm.provision "shell",
        path: "setup",
        args: ["192.168.1.#{36+i}"]
    end
  end
end
