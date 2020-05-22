# Homework Lesson 1 

## Test hardware and software provisioning steps made 
* ### Test machine (Lenovo Notebook e11) 
* ### Installed CentosOS 7 Linux 3.10.0-1127.8.2.el7.x86_64 on baremetal 
* ### Installed git, VSCode, VirtualBox-6.1,Vagrant 2.2.9, Packer 1.4.4

## Steps as per homework description
* ### Forked git from dmitry-lyutenko/manual_kernel_update into my account 
* ### Cloned git  zlovo /manual_kernel_update onto local machine 
* ### Opened directory `manual_kernel_update`

_## next steps description have more detailes ##_
* ### Start up cloned vagrant configuration VM and ssh into it

run `vagrant up` result => no error messages, 

virtual machine is up and running 

ssh to vm `[alexeyv@desktop-i3gm60g manual_kernel_update]$ vagrant ssh`

result => `Last login: Wed May 20 12:10:32 2020 from 10.0.2.2`

this mean we did ssh succesfully 

* ### Check current kernell 

```
[vagrant@kernel-update ~]$ uname -r
3.10.0-1127.el7.x86_64

```
* ### grub update and check if new kernell is up and running 
```
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
sudo grub2-set-default 0
sudo reboot
```
result => new kernell 

lets `vagrant ssh`

result => `Last login: Wed May 20 12:21:36 2020 from 10.0.2.2` 

check new kernell name 

```
[vagrant@kernel-update ~]$ uname -r
5.6.14-1.el7.elrepo.x86_64
```

check new os release 
```
[vagrant@kernel-update ~]$ sudo cat /etc/os-release file
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT
```
pull new os info with hostnamectl
```
[vagrant@kernel-update ~]$ hostnamectl
   Static hostname: kernel-update
         Icon name: computer-vm
           Chassis: vm
        Machine ID: cc811af22277d5419168e5d782665d8b
           Boot ID: 2ac3e414d4754b3784c772a7c500a301
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 5.6.14-1.el7.elrepo.x86_64
      Architecture: x86-64
 ```
 Identify current Linux release
 ```
 vagrant@kernel-update ~]$ cat /proc/version
Linux version 5.6.14-1.el7.elrepo.x86_64 (mockbuild@Build64R7) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC)) #1 SMP Tue May 19 12:17:13 EDT 2020
```

* ### Check packer provision config for Packer
```
{
  "variables": {
    "artifact_description": "CentOS 7.7 with kernel 5.x",
    "artifact_version": "7.7.1908",
    "image_name": "centos-7.7"
  },

  "builders": [
    {
      "name": "{{user `image_name`}}",
      "type": "virtualbox-iso",
      "vm_name": "packer-centos-vm",

      "boot_wait": "10s",
      "disk_size": "10240",
      "guest_os_type": "RedHat_64",
      "http_directory": "http",

      "iso_url": "http://mirror.yandex.ru/centos/7.7.1908/isos/x86_64/CentOS-7-x86_64-Minimal-1908.iso",
      "iso_checksum": "9a2c47d97b9975452f7d582264e9fc16d108ed8252ac6816239a3b58cef5c53d",
      "iso_checksum_type": "sha256",

      "boot_command": [
        "<tab> text ks=http://{{ .HTTPIP }}:{{ .HTTPPort }}/vagrant.ks<enter><wait>"
      ],

      "shutdown_command": "sudo -S /sbin/halt -h -p",
      "shutdown_timeout" : "5m",

      "ssh_wait_timeout": "20m",
      "ssh_username": "vagrant",
      "ssh_password": "vagrant",
      "ssh_port": 22,
      "ssh_pty": true,

      "output_directory": "builds",

      "vboxmanage": [
        [  "modifyvm",  "{{.Name}}",  "--memory",  "1024" ],
        [  "modifyvm",  "{{.Name}}",  "--cpus",  "2" ]
      ],

      "export_opts":
      [
        "--manifest",
        "--vsys", "0",
        "--description", "{{user `artifact_description`}}",
        "--version", "{{user `artifact_version`}}"
      ]

    }
  ],

  "post-processors": [
    {
      "output": "centos-{{user `artifact_version`}}-kernel-5-x86_64-Minimal.box",
      "compression_level": "7",
      "type": "vagrant"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "execute_command": "{{.Vars}} sudo -S -E bash '{{.Path}}'",
      "start_retry_timeout": "1m",
      "expect_disconnect": true,
      "pause_before": "20s",
      "override": {
        "{{user `image_name`}}" : {
          "scripts" :
            [
              "scripts/stage-1-kernel-update.sh",
              "scripts/stage-2-clean.sh"
            ]
        }
      }
    }
  ]
}
```
here we needed to change into these new download link and cheksum  
```
"iso_url": "http://mirror.yandex.ru/centos/7.7.1908/isos/x86_64/CentOS-7-x86_64-Minimal-1908.iso",
      "iso_checksum": "9a2c47d97b9975452f7d582264e9fc16d108ed8252ac6816239a3b58cef5c53d"
```
because in supplied config there was a link to expired release, so next release used 

* ### packer build
change directory to packer and run 
```
packer build centos.json
```
result => new file was created in current directory `centos-7.7.1908-kernel-5-x86_64-Minimal.box`

* ### import created box into vagrant 

check current boxes 
```
vagrant box list
centos/7        (virtualbox, 2004.01)
```
Import `vagrant box add --name centos-7-5 centos-7.7.1908-kernel-5-x86_64-Minimal.box`
check current boxes 
```
vagrant box list
centos/7        (virtualbox, 2004.01)
centos-7-5      (virtualbox, 0)
```
* ### Test of new Vagrant box image 
In two different directories initiated two different vagrants 1) with `centos/7` box 2) with `centos-7-5` box 

ssh to each of them and check with `hostnamectl`

Results =>
for centos/7 box
```
[vagrant@kernel-update ~]$ hostnamectl
   Static hostname: kernel-update
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 431c88ad1d5b8e498a62927ba39c2ffa
           Boot ID: 2b5aece92582422fb61cfe3e941c2df3
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-1127.el7.x86_64
      Architecture: x86-64
```
for centos-7-5 box
```
vagrant@kernel-update ~]$ hostnamectl
   Static hostname: kernel-update
         Icon name: computer-vm
           Chassis: vm
        Machine ID: c2fe3e1506b7734a9f638a525b88ec4a
           Boot ID: 08d1a738b29c4a62bc9b2624db54aeb6
    Virtualization: kvm
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 5.6.13-1.el7.elrepo.x86_64
      Architecture: x86-64

[vagrant@kernel-update ~]$ uname -r
5.6.13-1.el7.elrepo.x86_64
```
Kernells are differnt and kernell for centos-7-5 box is 5.6.13-1.el7.elrepo.x86_64 
So this means we have result as planned 

* ### Load created box into Vagrant Cloud 
Use Vagrant Cloud website UI, straitforvard. 
Providers field value shoud be `virtualbox`
Result => generated `zlovo/centos7-5`
I can use this box anywhere! 

* ### Update local vagrantfile 
agrantfile change :box_name => "centos-7-5", into :box_name => "zlovo/centos7-5"
check the box file is being downloaded from zlovo/centos7-5 

* ### Submit home work 
Create .gitignore file and add there .vagrantfile directory
deleted files shouldn't be commited with `git rm --cached -r .vagrantfile` command 
git Added and commited files from local directory manual_kernell_update 

* Create and write README.MD
Used markdown languade. 

## End
