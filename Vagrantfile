# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  alaveteli_host = "alaveteli.org.dev"

  # config.vm.box = "ubuntu/trusty64"
  config.vm.box = "ubuntu/focal64"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"

    # ansible.sudo = true
    ansible.become = true
    ansible.become_user = "vagrant"

    # Uncomment the following line if you want some verbose output from ansible
    # ansible.verbose = "vvvv"

    # Don't try to setup DNS stuff when running things through vagrant
    # because chances are we're just doing things with development VMs anyway
    ansible.skip_tags = "nondev"
    # ansible.tags = "sites"

    ansible.groups = {
      "alaveteli" => ["#{alaveteli_host}"]
    }
    ansible.host_vars = {
      "#{alaveteli_host}" => {
        "ansible_user" => "vagrant",
        "ansible_port" => "2222",
        # "ansible_distribution_release" => "trusty"
        # $(lsb_release -cs)
      }
    }
  end

  config.vm.provision "shell", inline: "sudo ufw allow 8080"

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    # Uncomment this and crank up the memory for a faster build
    v.cpus = 4
  end

  hosts = {
    "#{alaveteli_host}"      => "192.168.10.10"
  }

  hosts.each do |hostname, ip|
    config.vm.define hostname do |host|
      host.vm.network "forwarded_port", guest: 5432, host: 5432, auto_correct: true
      host.vm.network :private_network, ip: ip
      host.vm.hostname = hostname
    end
  end
end
