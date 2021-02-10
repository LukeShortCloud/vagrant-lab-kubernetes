# -*- mode: ruby -*-
# vi: set ft=ruby :

unless File.exists?("id_rsa")
 system("ssh-keygen -t rsa -f id_rsa -N '' -q")
end

Vagrant.configure("2") do |config|
  config.vm.base_mac = nil
  config.vm.box = "generic/ubuntu2004"

  # The kubernetes master
  config.vm.define "ckamaster1" do |ckamaster1|
    ckamaster1.vm.provider "vmware_desktop" do |vmw|
      disk = 'ckamaster1.img'
      vmw.memsize = 4 * 1024
      vmw.numvcpus = 2
      vmw.name = "ckamaster1"
    end

    ckamaster1.vm.hostname = "ckamaster1"
    ckamaster1.vm.network "private_network", ip: "192.168.56.101"
    ckamaster1.vm.network "forwarded_port", guest: 8001, host: 9001
    ckamaster1.vm.provision :shell, path: "provision.sh"
  end
  
 # Opional masters for HA lab
  # The kubernetes master-2
  config.vm.define "ckamaster2" do |ckamaster2|
    ckamaster2.vm.provider "vmware_desktop" do |vmw|
      disk = 'ckamaster2.img'
      vmw.memsize = 2 * 1024
      vmw.numvcpus = 1
      vmw.name = "ckamaster2"
    end

    ckamaster2.vm.hostname = "ckamaster2"
    ckamaster2.vm.network "private_network", ip: "192.168.56.102"
    ckamaster2.vm.provision :shell, path: "provision.sh"
  end

  # The kubernetes master-3
  config.vm.define "ckamaster3" do |ckamaster3|
    ckamaster3.vm.provider "vmware_desktop" do |vmw|
      disk = 'ckamaster3.img'
      vmw.memsize = 2 * 1024
      vmw.numvcpus = 1
      vmw.name = "ckamaster3"
    end

    ckamaster3.vm.hostname = "ckamaster3"
    ckamaster3.vm.network "private_network", ip: "192.168.56.103"
    ckamaster3.vm.provision :shell, path: "provision.sh"
  end

  # The kubernetes worker node
  config.vm.define "ckaworker1" do |ckaworker1|
    ckaworker1.vm.provider "vmware_desktop" do |vmw|
      disk = 'ckaworker1.img'
      vmw.memsize = 2 * 1024
      vmw.numvcpus = 1
      vmw.name = "ckaworker1"
    end

    ckaworker1.vm.hostname = "ckaworker1"
    ckaworker1.vm.network "private_network", ip: "192.168.56.104"
    ckaworker1.vm.provision :shell, path: "provision.sh"
  end
 
   # The external lb node
  config.vm.define "ckalb" do |ckalb|
    ckalb.vm.provider "vmware_desktop" do |vmw|
      disk = 'ckalb.img'
      vmw.memsize = 512
      vmw.numvcpus = 1
      vmw.name = "ckalb"
    end

    ckalb.vm.hostname = "ckalb"
    ckalb.vm.network "private_network", ip: "192.168.56.100"
    ckalb.vm.provision :shell, path: "provision.sh"
    ckalb.vm.provision :shell, path: "ckalb.sh"
  end
end
