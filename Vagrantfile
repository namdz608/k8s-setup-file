Vagrant.configure("2") do |config|

  config.vm.define "vknam" do |vknam|
    vknam.vm.box = "geerlingguy/ubuntu2004"
    vknam.vm.hostname = "vknam"
	vknam.vm.network "private_network", ip: "192.168.10.12"
	vknam.vm.provider "virtualbox" do |vb|
     vb.memory = "5120"
     vb.cpus = 4
   end
  end

  config.vm.define "nam" do |nam|
    nam.vm.box = "geerlingguy/ubuntu2004"
    nam.vm.hostname = "nam"
	nam.vm.network "private_network", ip: "192.168.10.13"
  nam.vm.provider "virtualbox" do |vb|
    vb.memory = "5120"
    vb.cpus = 4
   end
  end
  
  config.vm.define "masterk8s" do |masterk8s|
    masterk8s.vm.box = "geerlingguy/ubuntu2004"
    masterk8s.vm.hostname = "masterk8s"
	masterk8s.vm.network "private_network", ip: "192.168.10.14"
  masterk8s.vm.provider "virtualbox" do |vb|
    vb.memory = "5120"
    vb.cpus = 2
   end 
  end

  # config.vm.define "k8s" do |k8s|
  #   k8s.vm.box = "geerlingguy/ubuntu2004"
  #   k8s.vm.hostname = "k8s"
  # k8s.vm.network "private_network", ip: "192.168.10.15"
  # k8s.vm.provider "virtualbox" do |vb|
  #   vb.memory = "5120"
  #   vb.cpus = 4
  #  end 
  # end
end
