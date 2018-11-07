# -*- mode: ruby -*-
# vi: set ft=ruby :

coreos_release_channel = "stable"
coreos_release_version = "1911.3.0"

machines = {
  "master.zone-a.k8s.local" => { ip: "192.168.0.10", primary: true,  autostart: true, cpus: 1, memory: 2048 },
  "node.zone-a.k8s.local"   => { ip: "192.168.0.30", primary: false, autostart: true, cpus: 1, memory: 2048 },
  "node.zone-b.k8s.local"   => { ip: "192.168.0.31", primary: false, autostart: true, cpus: 1, memory: 2048 },
  "node.zone-c.k8s.local"   => { ip: "192.168.0.32", primary: false, autostart: true, cpus: 1, memory: 2048 }
}

Vagrant.configure("2") do |config|
  config.vm.box = "coreos-#{coreos_release_channel}"
  config.vm.box_url = "https://#{coreos_release_channel}.release.core-os.net/amd64-usr/#{coreos_release_version}/coreos_production_vagrant.json"

  config.vm.provider "virtualbox" do |vb|
    vb.check_guest_additions = false
    vb.functional_vboxsf = false
  end

  machines.each do |machine, settings|
    config.vm.define machine, primary: settings[:primary], autostart: settings[:autostart] do |vm|
      vm.vm.hostname = machine
      vm.vm.network "private_network", ip: settings[:ip]

      vm.vm.provider "virtualbox" do |mvb|
        mvb.name = machine
        mvb.cpus = settings[:cpus]
        mvb.memory = settings[:memory]
      end
    end
  end
end
