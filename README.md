# gigadb-misc-tools

## Preparation

This repository contains code that will auto-deploy the GigaDB website and 
Jesse'sdatabase tools on a virtual machine (VM) using 
[VirtualBox](https://www.virtualbox.org), [Vagrant](https://www.vagrantup.com) 
and [Ansible](https://www.ansible.com).

Vagrant is a command line utility for creating VMs. You need to download and
install Vagrant using the appropriate installer or package for your platform 
which is available from the 
[Vagrant download page](https://www.vagrantup.com/downloads.html).

The virtual machine hosting GigaDB itself is created using 
[VirtualBox](https://www.virtualbox.org). Download and install the appropriate 
version of [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
for your platform.

[Ansible](https://www.ansible.com) is software that allows you to automate the 
installation of software packages onto computers. It is used to install a 
Dockerised version of GigaDB, and Jesse's database tools and its software 
dependencies on the VM. Ansible is automatically installed onto the VM during 
its creation but is also needs to be manually installed on your computer to 
download code to that is used to install software packages to install GigaDB on 
the VM.

## Command-line access

We need access to the command-line console in order to use Vagrant which is a
command-line tool.

### MacOSX

On Apple computers, the `Terminal` app can be used to access the command-line. 
`Terminal` is in the Utilities folder in Applications. To open it, open your 
Applications folder, then open Utilities and double-click on Terminal.

### Windows

For Windows, try installing [Babun](http://babun.github.io) which provides a 
Linux-like console on this OS platform.

## Downloading gigadb-misc-tools

This `gigadb-misc-tools` code repository is available from 
[GitHub](https://github.com/pli888/gigadb-misc-tools). The `git` tool can be 
used to do this.

On MacOSX, `git` can be installed using `Xcode Command Line Tools`. For 
Mavericks (10.9) or above, you can do this by trying to run `git` from the 
`Terminal` the very first time. If you don’t have it installed already, it will 
prompt you to install the xcode tools.

On Windows platforms. `Babun` will provide `git` as well as other development 
tools.

After `git` has been installed, use it to download the source code repo from 
Github. Decide on a directory for where to save this code and execute the 
following command below:

```bash
$ git clone https://github.com/pli888/gigadb-misc-tools.git
Cloning into 'gigadb-misc-tools'...
remote: Counting objects: 1657, done.
remote: Compressing objects: 100% (68/68), done.
remote: Total 1657 (delta 25), reused 0 (delta 0), pack-reused 1581
Receiving objects: 100% (1657/1657), 2.33 MiB | 785.00 KiB/s, done.
Resolving deltas: 100% (516/516), done.
Checking connectivity... done.
```

## Ansible installation

### MacOSX

Ansible can be installed using a package manager for MacOSX called 
[Macports](https://www.macports.org).

Download the DMG image for MacPorts from [here](https://www.macports.org/install.php).
Be sure to pick the correct one for your MacOS X version. Mount the disk image 
and run the installer.

#### Install Python, pip and Ansible

Once `Macports` is installed, open a `Terminal` to execute the following command
to install Python:
```
sudo port install python38
```
Make python 3.8 the default, i.e. the version you get when you run `python`:
```
sudo port select --set python python38
```

`pip` is a package manager for installing Python software packages such as 
Ansible. Install `pip` as follows:
```
sudo port install py38-pip
sudo port select --set pip pip38
```

Finally, install Ansible:
```
$ pip install ansible
# Check ansible is installed
$ which ansible
ansible is /opt/local/bin/ansible
```

### Windows

Try these instructions for installing Ansible on Windows at 
http://iambelmin.com/2016/01/01/installing-ansible-on-babun/.


## Access to password-protected GigaDB variables for Ansible

There is a number of sensitive values which are used to parameterise various
variables that GigaDB needs to perform its functionality. These values are 
retrieved from GitLab during the GigaDB installation process with a key token 
which itself is password-protected. A file containing this password needs to be 
created in the `gigadb-misc-tools` directory. Contact 
`peter@gigasciencejournal.com` for this password which then should be saved into 
a file called `vault_passwd.txt` as follows:
```
# Change directory into the gigadb-misc-tools code repo folder
$ cd gigadb-misc-tools
$ echo "the_password" > ".vault_passwd.txt"
```

## Roles installation

An Ansible role is a set of source code files which can be used to install a 
software package. Extra roles need to be downloaded from 
https://galaxy.ansible.com and this can be done as follows in a command-line 
console:
```
# Download roles from ansible galaxy
$ ansible-galaxy install -r provision/roles/requirements.yml
```

## Install GigaDB and Jesse's database tools

We are now in a position to create a VM running GigaDB with Jesse's database
tools installed ready for use:
```
# Make sure you are in the gigadb-misc-tools directory
$ pwd
/path/to/gigadb-misc-tools
# Boot up VM
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'peru/ubuntu-18.04-server-amd64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'peru/ubuntu-18.04-server-amd64' version '20191202.01' is up to date...
==> default: Setting the name of the VM: gigadb.test
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 80 (guest) => 8086 (host) (adapter 1)
    default: 9170 (guest) => 9171 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
```

You now need to wait `15-20 minutes` whilst a VM running Linux Ubuntu version 
18.04 boots up and the process to install various software packages, GigaDB and 
Jesse's database tools is executed. Waiting times might be even longer depending 
on how fast your network connection is for downloading packages. You can follow 
the progress of the installation by monitoring output messages in the console:
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
Two log files, `config.log` and `webapp.log` will be created in the 
`/path/to/gigadb-misc-tools` directory. You can browse the contents of these two 
log files to continue monitoring the installation of GigaDB:
```
# Check latest messages in config.log
$ tail config.log
Current working directory: /var/www
An .env file is present, sourcing it
Running /var/www/ops/scripts/generate_config.sh for environment: dev
Sourcing secrets
Retrieving private_key variable for Google API from https://gitlab.com/api/v4/projects/****%2F****%2Fpli888-gigadb-website/variables
* ---------------------------------------------- *
done.

# Check webapp.log
$ tail webapp.log 
Selecting previously unselected package libicu52:amd64.
(Reading database ... 13685 files and directories currently installed.)
Preparing to unpack .../libicu52_52.1-8+deb8u7_amd64.deb ...
Unpacking libicu52:amd64 (52.1-8+deb8u7) ...

```

## Accessing GigaDB

If GigaDB is successfully installed then you will see this message in your 
console:
```
PLAY RECAP *********************************************************************
default                    : ok=61   changed=20   unreachable=0    failed=0    skipped=27   rescued=0    ignored=1  
```

The GigaDB website running inside your VM can also be viewed on a browser at: 
http://gigadb.gigasciencejournal.com:9171.

## Running Jesse's database tools

Jesse's database tools are Java programs which need to be executed using the
command line. Let's try using the Java tool to create a new dataset entry in 
GigaDB. In a Terminal console:
```
# Log into the GigaDB VM
$ vagrant ssh
# Change directory to where the create dataset tool is located
$ cd /home/vagrant/ExceltoGigaDB
```

In this directory, there is a `run.sh` bash script which is used to compile and 
run the Java code. The compiled code is saved into the `bin` directory. 

This `ExceltoGigaDB` Java tool will parse an Excel `.xls` file containing information
about a new GigaDB dataset and then executes a series of SQL commands to 
transfer dataset information from the Excel spreadsheet into GigaDB's PostgreSQL 
database. There is an example Excel file in the `uploadDir` directory:
```
$ ls uploadDir
100679newversion.xls
```

You can use this example Excel file to test the `ExceltoGigaDB` Java tool:
```
$ ./run.sh
```

There are a number of log files to check the `ExceltoGigaDB` tool for errors.
Try running the commands below to look at the contents of these files:
```
$ ls *log
create_db_error.log  create_db.log  javac.log  java.log
# Use more command to look in the files, for example
$ more java.log
**Begin: file 1 : 100679newversion.xls in process...
line:     itemname character varying(64) NOT NULL,
"AuthAssignment"
line:     userid character varying(64) NOT NULL,
"AuthAssignment"
line:     bizrule text,
"AuthAssignment"

```

To test new Excel files representing new GigaDB datasets then they will need to
be placed into this `uploadDir` folder:
```
# Change directory
$ cd /home/vagrant/ExceltoGigaDB/uploadDir
# Delete example Excel file
$ rm uploadDir/100679newversion.xls
# Place your Excel file into the gigadb-misc-tools directory on your host computer
# Move this Excel file into uploadDir
$ mv path/to/file.xls /home/vagrant/ExceltoGigaDB/uploadDir
``` 

Now run the `ExceltoGigaDB` tool using `./run.sh`. Checking the log files will
allow you to look for errors in the Excel file.