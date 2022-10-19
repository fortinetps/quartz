#vmware #alpine
## Make Alpine Linux VMware Image
- Step 1: download [Make Alpine Linux VM Image](https://github.com/alpinelinux/alpine-make-vm-image)
	- If you want to run this script in a docker container, please check this [workaround](https://github.com/alpinelinux/alpine-make-vm-image/issues/15)
- Step 2: create alpine linux image with open-vm-tools and openssh packages
	- ./alpine-make-vm-image --image-format qcow2 --script-chroot --image-size 2G alpine.qcow2 -p open-vm-tools -p open-vm-tools-guestinfo -p openssh
- Step 3: convert qcow2 image to vmdk image
	- qemu-img convert -f qcow2 -O vmdk -o adapter_type=lsilogic,subformat=monolithicFlat alpine.qcow2 alpine.vmdk
```shell-session
root@home# ls *.qcow2 *.vmdk -al
-rw-r--r-- 1 root root 2147483648 Oct 18 14:23 alpine-flat.vmdk
-rw-r--r-- 1 root root  174194688 Oct 18 14:18 alpine.qcow2
-rw-r--r-- 1 root root      10240 Oct 18 14:23 alpine.vmdk
```
- Step 4: create/update new VM, upload vmdk files to VMware, add existing disk, power on VM
	- I am running VMware 6.7 in my lab, when creating the VM, I choose "Linux - Other 4.x or later Linux (64-bit)" as the Guest OS
	- When creating the VM delete/remove the default disk
	- For SCSI controller, please choose "LSI Logic SAS"
	- For VM Options, change Boot Options from EFI to BIOS

![[Pasted image 20221018110604.png]]

![[Pasted image 20221018110631.png]]

	- Go to the datastore, find the VM folder, and upload files

![[Pasted image 20221018110808.png]]

	- Edit VM settings, "ADD NEW DEVICE" -> "Existing Hard Disk" -> browse to the folder and choose the vmdk file you uploaded in previous step

![[Pasted image 20221018111022.png]]

![[Pasted image 20221018111047.png]]

	- Power on VM, login with root (no password), run "ifup eth0", if you have a DHCP server in the network VM is connected to, VM will get an IP address

![[Pasted image 20221018111208.png]]

- Step 5: optimization and others
	- For [open-vm-tools](https://wiki.alpinelinux.org/wiki/Open-vm-tools)
		- rc-update add open-vm-tools boot
		- rc-service open-vm-tools start
	- For [networking](https://wiki.alpinelinux.org/wiki/Configure_Networking)
			- echo "alpine" > /etc/hostname && hostname -F /etc/hostname
			- rc-update add networking boot
			- rc-update add sshd boot
			- add "auto eth0" to /etc/network/interfaces
```shell-session
alpine:~# cat /etc/network/interfaces 
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

post-up /etc/network/if-post-up.d/*
post-down /etc/network/if-post-down.d/*
```
