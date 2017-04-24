# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box     = 'azure'
  config.vm.box_url = 'https://github.com/msopentech/vagrant-azure/raw/master/dummy.box'

  config.ssh.username         = 'vagrant'
  config.ssh.private_key_path = File.expand_path('./keys/azurevagrant.key')

  config.vm.provider :azure do |azure|
    azure.mgmt_certificate = File.expand_path('./keys/azuremgmtvagrant.pem')
    azure.mgmt_endpoint    = 'https://management.core.windows.net'
    azure.subscription_id  = '48cf3405-5bf9-4b8f-a575-dadd53327561'

    azure.cloud_service_name = 'tmrdockerapp'
    azure.storage_acct_name  = 'tmrazurevagrantstorage'
    azure.deployment_name    = 'tmrazurevagrantdeployment'

    azure.vm_name     = 'azurevagrantsmall'
    azure.vm_image    = '5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-73-20161221'
    azure.vm_size     = 'Small'
    azure.vm_location = 'UK South'

    azure.ssh_port             = '22'
    azure.ssh_private_key_file = File.expand_path('./keys/azurevagrant.key')
#    azure.ssh_certificate_file = File.expand_path('azurevagrant.cer')

    azure.tcp_endpoints = '8000'
  end

  config.vm.provision 'shell', name: 'TEST' , inline: 'echo OHAI'
  config.vm.provision 'shell', name: 'PROVISION DOCKER ENGINE' , inline: 'sudo yum -y install docker'
  config.vm.provision 'shell', name: 'START DOCKER DAEMON' , inline: 'sudo systemctl start docker'
  if ENV['DOCKER_USER'].nil?
    puts 'DOCKER_USER environment variable is not defined!'
    exit 1
  end
  if ENV['DOCKER_PASSWORD'].nil?
    puts 'DOCKER_PASSWORD environment variable is not defined!'
    exit 1
  end
  if ENV['DOCKER_REGISTRY'].nil?
    puts 'DOCKER_REGISTRY environment variable is not defined!'
    exit 1
  end 

  config.vm.provision "shell" do |s|
    s.name = "LOGIN TO PRIVATE DOCKER REGISTRY"
    s.inline = "docker login -u ${DOCKER_USER} -p ${DOCKER_PASSWORD} $DOCKER_REGISTRY"
    s.env = {"DOCKER_USER" => ENV['DOCKER_USER'], "DOCKER_PASSWORD" => ENV['DOCKER_PASSWORD'], "DOCKER_REGISTRY" => ENV['DOCKER_REGISTRY']}
  end
#

  config.vm.provision "docker" do |d|
    d.pull_images ENV['DOCKER_REGISTRY']+"/tmr-nodejs"
    d.pull_images ENV['DOCKER_REGISTRY']+"/mongo"
  end
  config.vm.provision "docker" do |d|
    d.run "db", image: ENV['DOCKER_REGISTRY']+"/mongo"
    d.run "web", image: ENV['DOCKER_REGISTRY']+"/tmr-nodejs", args: "-p 8000:8000"
  end
end
