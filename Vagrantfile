# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 2.2.2", "< 3.0.0"

coreos_release_channel = "stable"
coreos_release_version = "1967.3.0"

machines = {
  "master.zone-a.k8s.local" => { ip: "192.168.0.10", type: "master", primary: true,  autostart: true, cpus: 1, memory: 2048 },
  "node.zone-a.k8s.local"   => { ip: "192.168.0.30", type: "node",   primary: false, autostart: true, cpus: 1, memory: 2048 },
  "node.zone-b.k8s.local"   => { ip: "192.168.0.31", type: "node",   primary: false, autostart: true, cpus: 1, memory: 2048 },
  "node.zone-c.k8s.local"   => { ip: "192.168.0.32", type: "node",   primary: false, autostart: true, cpus: 1, memory: 2048 }
}

Vagrant.configure("2") do |config|
  config.vm.box = "coreos-#{coreos_release_channel}"
  config.vm.box_url = "https://#{coreos_release_channel}.release.core-os.net/amd64-usr/#{coreos_release_version}/coreos_production_vagrant.json"

  config.vm.provider "virtualbox" do |vb|
    vb.check_guest_additions = false
    vb.functional_vboxsf = false
  end

  machines.each do |machine, settings|
    config.vm.define machine, primary: settings[:primary], autostart: settings[:autostart] do |node|
      node.vm.hostname = machine
      node.vm.network "private_network", ip: settings[:ip]

      node.vm.provider "virtualbox" do |nvb|
        nvb.name = machine
        nvb.linked_clone = true
        nvb.cpus = settings[:cpus]
        nvb.memory = settings[:memory]
      end

      if machine == machines.keys.last
        node.vm.provision "ansible" do |ansible|
          ansible.playbook = "./ansible/playbooks/cluster.yaml"
          ansible.config_file = "./ansible/ansible.cfg"
          ansible.groups = {
            "master" => machines.select { |k, v| v[:type] == "master" }.keys,
            "etcd" => machines.select { |k, v| v[:type] == "etcd" }.keys,
            "node" => machines.select { |k, v| v[:type] == "node" }.keys,
            "all:vars" => { "ansible_python_interpreter" => "/usr/bin/toolbox python3" }
          }
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
        end
      end
    end
  end
end
