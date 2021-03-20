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
lsblk
```
The image below is output while using ```lsblk``` command <br><br>
![](/images/img3.jpg) 
### Partitioning
Use this command to format and partition the drive
``` 
cfdisk \dev\drv
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
mkfs.ext4 /dev/drv
```
Use this command to Mount root partition (use your drive name instead of 'drv')
```
mount /dev/drv /mnt
```
### Create swap partition
Use this command to Format swap partition (use your drive name instead of 'drv')
```
mkswap /dev/drv
```
Use this command to turn on swap partition (use your drive name instead of 'drv')
```
swapon /dev/drv
```
### Create boot partition
Use this command to make directory for boot partition (use your drive name instead of 'drv')
```
mkdir /mnt/boot
```
Use this command to Mount boot partition (use your drive name instead of 'drv')
```
mount /dev/drv /mnt/boot
```
## Setting up Network Configuration <br><br>
### Using Local Network or USB Tethering
Use the command below to scan for available network 
```
ifconfig
```
Now look for availble network from the the list as show below in the image <br><br>
![](/images/img4.jpg) <br><br>
Now to setup network use the command , instead of `enp....` give your network name
```
ip link set enp...... up
```
we can conform that we are connected to network by using command 
```
ping google.com
```
You can use any website to ping instead of `google.com` <br><br>
![](/images/img5.jpg) <br><br>
### Select an appropriate mirror
This is a big problem with installing Arch Linux. If you just go on installing it, you might find that the downloads are way too slow. In some cases, it’s so slow that the download fails.

It’s because the mirrorlist (located in /etc/pacman.d/mirrorlist) has a huge number of mirrors but not in a good order. The top mirror is chosen automatically and it may not always be a good choice.<br>

 First sync the pacman repository using command
 ```
 pacman -Syy
 ```
 Now, install reflector too that you can use to list the fresh and fast mirrors located in your country
 ```
 pacman -S reflector
 ```
 Make a backup of mirror list (just in case)
 ```
 cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
 ```
 Now, get the good mirror list with reflector and save it to mirrorlist. You can change the country from India to your own country
 ```
 reflector -c "India" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist
 ```
 
