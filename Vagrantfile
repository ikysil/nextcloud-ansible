# -*- mode: ruby -*-
# vi: set ft=ruby :

unless Vagrant.has_plugin?("vagrant-hostmanager")
  raise "Please install vagrant-hostmanager with \nvagrant plugin install vagrant-hostmanager"
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.ignore_private_ip = false
  end

  if Vagrant.has_plugin?("vagrant-vbguest")
    # set auto_update to false, if you do NOT want to check the correct
    # additions version when booting this machine
    config.vbguest.auto_update = false
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.vm.define "target" do |target|
    target.vm.network "private_network", ip: "10.100.1.3"
    target.vm.hostname = "target.private"
    target.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 1
      vb.name = "nextcloud"
      vb.customize ["modifyvm", :id, "--groups", "/nextcloud-ansible"]

      # bad I/O performance with Linux host otherwise
      # vb.customize ["storagectl", :id, "--name", "SCSI", "--hostiocache", "on"]

      vb.customize ["modifyvm", :id, "--rtcuseutc", "on"]
      vb.customize ["modifyvm", :id, "--chipset", "ich9"]
    end
  end

  config.vm.define "installer", autostart: true do |installer|
    installer.vm.network "private_network", ip: "10.100.1.2"
    installer.vm.hostname = "installer.private"
    installer.vm.synced_folder ENV["HOME"] + "/.ssh", "/var/host-ssh"
    installer.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 1
      vb.name = "nextcloud-installer"
      vb.customize ["modifyvm", :id, "--groups", "/nextcloud-ansible"]

      vb.customize ["modifyvm", :id, "--rtcuseutc", "on"]
    end

    installer.vm.provision "shell", privileged: true, inline: <<-SHELL
      apt-get update && apt-get -qy upgrade
      apt-get install -qy --no-install-recommends ansible sshpass
    SHELL
  end

end
