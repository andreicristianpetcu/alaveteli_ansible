# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  alaveteli_host=ENV['ALAVETELI_HOST'] || "alaveteli.org.dev"

  config.vm.box = "ubuntu/trusty64"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"

    ansible.extra_vars = {
      alaveteli_host: "#{alaveteli_host}"
    }

    ansible.sudo = true

    # Uncomment the following line if you want some verbose output from ansible
    ansible.verbose = "vvvv"

    # Don't try to setup DNS stuff when running things through vagrant
    # because chances are we're just doing things with development VMs anyway
    ansible.skip_tags = "dns"

    ansible.groups = {
      "alaveteli" => ["#{alaveteli_host}"] 
    }
  end

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    # Uncomment this and crank up the memory for a faster build
    v.cpus = 2
  end

  hosts = {
    "#{alaveteli_host}"      => "192.168.10.10"
  }

  hosts.each do |hostname, ip|
    config.vm.define hostname do |host|
      host.vm.network :private_network, ip: ip
      host.vm.hostname = hostname
    end
  end
end
