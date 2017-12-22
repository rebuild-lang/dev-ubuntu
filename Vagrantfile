# -*- mode: ruby -*-
# vi: set ft=ruby :

require_relative '.vagrant-file' if File.exists?('.vagrant-file.rb')

Vagrant.configure('2') do |config|
  #config.vm.box = 'ubuntu/xenial64'
  config.vm.box = 'bento/ubuntu-16.04'
  config.vm.box_check_update = false
  #config.vm.network 'private_network', type: 'dhcp' # use this for communications
  config.ssh.insert_key = false

  config.vm.define 'ansible-vm', primary: true do |linux|
    linux.vm.hostname = 'linux-vm'
    linux.vm.synced_folder '.', '/vagrant', :mount_options => ['fmode=666']
    linux.ssh.forward_agent = true
    customize_linux_vm(linux) if defined? customize_linux_vm
    # customize virtualbox settings
    linux.vm.provider 'virtualbox' do |v|
      v.memory = 4096
      v.cpus = 4
      v.gui = true
      v.customize ['modifyvm', :id, '--vram', '128']
      v.customize ['modifyvm', :id, '--clipboard', 'bidirectional']
      v.customize ['modifyvm', :id, '--draganddrop', 'bidirectional']
      v.customize ['modifyvm', :id, '--accelerate3d', 'on']
    end
  end
end
