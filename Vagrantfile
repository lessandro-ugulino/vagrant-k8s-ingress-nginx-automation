# -*- mode: ruby -*-
# vi: set ft=ruby  :
IMAGE_NAME = "bento/ubuntu-16.04"
N = 2

# Memory
MASTER_MEMORY=2048
NODE_MEMORY=1024

Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: <<-SHELL
        apt-get update -y
        echo "10.10.50.10 k8s-master" >> /etc/hosts
        echo "10.10.50.11 k8s-node-1" >> /etc/hosts
        echo "10.10.50.12 k8s-node-2" >> /etc/hosts
    SHELL

    config.ssh.insert_key = false

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "10.10.50.10"
        master.vm.hostname = "k8s-master"
        master.vm.provider :virtualbox do |vb|
            vb.name = "k8s-master"
            vb.memory = MASTER_MEMORY
            vb.cpus = 2
        end
    end
    (1..N).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "10.10.50.#{i + 10}"
            node.vm.hostname = "k8s-node-#{i}"
            node.vm.provider :virtualbox do |vb|
                vb.name = "k8s-node-#{i}"
                vb.memory = NODE_MEMORY
                vb.cpus = 2
            end
        end
    end
    config.vm.provision "ansible", run: "once" do |ansible|
        ansible.limit = "all"
        ansible.playbook = "deploy.yml"
        ansible.extra_vars = {
            node_ip: "10.10.50.10",
        }
    end
end
Vagrant.configure("2") do |config_apps|
    config_apps.vm.provision "ansible" do |ansible|
        ansible.limit = ["k8s-master"]
        ansible.playbook = "deploy_app.yml"
        ansible.extra_vars = {
            node_ip: "10.10.50.11",
        }
    end
end



