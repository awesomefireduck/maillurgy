# -*- mode: ruby -*-
# vi: set ft=ruby :


$install_rust_script = <<SCRIPT
set -e
sudo apt-get install -y curl libssl-dev
curl https://sh.rustup.rs -sSf > ~/rustup.sh
sh ~/rustup.sh -y
sudo systemctl reboot
SCRIPT


VAGRANTFILE_API_VERSION = "2"

unless Vagrant.has_plugin?("vagrant-vbguest") and Vagrant.has_plugin?("vagrant-hostmanager")
  raise "See below:\n\n\nNeccessary plugins are not installed! use:\n\n\t`vagrant plugin install vagrant-hostmanager vagrant-vbguest`\n\nto install them"
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vbguest.auto_update = false

  # disable default shared folder. (default is a RW mount to the host, WTF???)
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.hostmanager.enabled = false

  config.vm.define "development" do |node|
    node.vm.box = "debian/jessie64"
    node.vbguest.auto_update = true
    node.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end

    node.vm.network :private_network, ip: "192.166.240.2"
    node.vm.network "forwarded_port", guest: 3000, host: 3000

    node.vm.hostname = 'development.vm'

    config.vm.provision "shell", inline: $install_rust_script, privileged: false


    node.vm.synced_folder "./", "/home/vagrant/smtp", options: ["rw"], type: "nfs"
  end

end
