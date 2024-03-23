
IMAGE_NAME = "martyt/ubuntu2204server-arm"
N = 1

# Memory
MASTER_MEMORY=3072
NODE_MEMORY=1024

NETWORK_PREFIX="192.168.165"

Vagrant.configure("2") do |config|

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "#{NETWORK_PREFIX}.10"
        master.vm.hostname = "k8s-master"
        master.vm.provider :vmware_desktop do |vb|
            vb.gui = true
            vb.memory = MASTER_MEMORY
            vb.cpus = 2
        end
    end
    (1..N).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "#{NETWORK_PREFIX}.20"
            node.vm.hostname = "k8s-node-#{i}"
            node.vm.provider :vmware_desktop do |vb|
                vb.gui = true
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
# Vagrant.configure("2") do |config_apps|
#     config_apps.vm.provision "ansible" do |ansible|
#         ansible.limit = ["k8s-master"]
#         ansible.playbook = "deploy_app.yml"
#         ansible.extra_vars = {
#             node_ip: "10.10.50.11",
#         }
#     end
# end