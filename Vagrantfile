# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_NAME = ENV["BOX_NAME"] || "raring"
BOX_URI = ENV["BOX_URI"] || "https://cloud-images.ubuntu.com/vagrant/raring/current/raring-server-cloudimg-amd64-vagrant-disk1.box"
DOKKU_DOMAIN = ENV["DOKKU_DOMAIN"] || "valley.local"
DOKKU_IP = ENV["DOKKU_IP"] || "10.0.0.10"
PREBUILT_STACK_URL = File.exist?("#{File.dirname(__FILE__)}/stack.tgz") ? 'file:///root/dokku/stack.tgz' : nil

make_cmd = "make install"
if PREBUILT_STACK_URL
  make_cmd = "PREBUILT_STACK_URL='#{PREBUILT_STACK_URL}' #{make_cmd}"
end

Vagrant::configure("2") do |config|
  config.vm.box = BOX_NAME
  config.vm.box_url = BOX_URI
  config.vm.synced_folder File.dirname(__FILE__), "/root/dokku"
  config.vm.synced_folder "~/.ssh/gq_credentials", "/home/vagrant/.ssh/gq_credentials"  
  config.vm.synced_folder "../valley", "/valley"    
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.hostname = "#{DOKKU_DOMAIN}"
  config.vm.network :private_network, ip: DOKKU_IP

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    # Ubuntu's Raring 64-bit cloud image is set to a 32-bit Ubuntu OS type by
    # default in Virtualbox and thus will not boot. Manually override that.
    vb.customize ["modifyvm", :id, "--ostype", "Ubuntu_64"]
  end

  config.vm.provision :shell, :inline => "mkdir -p /home/dokku && printf 'export REGISTRY=10.0.0.20:5000\nexport DOKKU_TRACE=1' > /home/dokku/dokkurc && apt-get -y install git && cd /root/dokku && #{make_cmd}"
end
