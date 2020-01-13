# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Use clean minimal latest ubuntu server amd64 base box
  config.vm.box = "peru/ubuntu-18.04-server-amd64"
  # Give host access to web server on VM
  config.vm.network "forwarded_port", guest: 80, host: 8086
  config.vm.network "forwarded_port", guest: 9170, host: 9171
  config.vm.network "private_network", type: "dhcp"
  # Allow guest VM to access this repo's files
  if Vagrant::Util::Platform.windows? then
    # Enable one-time sync of repo files on a windows 10 host
    # to ubuntu guest VM on vagrant up
    config.vm.synced_folder ".", "/vagrant/", type: "rsync"
  else
    config.vm.synced_folder ".", "/vagrant"
  end

  config.vm.provider :virtualbox do |vb|
    vb.name = "gigadb.test"
    vb.memory = 2048
    vb.cpus = 2
  end

  # Use Ansible to provision dependencies
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "provision/gigadb.yml"
    ansible.verbose = true
    ansible.vault_password_file = ".vault_passwd.txt"
    #ansible.inventory_path = "inventory"
    ansible.extra_vars = {
      docker: {
        docker_users: "vagrant"
      },
      gigadb: {
        repo: "https://github.com/pli888/gigadb-website.git",
        repo_branch: "new-func-tests-fix-composer",
        home_dir: "/home/vagrant",
        prod_sql_zip_file: "gigadbv3_20191030.sql.zip"
      },
      excel2gigadb: {
        repo: "https://github.com/pli888/ExceltoGigaDB.git"
      }
    }
  end
end