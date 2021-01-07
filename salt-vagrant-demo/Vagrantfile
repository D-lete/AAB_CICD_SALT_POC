# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  os_1 = "bento/ubuntu-18.04" ##os
  os_2 = "peru/windows-server-2016-standard-x64-eval"
    os_2_version = "20201203.01" #See if this works?
  os_3 = "StefanScherer/windows_10"
  net_ip = "192.168.50"

  config.vm.define :master, primary: true do |master_config|
    master_config.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        vb.cpus = 1
        vb.name = "master"
    end
    
    master_config.vm.box = "#{os_1}"
    master_config.vm.host_name = 'saltmaster.local'
    master_config.vm.network "private_network", ip: "#{net_ip}.10"
    master_config.vm.synced_folder "saltstack/salt/", "/srv/salt"
    master_config.vm.synced_folder "saltstack/pillar/", "/srv/pillar"

    master_config.vm.provision :salt do |salt|
      salt.master_config = "saltstack/etc/master"
      salt.master_key = "saltstack/keys/master_minion.pem"
      salt.master_pub = "saltstack/keys/master_minion.pub"
      salt.minion_key = "saltstack/keys/master_minion.pem"
      salt.minion_pub = "saltstack/keys/master_minion.pub"
      salt.seed_master = {
                          "VM24-APP-OSX-RISK" => "saltstack/keys/VM24-APP-OSX-RISK.pub",
                          "VM25-WEB-OSX-FIN" => "saltstack/keys/VM25-WEB-OSX-FIN.pub",
                          "VM26-APP-OSX-FIN" => "saltstack/keys/VM26-APP-OSX-FIN.pub",
                          "VM27-REP-OSX-FIN" => "saltstack/keys/VM27-REP-OSX-FIN.pub",
                          "VM29-RP-OSX-NEA" => "saltstack/keys/VM29-RP-OSX-NEA.pub",
                          "VM29-RP-OSX-AC" => "saltstack/keys/VM29-RP-OSX-AC.pub",
                          "VM31-DB-OSX-FDA" => "saltstack/keys/VM31-DB-OSX-FDA.pub",
                          "VM99-CLI-OSX-PORTAL" => "saltstack/keys/VM99-CLI-OSX-PORTAL.pub",
                         }

      salt.install_type = "stable"
      salt.install_master = true
      salt.no_minion = true
      salt.verbose = true
      salt.colorize = true
      salt.bootstrap_options = "-P -c /tmp -x python3"
    end
  end


  [
    ["VM24-APP-OSX-RISK",    "#{net_ip}.11",    "1024",    os_2 ],
    ["VM25-WEB-OSX-FIN",    "#{net_ip}.12",    "1024",    os_2 ],
    ["VM26-APP-OSX-FIN",    "#{net_ip}.13",    "1024",    os_2 ],
    ["VM27-REP-OSX-FIN",    "#{net_ip}.14",    "1024",    os_2 ],
    ["VM29-RP-OSX-NEA",    "#{net_ip}.15",    "1024",    os_2 ],
    ["VM29-RP-OSX-AC",    "#{net_ip}.16",    "1024",    os_2 ],
    ["VM31-DB-OSX-FDA",    "#{net_ip}.17",    "1024",    os_2 ],
    ["VM99-CLI-OSX-PORTAL",    "#{net_ip}.18",    "1024",    os_3 ],
  ].each do |vmname,ip,mem,os|
    config.vm.define "#{vmname}" do |minion_config|
      minion_config.vm.provider "virtualbox" do |vb|
          vb.memory = "#{mem}"
          vb.cpus = 1
          vb.name = "#{vmname}"
          vb.gui = false #headless startup?
      end

      minion_config.vm.box = "#{os}"
      minion_config.vm.hostname = "#{vmname}"
      minion_config.vm.network "private_network", ip: "#{ip}"

      minion_config.vm.provision :salt do |salt|
        salt.minion_config = "saltstack/etc/#{vmname}"
        salt.minion_key = "saltstack/keys/#{vmname}.pem"
        salt.minion_pub = "saltstack/keys/#{vmname}.pub"
        salt.install_type = "stable"
        salt.verbose = true
        salt.colorize = true
        salt.bootstrap_options = "-P -c /tmp -x python3"
      end
    end
  end
end
