$base = "geerlingguy/centos7"
$vaquero = "gemini/vaquero"

def medium(config)
    config.vm.provider "virtualbox" do |v|
      v.memory = 512
      v.cpus = 1
    end
end

def large(config)
    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
end

def nat(config)
    config.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--nic2", "natnetwork", "--nat-network2", "vaquero", "--nictype2", "82543GC"]
    end
end

Vagrant.configure(2) do |config|
    config.vm.define "vaquero_server", autostart: true do |vaquero|
        medium(config)
        vaquero.vm.network "private_network", ip: "10.10.10.9", virtualbox__intnet: "vaquero"
        vaquero.vm.hostname = "vaquero"
        vaquero.vm.box = $vaquero
        vaquero.vm.network "forwarded_port", guest: 9090, host: 9090
        vaquero.vm.network "forwarded_port", guest: 8080, host: 8080
        vaquero.vm.network "forwarded_port", guest: 24601, host: 24601
        vaquero.vm.network "forwarded_port", guest: 24602, host: 24602
        vaquero.vm.provision :shell, path: "provision_scripts/docker-start.sh"
    end

    config.vm.define "vaquero_proxy", autostart: false do |vaquero|
        medium(config)
        vaquero.vm.network "private_network", ip: "10.10.10.9", virtualbox__intnet: "vaquero"
        vaquero.vm.hostname = "vaquero"
        vaquero.vm.box = $vaquero
        vaquero.vm.network "forwarded_port", guest: 9090, host: 9090
        vaquero.vm.network "forwarded_port", guest: 8080, host: 8080
        vaquero.vm.network "forwarded_port", guest: 24601, host: 24601
        vaquero.vm.network "forwarded_port", guest: 24602, host: 24602
        vaquero.vm.provision :shell, path: "provision_scripts/docker-start.sh"
    end

    config.vm.define "dnsmasq", autostart: false do |dnsmasq|
        medium(config)
        dnsmasq.vm.network "private_network", ip: "10.10.10.8", virtualbox__intnet: "vaquero"
        dnsmasq.vm.box = $vaquero
        dnsmasq.vm.provision "file", source: "provision_files/dnsmasq-iponly.conf", destination: "/tmp/dnsmasq.conf"
        dnsmasq.vm.provision :shell, path: "provision_scripts/dnsmasq-start.sh"
    end

    config.vm.define "vaquero_other", autostart: false do |vaquero|
        medium(config)
        vaquero.vm.network "private_network", ip: "10.10.10.9", virtualbox__intnet: "vaquero"
        vaquero.vm.hostname = "vaquero"
        vaquero.vm.box = $vaquero
        vaquero.vm.network "forwarded_port", guest: 9090, host: 9090
        vaquero.vm.network "forwarded_port", guest: 8080, host: 8080
        vaquero.vm.network "forwarded_port", guest: 24601, host: 24601
        vaquero.vm.network "forwarded_port", guest: 24602, host: 24602
        vaquero.vm.provision "file", source: "provision_files/dnsmasq-netboot.conf", destination: "/tmp/dnsmasq.conf"
        vaquero.vm.provision :shell, path: "provision_scripts/docker-start.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/dnsmasq-start.sh"
    end

    config.vm.define "build_vaquero", autostart: false do |vaquero|
        medium(config)
        vaquero.vm.network "private_network", ip: "10.10.10.9", virtualbox__intnet: "vaquero"
        vaquero.vm.hostname = "build-vaquero"
        vaquero.vm.box = $base
        vaquero.vm.network "forwarded_port", guest: 9090, host: 9090
        vaquero.vm.network "forwarded_port", guest: 8080, host: 8080
        vaquero.vm.network "forwarded_port", guest: 24601, host: 24601
        vaquero.vm.network "forwarded_port", guest: 24602, host: 24602
        vaquero.vm.provision :shell, path: "provision_scripts/general.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/docker.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/dnsmasq.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/docker-start.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/dnsmasq-start.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/images.sh"
        vaquero.vm.provision :shell, path: "provision_scripts/undionly.sh"
    end
end
