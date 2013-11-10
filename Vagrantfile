# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

vconfig = YAML::load_file("./Vagrantconfig.yml")

Vagrant.configure("2") do |config|
    # base box configuration
    config.vm.box = "ubuntu-precise12042-x64-vbox43"
    config.vm.box_url = "https://puphpet.s3.amazonaws.com/ubuntu-precise12042-x64-vbox43.box"

    # configure network
    config.vm.network "private_network", ip: "10.0.11.15"

    # port forwarding
    config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true

    # synced folders
    config.vm.synced_folder ".", "/vagrant", nfs: vconfig['nfs']

    config.vm.provider :virtualbox do |virtualbox|
        virtualbox.customize [
            # You can use any VBoxManage command here
            "modifyvm", :id, # tells Virtualbox what VM is to be customized
            "--natdnshostresolver1", "on", # fix for network issues in some OS
            "--memory", vconfig['memory'] # changes de memory
        ]
    end

    config.vm.provider :aws do |aws, override|
        aws.access_key_id = vconfig['aws']['access_key_id']
        aws.secret_access_key = vconfig['aws']['secret_access_key']
        aws.keypair_name = vconfig['aws']['keypair_name']
        aws.region = vconfig['aws']['region']
        aws.security_groups = vconfig['aws']['security_groups']
        aws.instance_type = vconfig['aws']['instance_type']

        aws.ami = vconfig['aws']['ami']

        override.ssh.username = vconfig['aws']['ssh_username']
        override.ssh.private_key_path = vconfig['aws']['ssh_private_key_path']
    end

    # Provision - puppet configuration
    config.vm.provision :puppet do |puppet|
        puppet.module_path = "vagrant/puppet/modules"
        puppet.manifests_path = "vagrant/puppet/manifests"
        puppet.manifest_file  = "main.pp"
        puppet.options = [
            '--verbose',
            #'--debug',
        ]
    end
end
