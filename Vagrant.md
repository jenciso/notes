## Add Second disk

Step 1: Install the vagrant-persistent-storage
```
vagrant plugin install vagrant-persistent-storage
```

Step 2: Modify your Vagrant file
```
second_disk = './second_disk_file.vdi'

  config.vm.provider "virtualbox" do |vb|
    unless File.exist?(second_disk)
     vb.customize ['createhd', '--filename', second_disk, '--size', 500 * 1024]
    end
     vb.customize ['storageattach', :id, '--storagectl', 'IDE Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', second_disk]
  end
```
Step 3: vagrant up 


