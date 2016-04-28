VAGRANTFILE_API_VERSION = "2"

master = {
  :name => "node1",
  :ip   => "192.168.33.10",
  :mem  => 1024,
  :cpus => 2
}

workers = [
  {
    :name => "node2",
    :ip   => "192.168.33.11",
    :mem  => 256,
    :cpus => 2
  },
  {
    :name => "node3",
    :ip   => "192.168.33.12",
    :mem  => 256,
    :cpus => 2
  }
]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"

  # configuration for the master node
  config.vm.define "node1" do |conf_master|
    # npm requires some memory to install all dependencies
    conf_master.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--nictype1", "virtio"]
      v.memory = 1024
      v.cpus = 2
    end
    # RESTful service is running at port 3030
    conf_master.vm.network :forwarded_port, guest: 3030, host: 3030
    # Web front-end is running on port 3000
    conf_master.vm.network :forwarded_port, guest: 3000, host: 3000
    conf_master.vm.network "private_network", ip: "192.168.33.10"
  end

  # configuration for workers
  workers.each do |opts|
    config.vm.define opts[:name] do |conf_worker|
      conf_worker.vm.network "private_network", ip: opts[:ip]

      conf_worker.vm.provider "vbox" do |v|
        v.customize ["modifyvm", :id, "--nictype1", "virtio"]
        v.memory = opts[:mem]
        v.cpus = opts[:cpus]
      end

      conf_worker.vm.provision "file",
        source: "config/mf_config.ini",
        destination: "/home/vagrant/monitoring-agent/dist/mf_config.ini"
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.sudo = true,
    ansible.playbook = "ansible/site.yml"
    ansible.extra_vars = {
      user: "vagrant"
    }
    ansible.groups = {
      # RESTful service plus Web front-end gets installed on the master
      "master" => [ "node1" ],
      # Only monitoring agents will be installed on these nodes
      "worker" => [ "node[2:3]" ],
    }
  end
end
