# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "geerlingguy/centos7"
  # config.vm.network "public_network", bridge: "en0: Wi-Fi (Wireless)"
  # config.vm.provision "ansible", playbook: "playbook.yml"

  # Vagrant generates secure ssh keys by default and put's it in the VM, you don't need this for testing machines that
  # no one will access except from the local machine and will use the insecure key that comes with vagrant.
  config.ssh.insert_key = false

  # By default Vagrant has a sync folder that syncs files between the host and the vagrant box,
  # disable it if you don't need it to prevent any problems with it.
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Configure resources that each VM has
  config.vm.provider :virtualbox do |v|
    v.memory = 1024
    # allows to creaate VMs faster by, instead of creating every machine from scratch it creates one and linkes the
    # others.
    v.linked_clone = true
  end

  # Provisioning with ansible
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "rockylinux-dev-playbook/main.yml"
  end

  config.vm.define "nfsclient1" do |nfsclient1|
    nfsclient1.vm.hostname = "nfsclient1.test"
    nfsclient1.vm.network :private_network, ip: "192.168.0.101"
  end

end
