# -*- mode: ruby -*-
# vi: set ft=ruby :

N = 2
Vagrant.configure("2") do |config|
  # always use Vagrants insecure key
  config.ssh.insert_key = false

  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.box = "bento/debian-9.0"
      machine.vm.box_check_update = false

      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.33.#{10+machine_id}"
      machine.vm.synced_folder ".", "/vagrant", disabled: true

      machine.vm.provider "virtualbox" do |vb|
        # Display the VirtualBox GUI when booting the machine
        vb.gui = false

        # Customize the amount of memory on the VM:
        vb.memory = "128"

        vb.check_guest_additions = false
        vb.functional_vboxsf     = false
      end

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          require 'json'
          fact_or_var = [{
            "id" => 0,
            "name" => "name",
          }]

          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.playbook = "provisioning/playbook.yml"

          ansible.host_vars = {
            "machine1" => { "fact_or_var" => "'#{fact_or_var.to_json}'" }
          }

          ansible.groups = {
            "fact_servers" => ["machine1"],
            "var_servers" => ["machine2"],
          }
        end
      end
    end
  end
end
