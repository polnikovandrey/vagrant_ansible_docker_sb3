### Configuration parameters ###
# Virtual machines to initialize count
VM_COUNT = 3

Vagrant.configure("2") do |config|

    config.ssh.insert_key = false

	(1..VM_COUNT).each do |i|

		config.vm.define "server#{i}" do |web|
			web.vm.box = "ubuntu/focal64"
			web.vm.network "forwarded_port", id: "ssh", host: 2221 + i, guest: 22
			web.vm.network "private_network", ip: "10.11.10.#{i + 1}", virtualbox__intnet: true
			if i == 1
			    web.vm.network "forwarded_port", id: "auth-client", host: 8080, guest: 8080
			    web.vm.network "forwarded_port", id: "auth-server", host: 9000, guest: 9000
			end
			web.vm.hostname = "server#{i}"

			web.vm.provider "virtualbox" do |v|
			    v.name = "server#{i}"
			    v.memory = 2048
			    v.cpus = 4
			end

			web.vm.provision "shell" do |s|
			    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
			    s.inline = <<-SHELL
			    echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
			    echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
			    SHELL
			end

			if i == VM_COUNT
			    web.vm.provision :ansible do |ansible|
			        ansible.playbook = "playbook.yml"
			        ansible.inventory_path = "inventory/cluster"
			        ansible.limit = "all"
			        # ansible.verbose = "v"
			    end
			end

	    end

	end

end

# ansible-playbook -i inventory --private-key=~/.vagrant.d/insecure_private_key -u vagrant dev.yml --tags="nginx, php"