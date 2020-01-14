# Windows 10 installation on an external drive using a Mac

To test the deployment of this VM containing a Dockerised GigaDB and database
tools on Windows 10, a copy of this operating system was installed on an 
external USB drive. The following steps used to do this were derived from this
[procedure](https://www.youtube.com/watch?v=kR28kVtZYJY).

## Steps

### Download Windows 10

The ISO image for Windows 10 can be downloaded from 
[here](https://www.microsoft.com/en-hk/software-download/windows10ISO).

### Download WinToUSB

`WinToUSB` is a tool for installing Windows on a USB stick. Version 5.1 
Professional was used and can be downloaded from 
[here](https://www.easyuefi.com/wintousb/).

### Download Windows Support Software

Use Boot Camp Assistant to download the 
[Windows Support Software](https://support.apple.com/en-hk/HT204923).

### Connect and erase external drive

Your external drive should now be connected to your Mac computer. Then use Disk
Utility to erase the drive with the following settings:
* Name: Boot Camp
* Format: Mac OS Extended (journaled)
* Scheme: GUID Partition Map

### Create Windows 10 virtual machine

Download and install [VMWare Fusion](https://www.vmware.com/products/fusion.html)
(free for use for 30 days). Use this tool to create a Windows 10 virtual machine 
(VM). VirtualBox cannot be used since tests found problems running WinToUSB on 
VirtualBox VMs.

### Re-format external drive

Re-connect the USB external drive so that it is recognised by the Windows 10 VM.
Use `Disk Management` to created a larger unallocated space to NTFS partition
named `Boot Camp`.

### Copy software into Windows 10 VM

Copy over the `WinToUSB` and `Windows Support` software into the Window 10 VM.

### Install Windows 10 on external drive

Install `WinToUSB` on the Windows 10 VM. Use it to install Windows 10 on the 
external USB drive. This installation process will take some time. Copy over
`Windows Support` directory into the drive.

### Boot up Windows 10 from external drive

Shut down the Windows 10 VM. Start your Mac computer whilst holding down the
`Option` key. Select the external drive as the start up disk. The Windows 10
installed on your external drive will boot up. 

### Install Windows Support software

Use the Windows Support software to install the Boot Camp drivers to complete 
installation of Windows 10 on the external drive.
