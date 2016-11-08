# -*- mode: ruby -*-
# vi: set ft=ruby :

vb_memory = '2048'
vb_cpus = '4'
hostname = 'vbox.localhost.com'



# for vagrant-hostmanager dhcp.
$logger = Log4r::Logger.new('vagrantfile')
def read_ip_address(machine)
  command =  "ip a | grep 'inet' | grep -v '127.0.0.1' | cut -d: -f2 | awk '{ print $2 }' | cut -f1 -d\"/\""
  result  = ""

  $logger.info "Processing #{ machine.name } ... "

  begin
    # sudo is needed for ifconfig
    machine.communicate.sudo(command) do |type, data|
      result << data if type == :stdout
    end
    $logger.info "Processing #{ machine.name } ... success"
  rescue
    result = "# NOT-UP"
    $logger.info "Processing #{ machine.name } ... not running"
  end

  # the second inet is more accurate
  result.chomp.split("\n").last
end


Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento-centos-6.8"
  config.vm.box_url = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.8_chef-provisionerless.box"
  config.vm.hostname = hostname
  config.vm.network "private_network", type: "dhcp"
  config.vm.synced_folder ".", "/vagrant", type: "nfs"

  # vagrant-omnibus
  config.omnibus.chef_version = :latest
  
  # vagrant-vbguest
  config.vbguest.auto_update = true
  
  # vagrant-hostsmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = vb_memory
    vb.cpus = vb_cpus
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
  end

  config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
    read_ip_address(vm)
  end
end
