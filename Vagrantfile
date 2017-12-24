# vagrant plugin install vagrant-vbguest
# vagrant plugin install vagrant-multi-putty


has_all_plugins=true

["vagrant-vbguest", "vagrant-multi-putty"].each do |plugin_name|
  if !Vagrant.has_plugin?(plugin_name)
    puts "#{plugin_name} is not installed! run 'vagrant plugin install #{plugin_name}'"
    has_all_plugins=false
  end
end

unless has_all_plugins
  raise "One or more required plugin is not installed."
end

$scriptsWithSudo = <<SCRIPT
echo "provisioning..."
whoami
pacman -S ansible
pacman -S sshpass

SCRIPT


Vagrant.configure("2") do |config|
    config.vm.box = "archlinux/archlinux"
    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 1
        v.name = "Homelab controller"
        v.customize ["modifyvm", :id, "--description", __FILE__]
    end
    config.ssh.insert_key = false
    config.vm.synced_folder ".", "/homelab", type: "virtualbox"
    config.vm.network "private_network", :type => 'dhcp'
    config.vm.network "forwarded_port", guest: 80, host: 8080
    config.vm.network "forwarded_port", guest: 443, host: 4430
    config.vm.provision "shell", inline: "sudo rm /etc/localtime && sudo ln -s /usr/share/zoneinfo/Canada/Pacific /etc/localtime", run: "always"
    config.vm.provision "shell", inline: $scriptsWithSudo
    config.vm.post_up_message = "The box is up, but there is nothing running yet.\nRun 'vagrant putty' (windows) or 'vagrant ssh' (linux) to connect to the guest box"
end