# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# Deploy docker swarm?
auto = true

# Set Virtualbox as default provider
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'

# Number of worker nodes - Virtual Machines
numworkers = 2

# Memory for your Virtual Machines
vmmemory = 1024

# Number of vCPU per VM
numcpu = 2

# Which box image to use
box_image = "ubuntu/xenial64"

instances = []

(1..numworkers).each do |n| 
  instances.push({:name => "worker#{n}", :ip => "192.168.10.#{n+2}"})
end

manager_ip = "192.168.10.2"

File.open("./hosts", 'w') { |file| 
  instances.each do |i|
    file.write("#{i[:ip]} #{i[:name]} #{i[:name]}\n")
  end
}

Vagrant.configure("2") do |config|
    config.vm.provision "fix-no-tty", type: "shell" do |s|
      s.privileged = false
      s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
    end
#    config.vm.provider "virtualbox" do |v|
#     	v.memory = vmmemory
#  	v.cpus = numcpu
#    end
    
    config.vm.define "manager" do |i|
      i.vm.box = box_image 
      i.vm.hostname = "manager"
      i.vm.network "private_network", ip: "#{manager_ip}"
      i.vm.provision "shell", path: "./provision.sh"
      i.vm.provider "virtualbox" do |v|
	      v.memory = vmmemory
	      v.cpus = numcpu
      end
      if File.file?("./hosts") 
        i.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
        i.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
      end 
      if auto 
        i.vm.provision "shell", inline: "docker swarm init --advertise-addr #{manager_ip}"
        i.vm.provision "shell", inline: "docker swarm join-token -q worker > /vagrant/token"
	i.vm.provision "shell", inline: "docker service create --name redis --mode global --publish 6379:6379 redis"
      end
    end 

  instances.each do |instance| 
    config.vm.define instance[:name] do |i|
      i.vm.box = box_image
      i.vm.hostname = instance[:name]
      i.vm.network "private_network", ip: "#{instance[:ip]}"
      i.vm.provision "shell", path: "./provision.sh"
      i.vm.provider "virtualbox" do |v|
              v.memory = vmmemory
              v.cpus = numcpu
      end
      if File.file?("./hosts") 
        i.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
        i.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
      end 
      if auto
        i.vm.provision "shell", inline: "docker swarm join --advertise-addr #{instance[:ip]} --listen-addr #{instance[:ip]}:2377 --token `cat /vagrant/token` #{manager_ip}:2377"
      end
    end 
  end
end
