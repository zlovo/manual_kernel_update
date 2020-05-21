[alexeyv@desktop-i3gm60g manual_kernel_update]$ vagrant ssh
Last login: Wed May 20 12:10:32 2020 from 10.0.2.2
[vagrant@kernel-update ~]$ uname -r
3.10.0-1127.el7.x86_64

Last login: Wed May 20 12:21:36 2020 from 10.0.2.2
[vagrant@kernel-update ~]$ uname -r
5.6.14-1.el7.elrepo.x86_64

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

vagrant@kernel-update ~]$ cat /proc/version
Linux version 5.6.14-1.el7.elrepo.x86_64 (mockbuild@Build64R7) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC)) #1 SMP Tue May 19 12:17:13 EDT 2020


[vagrant@kernel-update ~]$ uname -r -v
5.6.14-1.el7.elrepo.x86_64 #1 SMP Tue May 19 12:17:13 EDT 2020
[vagrant@kernel-update ~]$ cat /etc/centos-release 
CentOS Linux release 7.8.2003 (Core)

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

##old kernel
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

### Loaded generated vagrant box with new kernel 
[vagrant@kernel-update ~]$ hostnamectl
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

Vagrant.configure("2") do |config|
  config.vm.box = "zlovo/centos7-5"
  config.vm.box_version = "1.0"
end

## At vagrantfile changed from :box_name => "centos-7-5", to :box_name => "zlovo/centos7-5