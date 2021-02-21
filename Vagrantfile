$mach_quant = 2

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|
      vb.gui=false
      vb.memory=1024
      vb.cpus=2
      vb.check_guest_additions=false
  config.vm.box_check_update=false
  config.vm.box="ubuntu/focal64"
 end

  (1..$mach_quant).each do |i|
      config.vm.define "node#{i}" do |node|
          node.vm.network "public_network", ip: "192.168.88.#{24+i}", bridge: "Realtek PCIe GbE Family Controller"
          node.vm.network "private_network", ip: "12.12.12.#{0+i}", virtualbox__intnet: "intnet"
          node.vm.hostname = "node#{i}"
      end
  end

end
