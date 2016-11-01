# Deployment Instructions

## Install VirtualBox

Follow instructions [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

## Install BOSH CLI

```
$ gem install bosh_cli --no-ri --no-rdoc
```

## Install Vagrant

Follow instructions [Vagrant](https://www.vagrantup.com/downloads.html)

## Create a workspace directory

```
$ cd
$ mkdir workspace
$ cd workspace
```

## Clone BOSH Lite

```
$ git clone https://github.com/cloudfoundry/bosh-lite
$ cd bosh-lite
```

## Modify Vagranfile

```
config.vm.provider :virtualbox do |v, override|
  override.vm.box_version = '9000.131.0' # ci:replace
  # To use a different IP address for the bosh-lite director, uncomment this line:
  # override.vm.network :private_network, ip: '192.168.59.4', id: :local
  v.name = "pivotal-interview-bosh-release-box" # naming the box
  v.memory = 2048 # setting the system base memory to 2048MB
  v.customize ["modifyvm", :id, "--vram", 16] # setting the video memory to 16MB
  v.cpus = 1 # setting the number of CPUs to 1
end
```
## Spin up a new box

```
$ vagrant up --provider=virtualbox
$ export no_proxy=xip.io,192.168.50.4
$ bosh target 192.168.50.4 lite
$ bin/add-route
$ vagrant ssh
vagrant@agent-id-bosh-0:~$ sudo chown -cR vcap:vcap /vagrant/
vagrant@agent-id-bosh-0:~$ exit
```

## Clone this repository

```
$ cd ..
$ git clone https://github.com/chiswicked/interview
$ cd interview
```

## Deploy a stemcell

Download latest [BOSH Lite Warden stemcell](https://bosh.io/stemcells)

```
$ bosh upload stemcell ~/Downloads/bosh-stemcell-3262.2-warden-boshlite-ubuntu-trusty-go_agent.tgz
```

## Deploy BOSH release

```
$ bosh deployment deployments/warden.yml
$ bosh create release
$ bosh upload release
$ bosh deploy
```

# Verify deployment

```
$ cd ../bosh-lite
$ vagrant ssh
$ bosh vms
$ ssh vcap@10.254.0.2
vcap@a9f48d90-613b-4e20-bb4e-1eee0b90d7d1:~$ tail -f /var/vcap/sys/log/timer/*
```
