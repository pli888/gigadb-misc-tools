# GigaDB VM deployment in Windows 10

The following procedure can be used to deploy the GigaDB VM on Windows 10.

## Steps

### Git

Download and install the [git](https://git-scm.com/downloads) tool.

### VirtualBox

Download and install VirtualBox 6.0.1 for Windows.

### Vagrant

Download and install [Vagrant](https://www.vagrantup.com/downloads.html) 64-bit 
version 2.2.6 for Windows. This 2.2.6 version works with VirtualBox 6.0.1.


### Cygwin

Cygwin provides a Linux environment for Windows. Install Cygwin using the
[cygwin-portable-installer](https://vegardit.github.io/cygwin-portable-installer/)
which is a Windows batch file that also automates the install of Ansible which 
is used in the GigaDB VM provisioning process later on.

Open a Command Prompt windows to download the cygwin-portable-installer code:
``` 
$ git clone https://github.com/vegardit/cygwin-portable-installer --single-branch --branch master --depth 1 C:\apps\cygwin-portable
```

Run the `cygwin-portable-installer.cmd` by double-clicking it in Windows 
Explorer.

### Configure PATH

We need to let the Cygwin environment know where the software is in the file
system so that it can find it. This is the done by updating the 
[$PATH variable](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/).

The paths to Vagrant, VirtualBox and the Cygwin executables need to be added to 
the PATH variable:
```
C:\HashiCorp\Vagrant\bin
C:\Program Files\Oracle\VirtualBox
C:\apps\cygwin-portable\bin
```

### Download gigadb-misc-tools

We can now open a [`Command Prompt`](https://www.lifewire.com/command-prompt-2625840) 
window and use it to obtain the code for GigaDB VM deployment with the following 
command:
```
$ git clone https://github.com/pli888/gigadb-misc-tools.git
```

Switch to the repository branch containing the latest code:
```
# Change directory to the repo
$ cd gigadb-misc-tools
# Switch branch
$ git checkout create-vm
```

### Access to password-protected GigaDB variables for Ansible

There is a number of sensitive values which are used to parameterise various
variables that GigaDB needs to perform its functionality. These values are 
retrieved from GitLab during the GigaDB installation process with a key token 
which itself is password-protected. A file containing this password needs to be 
created in the `gigadb-misc-tools` directory. Contact `peter@gigasciencejournal.com` 
for this password which then should be saved into a file called `vault_passwd.txt` 
as follows:
```
# Create password file
$ echo 'the_password' > '.vault_passwd.txt'
```

### Roles installation

An Ansible role is a set of source code files which can be used to install a 
software package. Extra roles need to be downloaded from https://galaxy.ansible.com 
and this can be done as follows in a command-line console:
```
# Download roles from ansible galaxy
$ ansible-galaxy install -r provision/roles/requirements.yml
```

### Deploy GigaDB and Jesse's database tools

We are now in a position to create a VM running GigaDB with Jesse's database
tools installed ready for use:
```
# Boot up VM
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
```

A VirtualBox window will appear on your desktop showing the Ubuntu boot screen 
and a console login. The provisioning process will start to deploy GigaDB and 
install Jesse's database tools in the VM. The process can take 30 mins or more 
depending on your network speed.

You can follow the progress of the installation by monitoring output messages in 
the command prompt window:
```
TASK [gigadb : Clone gigadb-website repo] **************************************
ok: [default] => {"after": "564e669f7f680b9d7800040ac10e999768982c5c", "before": "564e669f7f680b9d7800040ac10e999768982c5c", "changed": false, "remote_url_changed": false}

TASK [gigadb : Create /home/vagrant/.env file] *********************************
changed: [default] => {"changed": true, "checksum": "6835dfef1d4c6b1578853e4775a2797b6a3b6d5b", "dest": "/home/vagrant/gigadb-website/.env", "gid": 1000, "group": "vagrant", "md5sum": "eec4ff81addf13eaee7e3eff84732e83", "mode": "0644", "owner": "vagrant", "size": 3103, "src": "/home/vagrant/.ansible/tmp/ansible-tmp-1576736467.46-253135704061037/source", "state": "file", "uid": 1000}

TASK [gigadb : Add Gitlab private token into .env file] ************************
changed: [default] => {"backup": "", "changed": true, "msg": "line replaced"}
```

The vagrant provisioning process is long and complex, and likely to fail whilst 
this GigaDB installation code is being tested. If it does fail, try restarting 
the installation process:
```
$ vagrant provision
```

When the process reaches the installation of GigaDB stage, you should see this
message in your console:
```
TASK [gigadb : Run composer update, then spin up the web application's services, then exit] ***
```

To continue monitoring the deployment, log into the Ubuntu VM with a new
`Command Prompt` window and check two log files, `config.log` and `webapp.log` 
which are created in the `/vagrant/gigadb-misc-tools` directory:
```
# Log into VM
$ vagrant ssh
# Check latest messages in config.log
$ tail /vagrant/gigadb-misc-tools/config.log
Current working directory: /var/www
An .env file is present, sourcing it
Running /var/www/ops/scripts/generate_config.sh for environment: dev
Sourcing secrets
Retrieving private_key variable for Google API from https://gitlab.com/api/v4/projects/****%2F****%2Fpli888-gigadb-website/variables
* ---------------------------------------------- *
done.

# Check webapp.log
$ tail /vagrant/gigadb-misc-tools/ webapp.log 
Selecting previously unselected package libicu52:amd64.
(Reading database ... 13685 files and directories currently installed.)
Preparing to unpack .../libicu52_52.1-8+deb8u7_amd64.deb ...
Unpacking libicu52:amd64 (52.1-8+deb8u7) ...
```

If all is working, then the end of `webapp.log` should contain the following:
```
Step 41/41 : EXPOSE 9000
 ---> Running in 7e7b55862ff2
Removing intermediate container 7e7b55862ff2
 ---> 38d49b791625

Successfully built 38d49b791625
Successfully tagged deployment_application:latest
```

Various PHP packages are then downloaded into `vendor` directory. We can check 
the progress of their download:
```
$ ls -lh /home/vagrant/gigadb-website/vendor
```

When all the PHP packages are loaded then, after a while, the GigaDB website
should appear at http://gigadb.gigasciencejournal.com:9171 in a Windows 10 web 
browser.

![Image of GigaDB website in Windows 10](https://www.dropbox.com/s/bhjhi879uf3s33j/win10_gigadb.png?raw=1)


The `command prompt` window will end with the output of new messages showing 
that a production database is being loaded into the postgresql database. 

If GigaDB is successfully installed in your VM then you will see this message 
in your `command prompt` window where you executed `vagrant up`:
```
PLAY RECAP *********************************************************************
default                    : ok=61   changed=20   unreachable=0    failed=0    skipped=27   rescued=0  
```

## Shutting down the GigaDB virtual machine

The GigaDB VM can be shutdown using the command, `vagrant halt`. `vagrant up`
will re-create the VM again.