#
# Copyright (c) 2015 Davide Guerri <davide.guerri@gmail.com>
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
# implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
#
# Vagrant demo setup for Ansible-OpenStack roles
#
##############################################################################

COMPUTE_NODES = (ENV['COMPUTE_NODES'] || 2).to_i
VAGRANT_BOX_NAME = ENV['BOX_NAME'] || 'trusty64'
CONTROLLER_RAM = (ENV['CONTROLLER_RAM'] || 4096).to_i
NETWORK_RAM = (ENV['NETWORK_RAM'] || 2048).to_i
COMPUTE_RAM = (ENV['COMPUTE_RAM'] || 4096).to_i
NESTED_VIRT = (ENV['NESTED_VIRT'] || 'true') == 'true'
LIBVIRT_DRIVER = ENV['LIBVIRT_DRIVER'] || 'kvm'
CACHE_SCOPE = ENV['CACHE_SCOPE'] || :machine
EXTERNAL_NETWORK_IF = ENV['EXTERNAL_NETWORK_IF'] || 'veth1'
#Fake uplink as in midonet docs

cluster = {
  "mido-nsdb1"  => { :ip => "10.1.2.101", :cpus => 4, :mem => 1024 },
  "mido-nsdb2"  => { :ip => "10.1.2.102", :cpus => 4, :mem => 1024 },
  "mido-nsdb3"  => { :ip => "10.1.2.103", :cpus => 4, :mem => 1024 }
}


Vagrant.configure('2') do |config|

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.auto_detect = false
    config.cache.enable :apt
    config.cache.scope = CACHE_SCOPE
  end

  config.ssh.insert_key = false

  config.vm.synced_folder '.', '/vagrant', disabled: true
  # Cluster NSDB for midonet
  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|
        cfg.vm.box = VAGRANT_BOX_NAME
        cfg.vm.hostname = hostname
        cfg.vm.network :private_network, ip: "#{info[:ip]}"

        cfg.vm.provider :libvirt do |vb|
           vb.memory = 1024
           vb.cpus = 4
           vb.driver = LIBVIRT_DRIVER 
        end

    end # end config

  end #end cluster


  # Cloud controller
  config.vm.define 'controller' do |server|
    server.vm.hostname = 'controller'

    server.vm.box = VAGRANT_BOX_NAME

    # Management network (eth1)
    server.vm.network :private_network, ip: '10.1.2.10'

    %w(parallels virtualbox libvirt vmware_fusion).each do |provider|
      server.vm.provider provider do |c|
        c.memory = CONTROLLER_RAM
        c.cpus = 4
        c.driver = LIBVIRT_DRIVER if provider == 'libvirt'
      end
    end
  end

  # Network controller
  config.vm.define 'network' do |server|
    server.vm.hostname = 'network'
    server.vm.box = VAGRANT_BOX_NAME

    # Management network (eth1)
    server.vm.network :private_network, ip: '10.1.2.20'

    # Tunnels network (eth2)
    server.vm.network :private_network, ip: '192.168.129.5'

    # External network (eth3)
    server.vm.network :private_network, ip: '192.168.5.5'
    # External network (eth3) - Mixed syntax to accomodate libvirt
#    server.vm.network :public_network, mode: 'bridge',
#                                       dev: EXTERNAL_NETWORK_IF,
#					mac: 'be:5c:5f:18:a3:a4'	

    %w(parallels virtualbox libvirt vmware_fusion).each do |provider|
      server.vm.provider provider do |c|
        c.memory = NETWORK_RAM
        c.cpus = 4
        c.driver = LIBVIRT_DRIVER if provider == 'libvirt'
      end
    end
  end

  # Compute nodes
  COMPUTE_NODES.times do |number|
    config.vm.define "compute#{number + 1}" do |server|
      server.vm.hostname = "compute#{number + 1}"
      server.vm.box = VAGRANT_BOX_NAME

      # Management network (eth1)
      server.vm.network :private_network, ip: "10.1.2.#{30 + number}"

      # Tunnels network (eth2)
      server.vm.network :private_network, ip: "192.168.129.#{6 + number}"

      # Provider specific settings
      %w(parallels virtualbox libvirt vmware_fusion).each do |provider|
        server.vm.provider provider do |c|
          c.memory = COMPUTE_RAM
          c.cpus = 4

          c.driver = LIBVIRT_DRIVER if provider == 'libvirt'
          if NESTED_VIRT
            c.vmx['vhv.enable'] = 'TRUE' if provider == 'vmware_fusion'
            c.nested = true if provider == 'libvirt'
            c.customize [
              'set', :id, '--nested-virt', 'on'
            ] if provider == 'parallels'
          end
        end
      end
    end
  end

  # Ansible provisioning
  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'playbook.yml'
    #ansible.verbose = "vvv"
    ansible.limit = 'all'
    ansible.sudo = true
    ansible.extra_vars = {
                ansible_ssh_user: 'vagrant',
                 }
    ansible.groups = {
      'controller' => 'controller',
      'network' => 'network',
      'compute_nodes' => COMPUTE_NODES.times.map { |x| "compute#{x + 1}" },
      'nsdb' => cluster.keys
    }
  end
end
