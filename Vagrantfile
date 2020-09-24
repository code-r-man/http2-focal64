# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  
  # Read the default configuration settings
  _conf = YAML.load(
    File.open(
      File.join(File.dirname(__FILE__), 'provision/default.yml'),
      File::RDONLY
    ).read
  )

  # Read the custom configuration files if they exist
  if File.exists?(File.join(File.dirname(__FILE__), 'site.yml'))
    _site = YAML.load(
      File.open(
        File.join(File.dirname(__FILE__), 'site.yml'),
        File::RDONLY
      ).read
    )
    _conf.merge!(_site) if _site.is_a?(Hash)
  end

  # Box name
  config.vm.box = _conf['vm_box']

  # Network configuration
  config.vm.network "private_network", ip: _conf['ip']

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.name = _conf['hostname']
  end


  puts "_conf: #{_conf}"

  # Hostname
  config.vm.hostname = 'dev.wordpress'

  config.vm.synced_folder _conf['synced_folder'], _conf['site_root'], create: true

  # Ansible
  config.vm.provision "ansible" do |ansible|
    # Pass the settings from the YML file down to playbooks
    ansible.extra_vars = {
      settings: _conf
    }
    ansible.playbook = "provision/playbook.yml"
  end

end
