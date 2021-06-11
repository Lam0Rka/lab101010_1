```sh
$ apt -S vagrant
```
Смотрим на версию вагранта
```sh
$ vagrant version
    Vagrant 2.2.15
Инициализация виртуальной машины
$ vagrant init bento/ubuntu-19.10
	A `Vagrantfile` has been placed in this directory. You are now
	ready to `vagrant up` your first virtual environment! Please read
	the comments in the Vagrantfile as well as documentation on
	`vagrantup.com` for more information on using Vagrant.
редактируем вагрант файл
$ vim Vagrantfile
$ vagrant init -f -m bento/ubuntu-19.10
```

```sh
$ mkdir shared
```
записываем в вагрант файл следющий текст (добавление конфигурации)
```sh
$ cat > Vagrantfile <<EOF
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
```

```sh
$ cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
```

```sh
$ cat >> Vagrantfile <<EOF

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: \$script, privileged: true

  config.ssh.extra_args = "-tt"

end
EOF
```
проверяем коректность файла вагрант
```sh
$ vagrant validate
    Installing the 'vagrant-vbguest' plugin. This can take a few minutes...
    ...
запускаем вагрант с помощью виртуаль бокса
$ vagrant status
$ sudo apt -S virtualbox
$ sudo vagrant up --provider virtualbox
	Bringing machine 'default' up with 'virtualbox' provider...
	==> default: Box 'bento/ubuntu-19.10' could not be found. Attempting to find and install...
	    default: Box Provider: virtualbox
        ...
Просмотр порта
$ vagrant port
    22 (guest) => 2222 (host)

$ vagrant status
	Current machine states:
	
	default                   running (virtualbox)
подключаемся к виртуальной машина
$ vagrant ssh
	  System information as of Thu 06 May 2021 05:59:28 PM UTC
	
	  System load:  0.02              Processes:           110
	  Usage of /:   2.6% of 61.80GB   Users logged in:     0
	  Memory usage: 6%                IP address for eth0: 10.0.2.15
	  Swap usage:   0%
      ...
      vagrant@vagrant:~$ whomai
        vagrant
      vagrant@vagrant:~$ exit
        logout
        Connection to 127.0.0.1 closed.
Просмотр снимков виртуальной машины
$ vagrant snapshot list
    ==> default: No snapshots have been taken yet!
Добавление снимков
$ vagrant snapshot push
	==> default: Snapshotting the machine as 'push_1620324147_4207'...
	==> default: Snapshot saved! You can restore the snapshot at any time by
	==> default: using `vagrant snapshot restore`. You can delete it using
	==> default: `vagrant snapshot delete`.

$ vagrant snapshot list
	==> default: 
	push_1620324147_4207
Выключаем виртуальную машину
$ vagrant halt
    ==> default: Attempting graceful shutdown of VM...

$ vagrant snapshot pop
	==> default: Restoring the snapshot 'push_1620324147_4207'...
	==> default: Deleting the snapshot 'push_1620324147_4207'...
	==> default: Snapshot deleted!
	==> default: Checking if box 'bento/ubuntu-19.10' version '202008.16.0' is up to date...
	==> default: Resuming suspended VM...
	==> default: Booting VM...
	==> default: Waiting for machine to boot. This may take a few minutes...
	    default: SSH address: 127.0.0.1:2222
        ...
```

```ruby
  config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
```
устанавливаем плагин Vmware и просматриваем эти плагины
```sh
$ vagrant plugin install vagrant-vmware-esxi
    Installing the 'vagrant-vmware-esxi' plugin. This can take a few minutes...
    ...
    Installed the plugin 'vagrant-vmware-esxi (2.5.2)'!

$ vagrant plugin list
    vagrant-vmware-esxi (2.5.2, global)
 
$ vagrant up --provider=vmware_esxi
	Bringing machine 'default' up with 'vmware_esxi' provider...
    ...
```

