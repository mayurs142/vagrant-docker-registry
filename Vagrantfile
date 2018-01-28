# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

NODE_SCRIPT = <<EOF.freeze
echo "Preparing node..."

# Install dependent packages
yum install -y yum-utils device-mapper-persistent-data lvm2 vim bash-completion epel-release
yum install -y python-pip tree telnet

# Setup docker repository
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# Update Yum cache
yum makecache fast

# Install docker
yum install -y docker-ce

# Install docker-compose
pip install docker-compose

# Start & enable docker
systemctl start docker
systemctl enable docker

# Verify docker installation
docker run --rm hello-world

EOF

def set_hostname(server)
  server.vm.provision 'shell', inline: "hostname #{server.vm.hostname}"
end

Vagrant.configure(2) do |config|
  config.vm.define 'docker-registry' do |d|
    d.vm.box = 'bento/centos-7.2'
    d.vm.hostname = 'docker-registry'
    d.vm.network 'private_network', ip: '10.1.1.30'
    d.vm.provision :shell, inline: NODE_SCRIPT.dup
    set_hostname(d)

    d.vm.provider 'virtualbox' do |v|
      v.memory = 4096
      v.cpus = 4
    end
  end
end
