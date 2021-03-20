# Arch-Linux-Installation
<br><br>
![](/images/archlinux.png) <br><br>
So Here in this tutorial, I am going to explain , How I Dual-Booted ArchLinux With Windows-10 
on my PC with efi partition
<br><br>
## Download ArchLinux iso
First of all we need to Download iso file of ArchLinux From the link below
<br>
#### Download Link : https://archlinux.org/download/
<br><br>
## Making Bootable Drive <br><br>

![](/images/img1.png)<br><br>
Inorder to make bootable drive of ArchLinux , I have used rufus 
Here you can use flash drive of different memory size , It should be minimum of size 2GB
* here we need to select ArchLinux iso file from the where the iso had been downloaded
* In Partition Scheme  I am using GPT Partition , you can select MBR or GPT Partition
* Click on start
<br>
Now after making a bootable drive , we need to access the boot menu and boot from usb drive , Now ArchLinux menu will visible in that 
select Boot Arch linux(x86_64)
(Note: Accesing boot menu for a specfic pc can be found from web or user's manual) <br><br>

![](/images/1-2.png) 
<br><br>
## Installation
At first there will be a similar content in screen as shown below <br><br>
![](/images/img2.jpg)<br><br>
Now we need to partition the drive , where ArchLinux need to be installed <br>
Use this command to list all the disk and partitions on your system <br>
```bash
# lsblk
```
The image below is output while using ```lsblk``` command <br><br>
![](/images/img3.jpg) 
### Partitioning
Use this command to format and partition the drive
``` 
# cfdisk \dev\drv
```
here instead of `drv` give your drive's name
* Create a boot partition with `Type : EFI System`
* Create a root partition with `Type : Linux filesystem`
* Create a boot partition with `Type : Linux swap`
* write the change using `[Write]` by navigating using left and right arrow  and type "yes" to make changes
* now use `[Quit]` to exit writing changes to partition <br><br>
### Create root partition
Use this command to Format partition to ext4 (use your drive name instead of 'drv')
```
# mkfs.ext4 /dev/drv
```
Use this command to Mount root partition (use your drive name instead of 'drv')
```
# mount /dev/drv /mnt
```
### Create swap partition
Use this command to Format swap partition (use your drive name instead of 'drv')
```
# mkswap /dev/drv
```
Use this command to turn on swap partition (use your drive name instead of 'drv')
```
# swapon /dev/drv
```
### Create boot partition
Use this command to make directory for boot partition (use your drive name instead of 'drv')
```
# mkdir /mnt/boot
```
Use this command to Mount boot partition (use your drive name instead of 'drv')
```
# mount /dev/drv /mnt/boot
```
## Connect to Internet <br><br>
### Using Local Network or USB Tethering
Use the command below to scan for available network 
```
# ifconfig
```
Now look for availble network from the the list as show below in the image <br><br>
![](/images/img4.jpg) <br><br>
Now to setup network use the command , instead of `enp....` give your network name
```
# ip link set enp...... up
```
we can conform that we are connected to network by using command 
```
# ping google.com
```
You can use any website to ping instead of `google.com` <br><br>
![](/images/img5.jpg) <br><br>
### Select an appropriate mirror
This is a big problem with installing Arch Linux. If you just go on installing it, you might find that the downloads are way too slow. In some cases, it’s so slow that the download fails.

It’s because the mirrorlist (located in /etc/pacman.d/mirrorlist) has a huge number of mirrors but not in a good order. The top mirror is chosen automatically and it may not always be a good choice.<br>

 First sync the pacman repository using command
 ```
 # pacman -Syy
 ```
 Now, install reflector too that you can use to list the fresh and fast mirrors located in your country
 ```
 # pacman -S reflector
 ```
 Make a backup of mirror list (just in case)
 ```
 # cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
 ```
 Now, get the good mirror list with reflector and save it to mirrorlist. You can change the country from India to your own country
 ```
 # reflector -c "India" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist
 ```
 ### Base Install
 Use the command to install base packages for ArchLinux
 ```
 # pacstrap /mnt base linux linux-firmware nano vim
 ```
 ### Fstab
 Use the command to setup fstab
 ```
 # genfstab -U /mnt >> /mnt/etc/fstab
 ```
 Check the resulting /mnt/etc/fstab file, and edit it in case of errors , Use the command
 ```
 # nano /mnt/etc/fstab
 ```
 ### Chroot
 Change root into the new system:
 ```
 # arch-chroot /mnt
 ```
 ### Time zone
Set the time zone:
```
# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
 ```
 Here change Region and City according to your region and city <br>
 
 Run hwclock to generate `/etc/adjtime`:
 ```
# hwclock --systohc
```
This command assumes the hardware clock is set to UTC
### Localization
Edit `/etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8` and other needed locales , Use command <br> 
```
# nano /etc/locale.gen
```
In nano 
* Use `ctrl` + `c` to write
* Use `ctrl` + `x` to quit <br>
Generate the locales by running
```
# locale-gen
```
Create the locale.conf file, and set the LANG variable accordingly , Use command
```
# nano /etc/locale.conf
```
Now add the line given below
```
LANG=en_US.UTF-8
```
### Network configuration
Create the hostname file , Use command
```
# nano /etc/hostname
```
In nano 
* Use `ctrl` + `c` to write
* Use `ctrl` + `x` to quit <br>
Add you hostname instead of `myhostname`
```
myhostname
```
Edit hosts file by using command
```
# nano /etc/hosts
```
Add matching entries to hosts 
```
127.0.0.1    localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```
Change `myhostname` to your hostname <br>
If the system has a permanent IP address, it should be used instead of 127.0.1.1 <br>
Complete the network configuration for the newly installed environment, that may include installing suitable network management software.
### Root password
Set the root password
```
# passwd
```
### Install Other Pacakges
* Use command given below to install bootloader and other packages 
```
# pacman -S grub efibootmgr networkmanager network-manager-applet dialog  wireless_tools wpa_supplicant os-prober mtools 
dosfstools base-devel linux-headers ntfs-3g gvfs
```
* To install `grub` use command 
```
# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```
* To generate grub configuration , Use the command
```
# grub-mkconfig -o /boot/grub/grub.cfg
```
* we need to enable network manager inorder to start at reboot , Use command
```
# systemctl enable NetworkManager
```

