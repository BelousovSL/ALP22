ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'
Vagrant.configure("2") do |config|
    config.vm.define "router1" do |router1|
        router1.vm.box = 'bento/ubuntu-24.04'
        router1.vm.host_name = 'router1'
        router1.vm.network :forwarded_port, host: 2301, guest: 22        
        router1.vm.network :private_network, ip: "10.0.10.1", netmask: "255.255.255.252", virtualbox__intnet: 'r1-r2', adapter: 2
        router1.vm.network :private_network, ip: "10.0.12.1", netmask: "255.255.255.252", virtualbox__intnet: 'r1-r3', adapter: 3
        router1.vm.network :private_network, ip: "192.168.10.1", netmask: "255.255.255.0", virtualbox__intnet: 'net1', adapter: 4
        router1.vm.provider "virtualbox" do |vb|
            vb.memory = 1024            
        end

        router1.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible_router1/provision.yml"
            ansible.inventory_path = "ansible_router1/hosts"
            ansible.host_key_checking = "false"
            ansible.limit = "all"
        end
    end
        
    config.vm.define "router2" do |router2|
        router2.vm.box = 'bento/ubuntu-24.04'
        router2.vm.host_name = 'router2'
        router2.vm.network :forwarded_port, host: 2302, guest: 22        
        router2.vm.network :private_network, ip: "10.0.10.2", netmask: "255.255.255.252", virtualbox__intnet: 'r1-r2', adapter: 2
        router2.vm.network :private_network, ip: "10.0.11.2", netmask: "255.255.255.252", virtualbox__intnet: 'r2-r3', adapter: 3
        router2.vm.network :private_network, ip: "192.168.20.1", netmask: "255.255.255.0", virtualbox__intnet: 'net2', adapter: 4
        router2.vm.provider "virtualbox" do |vb|
            vb.memory = 1024            
        end

        router2.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible_router2/provision.yml"
            ansible.inventory_path = "ansible_router2/hosts"
            ansible.host_key_checking = "false"
            ansible.limit = "all"
        end
    end

    config.vm.define "router3" do |router3|
        router3.vm.box = 'bento/ubuntu-24.04'
        router3.vm.host_name = 'router3'
        router3.vm.network :forwarded_port, host: 2303, guest: 22        
        router3.vm.network :private_network, ip: "10.0.11.1", netmask: "255.255.255.252", virtualbox__intnet: 'r2-r3', adapter: 2
        router3.vm.network :private_network, ip: "10.0.12.2", netmask: "255.255.255.252", virtualbox__intnet: 'r1-r3', adapter: 3
        router3.vm.network :private_network, ip: "192.168.30.1", netmask: "255.255.255.0", virtualbox__intnet: 'net3', adapter: 4
        router3.vm.provider "virtualbox" do |vb|
            vb.memory = 1024            
        end

        router3.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible_router3/provision.yml"
            ansible.inventory_path = "ansible_router3/hosts"
            ansible.host_key_checking = "false"
            ansible.limit = "all"
        end
    end

end