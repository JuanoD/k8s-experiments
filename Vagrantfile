# -*- mode: ruby -*-
# vi: set ft=ruby :

workers = Integer(ENV["WORKERS"] || 1)
servers = Integer(ENV["SERVERS"] || 1)
machines = workers + servers

Vagrant.configure("2") do |config|
  # https://docs.vagrantup.com
  config.vm.box = "bento/ubuntu-24.04"

  config.vm.synced_folder ".", "/vagrant", disabled: true
  
  (1..machines).each do |i|
    config.vm.define "k-#{i}" do |node|
      # https://developer.hashicorp.com/vagrant/docs/providers/virtualbox/configuration
      node.vm.provider "virtualbox" do |vb|
        if i <= servers
          vb.memory = 1024 * 5
          vb.cpus = 4
        else
          vb.memory = 1024 * 4
          vb.cpus = 2
        end
        # vb.customize ['modifyvm', :id, '--graphicscontroller', 'vmsvga']
      end
      node.vm.hostname = "k-#{i}"

      node.vm.network "public_network",
        bridge: "enp6s0",
        ip: "192.168.1.#{36+i}"

      if machines == i
        node.vm.provision :ansible do |ansible|
          ansible.limit = "all"
          ansible.compatibility_mode = "2.0"
          ansible.groups = {
            "server" => ["k-[1:#{servers}]"],
            "server:vars" => {
              "rke2_type" => "server",
              "server_only" => "",
              "worker_only" => "\"# \""
            },
            "worker" => ["k-[#{servers + 1}:#{machines}]"],
            "worker:vars" => {
              "rke2_type" => "agent",
              "server_only" => "\"# \"",
              "worker_only" => ""
            },
            "all_groups:children" => ["server", "worker"],
            "all_groups:vars" => {
              # "token" => "#{Random.bytes(8).unpack1('H*')}.#{Random.bytes(128).unpack1('H*')}",
              "token" => "f8cf4304e8d69941.6bc2bac60edc7414b2d7f15c6b936648910aff8d6c5213c3f40a0d70e79bf5ff9ca65cbd6a88bdf1a8d7339c74333d38b73edb573e4ddf09aa58a765c76515689256794203fe07f9c7deb3747fc7667a0568febcf99559406cd090ca9721cc9a5925e9b0a19754761a622cbaab7d126802abc3e34a2c91cc665a19cddb42edd4", # Generated with `echo "$(openssl rand -hex 8).$(openssl rand -hex 128)"`
              "version" => "v1.31.2+rke2r1"
            }
          }

          ansible.host_vars = {}
          (1..machines).each do |j|
            ansible.host_vars.merge!({
              "k-#{j}" => {
                "node_ip" => "192.168.1.#{36 + j}",
                "leader_exclude" => j==1 ? "\"# \"" : ""
              }
            })
          end

          ansible.playbook = "setup/playbook.yml"
        end
      end

    end
  end
end
