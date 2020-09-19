Vagrant is used to deploy multiple virtual machines (VMs) with a click of a button, using a virtualisation software, such as VirtualBox.

Install vagrant from: https://www.vagrantup.com/
Installing vboxfs for folder sharing:
```sh
vagrant plugin install vagrant-vbguest
vagrant vbguest
```

Create a local vagrantfile that contains the deployment configuration, such as:
```sh
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.box_version = "1802.01"
end
```

A list of all available images can be obtained via:
```sh
https://app.vagrantup.com/centos/boxes/7?page=2
```

Starting up the deployment: 
```sh
vagrant up
```
The CentOS image will be downloaded and two VMs will be created.

SSH to a VM:
```sh
vagrant ssh [hostname]
```
Or
```sh
ssh -i .vagrant/machines/VM_Name/virtualbox/private_key vagrant@IP
```

Please look at the below vagrant file that deploys a 3 CentOS VMs and installs an RPM from a shared folder with the host machine:
```sh
# Post installation script
$script = <<SCRIPT
rpm -ivh /RPMs/RPM_file.x86_64.rpm
setenforce 0
sed -i 's/Enabled/Disabled/' /etc/selinux/config
sync
SCRIPT

# Config vargant using the config object
###### CB node 1
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: $script
  config.vm.define "CB-1" do |cbnode|
    # OS type and version
    cbnode.vm.box = "centos/7"
    cbnode.vm.box_version = "1802.01"
    # Set bridged network
    #cbnode.vm.network "public_network", type: "dhcp", bridge: "en0: Wi-Fi (Wireless)"
    cbnode.vm.network "public_network", ip: "192.168.1.200", bridge: "en0: Wi-Fi (Wireless)"
    # Shared folder
    cbnode.vm.synced_folder "/Users/shacharbar/Downloads/", "/RPMs"
    cbnode.vm.provider "virtualbox" do |vb|
      # Set RAM
      vb.customize ["modifyvm", :id, "--memory", 6500]
      # Set Cores
      vb.customize ["modifyvm", :id, "--cpus", 2]
      # Disable audio
      vb.customize ["modifyvm", :id, "--audio", "none"]
      vb.customize ["modifyvm", :id, "--vram", "12"]
      vb.customize ["modifyvm", :id, "--name", "CB-1"]
    end
  end

config.vm.define "CB-2" do |cbnode|
    # OS type and version
    cbnode.vm.box = "centos/7"
    cbnode.vm.box_version = "1802.01"
    # Set bridged network
    #cbnode.vm.network "public_network", type: "dhcp", bridge: "en0: Wi-Fi (Wireless)"
    cbnode.vm.network "public_network", ip: "192.168.1.201", bridge: "en0: Wi-Fi (Wireless)"
    # Shared folder
    cbnode.vm.synced_folder "/Users/shacharbar/Downloads/", "/RPMs"
    cbnode.vm.provider "virtualbox" do |vb|
      # Set RAM
      vb.customize ["modifyvm", :id, "--memory", 6500]
      # Set Cores
      vb.customize ["modifyvm", :id, "--cpus", 2]
      # Disable audio
      vb.customize ["modifyvm", :id, "--audio", "none"]
      vb.customize ["modifyvm", :id, "--vram", "12"]
      vb.customize ["modifyvm", :id, "--name", "CB-2"]
    end
  end

config.vm.define "CB-3" do |cbnode|
    # OS type and version
    cbnode.vm.box = "centos/7"
    cbnode.vm.box_version = "1802.01"
    # Set bridged network
    #cbnode.vm.network "public_network", type: "dhcp", bridge: "en0: Wi-Fi (Wireless)"
    cbnode.vm.network "public_network", ip: "192.168.1.202", bridge: "en0: Wi-Fi (Wireless)"
    # Shared folder
    cbnode.vm.synced_folder "/Users/shacharbar/Downloads/", "/RPMs"
    cbnode.vm.provider "virtualbox" do |vb|
      # Set RAM
      vb.customize ["modifyvm", :id, "--memory", 6500]
      # Set Cores
      vb.customize ["modifyvm", :id, "--cpus", 2]
      # Disable audio
      vb.customize ["modifyvm", :id, "--audio", "none"]
      vb.customize ["modifyvm", :id, "--vram", "12"]
      vb.customize ["modifyvm", :id, "--name", "CB-3"]
    end
  end
end
```
