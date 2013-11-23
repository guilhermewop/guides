Mount Ext4 on a Mac OS
======================

```
vagrant init precise32
```


### Vagrantfile

```
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.network "private_network", ip: "192.168.50.4"
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "512"]
  end
end
```


### VMDK

```
disk=disk9

sudo chmod go+rw /dev/$disk
VBoxManage internalcommands createrawvmdk -filename $disk.vmdk -rawdisk /dev/$disk
```

Open VirtualBox

Virtual machine > Settings > Storage > Controller: SATA Controller > Add Hard Disk > Choose existing disk > $disk.vmdk


### Mounting disk

```
vagrant ssh
```

```
sudo mkdir /mnt/sdb1
sudo mount /dev/sdb1 /mnt/sdb1
sudo chown -R vagrant.vagrant /mnt/sdb1
```

### Installing Samba

```
sudo apt-get update
sudo apt-get install samba
```

```
sudo vi /etc/samba/smb.conf
```

```
[global]
  workgroup = WORKGROUP
  server string = SambaServer
  security = share
  name resolve order = hosts lmhosts

[media]
  path = /mnt/sdb1
  force user = vagrant
  force group = vagrant
  read only = no
  guest ok = yes
```

```
sudo restart smbd
sudo restart nmbd
```


### Connect to share

Finder > Go > Connect to Server...

  smb://192.168.50.4/media
  Guest