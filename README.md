# gigadb-misc-tools

This repository contains code that will auto-deploy the GigaDB website and 
Jesse's database tools on a virtual machine (VM) using 
[VirtualBox](https://www.virtualbox.org), [Vagrant](https://www.vagrantup.com) 
and [Ansible](https://www.ansible.com).

## Deployment on OS X

The following information describes how GigaDB website and Jesse's database 
tools can be installed on **Sierra OSX 10.12**.

### Install VirtualBox

The VM hosting GigaDB is created using [VirtualBox](https://www.virtualbox.org). 
Download the `6.0.14` version of VirtualBox for your platform from [here](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)
and follow the instructions provided by the VirtualBox installer.

### Install Vagrant

Vagrant is a command line utility which can be used for creating VirtualBox VMs.
Download Vagrant version `2.2.6` available at https://releases.hashicorp.com/vagrant/. 
Open the `dmg` file and double-click the `vagrant.pkg` file and follow the 
instructions in the installer for complete installation.

### Install Ansible

[Ansible](https://www.ansible.com) is software that allows you to automate the 
installation of software packages into computers. It is used to install a 
Dockerised version of GigaDB, and Jesse's database tools and its software 
dependencies within the VM. Ansible is automatically installed onto the VM during 
its creation but it also needs to be manually installed on your computer to 
download supplementary Ansible code not included in the `gigadb-misc-tools` 
repo.

A local copy of Ansible will be installed using the [Macports](https://www.macports.org) 
package manager for OSX:

### Install Macports

#### Check if Macports is installed

We need access to the command-line console to run commands. On Apple computers, 
the `Terminal` app can be used to access the command-line. `Terminal` can be 
found in the `Utilities` folder in `Applications`. To open it, open your 
`Applications` folder, then click `Utilities` and double-click on `Terminal`.

You can check if Macports is already installed on your computer by running in 
the `Terminal`:
```
# Check
$ port list
```

If a list of packages are displayed then Macports is already installed.

#### Install Xcode and Xcode command line tools
 
Macports requires Xcode and Xcode command line tools. You can search for version 
9.2 at https://developer.apple.com/download/more/. Download the 
5.1 GB Xcode_9.2.xip file and the Command Line Tools (macOS 10.12) for 
Xcode 9.2.dmg file.

Double click on Xcode_9.2.xip to unzip the file. You will now see an Xcode 
application file - click this to install Xcode components into Sierra OSX. You 
can close the `Welcome to code` start up window.

Double click on command_line_Tools_macOS_10.12_for_Xcode_9.2.dmg file to open 
it. Then click on the `pkg` file and follow the instructions in Command line 
tools installer.

#### Macports installation

Download the Macports for Sierra OSX installer from https://github.com/macports/macports-base/releases/download/v2.6.2/MacPorts-2.6.2-10.12-Sierra.pkg.
Double click this `pkg` file and follow its installer instructions.

##### Install Git

Git is a version control system that we use in GigaScience for managing source 
code. It is used to download this repo onto your laptop.

If git is already installed on your laptop then this part can be skipped. Test 
if git is installed:
```
$ which git
git is /opt/local/bin/git
```

Use Macports to install git:
```
$ sudo port install git
```

Note that Python 2 will be installed as a dependency of git.

#### Finish installing Ansible

Since a version of Python 2 has been installed by Macports, lets use it as the 
default version of Python to be used:
```
$ sudo port select --set python python27
```

Pip is a package manager for installing Python packages and can be used to 
install Ansible. Install Pip as follows:
```
$ sudo port install py27-pip
$ sudo port select --set pip pip27
```

Finally, install Ansible using the Pip manager:
```
$ sudo pip install ansible
```

The `pip` manager in your `Macports` Python installation installs Ansible but you 
might not be able to use it since it might not be listed on your `PATH `
environment variable:
```
# Check
$ which ansible
ansible is not on PATH
```

To add the location of the `ansible` tool to your PATH, find where ansible is:
```
$ find /opt/local -name ansible
```

This command should show that the ansible executable is in `/opt/local/Library/Frameworks/Python.framework/Versions/2.7/bin`. 
Your PATH variable needs to be updated with the addition of this path. To do 
this, `CONTROL-C` copy the `export` line below:
```
export PATH=/opt/local/Library/Frameworks/Python.framework/Versions/2.7/bin:$PATH
```

Open your `profile` file using the VIM test editor
```
$ vi ~/.profile
```

Position the cursor at the beginning of the first line of the `profile` file. 
Type `i` to enter into `insert` mode and then `CONTROL-V` to paste the text and 
press `return`. Press `ESC` to return to command mode and then type `:wq!` to 
save and exit the VIM text editor.

Refresh the PATH variable for your console using the edited `~/.profile` file:
```
$ source ~/.profile
```

Now check Ansible can be found:
```
$ ansible --version
ansible 2.8.2
  config file = None
  configured module search path = [u'/Users/peterli/.ansible/plugins/modules', u'/opt/local/share/ansible/plugins/modules']
  ansible python module location = /opt/local/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages/ansible
  executable location = /opt/local/bin/ansible
  python version = 2.7.17 (default, Oct 21 2019, 00:44:43) [GCC 4.2.1 Compatible Apple LLVM 9.1.0 (clang-902.0.39.2)]
```

### Download gigadb-misc-tools

This `gigadb-misc-tools` code repository is available from [GitHub](https://github.com/pli888/gigadb-misc-tools). 
The `git` tool can be used to do this.

Decide on a directory for where to save this code and execute the following 
`git clone` command below:

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

### Install GigaDB and Jesse's database tools

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

A VirtualBox window will appear on your desktop showing the Ubuntu boot screen 
and a console login. You might need to provide your `admin` password when the 
Ubuntu VM boot up process is setting up a shared folder between your computer 
and the guest Ubuntu VM.

There can be a wait of `30 minutes or more` whilst a VM running Linux Ubuntu 
version 18.04 boots up and the process to install various software packages, 
GigaDB and Jesse's database tools is executed. Waiting times might be even 
longer depending on how fast your network connection is for downloading 
packages. You can follow the progress of the installation by monitoring output 
messages in the console:
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

Two log files, `config.log` and `webapp.log` will then be created in the `/path/to/gigadb-misc-tools` 
directory. You can browse the contents of these two log files to continue 
monitoring the installation of GigaDB:
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

If all goes well then the last messages you will see in `webapp.log` will be:
```
Step 41/41 : EXPOSE 9000
 ---> Running in 7e7b55862ff2
Removing intermediate container 7e7b55862ff2
 ---> 38d49b791625

Successfully built 38d49b791625
Successfully tagged deployment_application:latest
```

These messages tell you that the `deployment_application:latest` docker 
container has been created. PHP packages required by GigaDB will now be 
downloaded into the `gigadb-website/vendor` folder. This can take a while 
especially if you have a slow network connection but you can follow the 
downloading of these packages by logging into the VM. Open a new `Terminal` 
console window by holding down the COMMAND key and pressing T, and executing the
following commands:
```
# Connect to the VM
$ vagrant ssh
# Go to /home/vagrant/gigadb-website/vendor directory
$ cd /home/vagrant/gigadb-website/vendor
$ ls -lh
total 212K
drwxr-xr-x  3 root root 4.0K Jan  8 01:19 aik099
-rw-r--r--  1 root root  178 Jan  8 01:20 autoload.php
drwxr-xr-x 10 root root 4.0K Jan  8 01:19 behat
drwxr-xr-x  6 root root 4.0K Jan  8 01:12 bower-asset
drwxr-xr-x  3 root root 4.0K Jan  8 01:12 cebe
drwxr-xr-x  4 root root 4.0K Jan  8 01:16 cilex
drwxr-xr-x  4 root root 4.0K Jan  8 01:20 composer
```

This list of PHP packages will be added to until there are:
```
drwxr-xr-x  5 root root 4.0K Jan  8 01:12 yiisoft
drwxr-xr-x 12 root root 4.0K Jan  8 01:15 zendframework
drwxr-xr-x  4 root root 4.0K Jan  8 01:16 zetacomponents
```

## Accessing GigaDB

If GigaDB is successfully installed in your VM then you will see this message in
your console window where you executed `vagrant up`:
```
PLAY RECAP *********************************************************************
default                    : ok=61   changed=20   unreachable=0    failed=0    skipped=27   rescued=0    ignored=1  
```

The GigaDB website running inside your VM can then be viewed on a browser at: 
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