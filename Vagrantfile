# -*- mode: ruby -*-
# vi: set ft=ruby :
Encoding.default_external = 'UTF-8'

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-16.04"

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y linux-image-extra-$(uname -r) linux-image-extra-virtual
    apt-get install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    apt-key fingerprint 0EBFCD88
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    apt-get update
    apt-get install -y docker-ce
  SHELL

  config.vm.define "rancher-server" do |rancher|
    rancher.vm.network "private_network", ip: "192.168.90.10"
    rancher.vm.hostname = "rancher-server"
    rancher.vm.provision "file", source: "./src/rancher", destination: "/tmp/rancher"
    rancher.vm.provision "shell", inline: <<-SHELL
      #!/bin/bash
      set -eo pipefail
      [[ `docker ps | wc -l` -eq 1 ]] && docker run -d -v mysql-data:/var/lib/mysql --restart=unless-stopped --name rancher -p 80:8080 rancher/server:v1.6.12
      [[ ! -f /root/.provisioned ]] && sleep 90 && docker exec -i rancher mysql cattle < /vagrant/src/rancher.sql && touch /root/.provisioned
      [[ `grep RANCHER_ACCESS_KEY /etc/environment | wc -l` -eq 0 ]] && echo "RANCHER_ACCESS_KEY=E337748A1A34D9A25335" >> /etc/environment
      [[ `grep RANCHER_SECRET_KEY /etc/environment | wc -l` -eq 0 ]] && echo "RANCHER_SECRET_KEY=HWjvcrxuMSgpoXvYJLhtdKGyXCEY5j7EKq1FuvYR" >> /etc/environment
      [[ `grep RANCHER_URL /etc/environment | wc -l` -eq 0 ]] && echo "RANCHER_URL=http://192.168.90.10/v2-beta" >> /etc/environment
      [[ -f /tmp/rancher ]] && mv /tmp/rancher /usr/local/bin
      exit 0
    SHELL
  end

  config.vm.define "node1" do |node1|
    node1.vm.network "private_network", ip: "192.168.90.11"
    node1.vm.hostname = "node1"
    node1.vm.provision "shell", inline: <<-SHELL
      #!/bin/bash
      set -eo pipefail
      [[ ! -f /root/.provisioned ]] && docker run --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v1.2.7 http://192.168.90.10/v1/scripts/B6E6AE22FF0BD7596BF1:1483142400000:gAmHRiMQROA1EAuIUMgHC95b2Q && touch /root/.provisioned
      exit 0
    SHELL
  end

  config.vm.define "node2" do |node2|
    node2.vm.network "private_network", ip: "192.168.90.12"
    node2.vm.hostname = "node2"
    node2.vm.provision "shell", inline: <<-SHELL
      #!/bin/bash
      set -eo pipefail
      [[ ! -f /root/.provisioned ]] && docker run --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v1.2.7 http://192.168.90.10/v1/scripts/B6E6AE22FF0BD7596BF1:1483142400000:gAmHRiMQROA1EAuIUMgHC95b2Q && touch /root/.provisioned
      exit 0
    SHELL
  end

end
