Vagrant
=======

Download and install
--------------------

Download and install VirtualBox:

    https://www.virtualbox.org/wiki/Downloads

Download and install Vagrant:

    http://downloads.vagrantup.com

Download and install base box:

    vagrant box add precise32 http://files.vagrantup.com/precise32.box
    vagrant init precise32
    vagrant up


Vagrantfile basic configuration
-------------------------------

```ruby
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise32"
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "512"]
  end
end
```


Updating guest additions to match VirtualBox version
----------------------------------------------------

    vagrant ssh
    wget http://download.virtualbox.org/virtualbox/4.3.2/VBoxGuestAdditions_4.3.2.iso
    sudo mount -r VBoxGuestAdditions_4.3.2.iso -o loop /mnt

    sudo apt-get install build-essential linux-headers-$(uname -r)

    cd /mnt
    sudo sh VBoxLinuxAdditions.run --nox11

    exit
    vagrant reload


Recompiling guest additions to match kernel version
---------------------------------------------------

    vagrant ssh

    sudo apt-get install build-essential linux-headers-$(uname -r)
    sudo /etc/init.d/vboxadd setup

    exit 
    vagrant reload


Using Chef and cookbooks
------------------------

Install Chef:

    curl -L https://www.opscode.com/chef/install.sh | sudo bash

Download cookbooks:

    mkdir -p cookbooks
    cd cookbooks

    echo "apt mysql mysqld apache2 php" | xargs -n 1 knife cookbook site download
    echo *.tar.gz | xargs -n 1 tar xzf

    cd ..

Configure Vagrantfile:

```ruby
config.vm.provision :chef_solo do |chef|
  chef.cookbooks_path = "cookbooks"
  chef.add_recipe "apt"
  chef.add_recipe "mysql"
  chef.add_recipe "mysqld"
  chef.add_recipe "apache2"
  chef.add_recipe "php"
end
```

Provision:

    vagrant provision
