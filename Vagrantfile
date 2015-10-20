# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'socket'

$script = <<SCRIPT
echo I am provisioning...
date > /etc/vagrant_provisioned_at
SCRIPT

local_ip = Socket::getaddrinfo(Socket.gethostname,"echo",Socket::AF_INET)[0][3]

Vagrant.configure("2") do |config|
  
  config.vm.box = "ubuntu/precise64"
  config.vm.box_url = "https://atlas.hashicorp.com/ubuntu/boxes/precise64"
  config.vm.host_name = "postgresql" 

  config.vm.provision "shell", inline: $script

  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://" + local_ip + ":53128/"
    config.proxy.https    = "http://" + local_ip + ":53128/"
    config.proxy.no_proxy = "localhost,127.0.0.1," + local_ip
  end

  config.vm.provision "fix-no-tty", type: "shell" do |s|
	s.privileged = false
	s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
  end
  
  config.vm.provision "shell", path: "Vagrant-setup/bootstrap.sh"
  
  # PostgreSQL Server port forwarding
  config.vm.network "forwarded_port", host: 15432, guest: 5432
    
end
