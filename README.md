# arch-linux-dual-boot-win10
The following is a guide to dualbooting arch linux and windows 10
https://wiki.archlinux.org/title/Installation_guide
Requirements: An 8GB or higher USB Drive to create a bootable disk with arch linux
Download the ISO here: https://archlinux.org/download/ 
1. Make space for arch linux: Use Disk management in windows to shrink your windows partition and free up space for arch *must have at least 40 to 50GB free space
2. Create Free space for Arch Linux:
   #Open Disk Management by either right-clicking on your windows icon on the bottom or going into command prompt and typing DISKMGMT to access disk manager.
   #Next View Connected drives and their partitions, Choose the C or D or any other Drive for Arch Linux right-cick on it and  Select Shrink Volume to allocate free space.
   #Allocate at least 40GB or more to create unallocated free space by clicking on shrink.
   
![image](https://github.com/user-attachments/assets/f1669acf-8419-44eb-9569-3ea1c6592d74)

4. Make a bootable USB for Arch linux
   #Connect USB stick to your PC you can either use Rufus or BalenaEtcher to create bootable ISO into the USB drive link for Rufus https://rufus.ie/en/ or link for BalenaEtcher https://etcher.balena.io
   
![image](https://github.com/user-attachments/assets/11ef7144-2809-43e1-bb40-5f192f9d7a55)

BIOS Settings
restart your PC 
during the restart, enter to the BIOS by using keyboard shortcut F2,F9, Delete Key or ESC depending on your motherboard's manufacturer.
in BIOS settings, Disable secure boot, then save changes in the BIOS settings and boot into USB drive you could dalso change primary boot order to USB drive.

4. Booting into Arch Linux and installation
   
![image](https://github.com/user-attachments/assets/04eb0cb4-a491-4f1b-b1de-bceb37b5dcc9)

now on your monitor this is optional to increase font by Typing setfont ter-122n on terminal.
next you wanna use iWCTL tool to connect to wifi (if you have a wired ethernet connection you can skip this step)
To find and connect to your network type: ```iwctl device list``` then ```iwctl station wlan0 get-networks```connect to wifi typing ```iwctl station wlan0 connect WIFI_NAME``` (example: IAMSNDE@spectrum)
Once you're connected ```ping google.com``` to check your connection after checking disable the ping by pressing CTRL + C
Then type ```pacman -Sy``` to sync package database and ```pacman -S archlinux-keyring```
Now create partitions for Arch linux typing ```lsblk``` to list drives. Identify the main drive you chose now for me it was nvme0n1 which is main drive for windows 10 and SDA should be the bootable USB 

![image](https://github.com/user-attachments/assets/328bdc1e-0317-4efd-847d-28d4a71aceda)

Then Create partitions for Arch linux using free space from earlier
Typing ```cfdisk /dev/nvme0n1```

![image](https://github.com/user-attachments/assets/efc3ac12-ea74-447c-a22a-de731b2efb2c)

Create 3 partitions: EFI, ROOT and Swap by using arrow keys into New with Type and Write
#EFI partition with type EFI System should be around 800MB.
#SWAP partition with type Linux SWAP should be around 1GB to 2GB
#ROOT partition with type Linux file system

![image](https://github.com/user-attachments/assets/5c65c3d1-2c18-4d98-b22d-627ca77bf4ba)

Format the Partitions 
Typing lsblk again to view the new created partitions
nvme0n1p5 for EFI, nvme0n1p6 for ROOT, and nvme0n1p7 for SWAP.

Format the Arch linux EFI partition by typing ```mkfs.fat -F32 /dev/nvme0n1p5```
now for Root partition ```mkfs.ext4 /dev/nvme0n1p6```
finally for swap partition ```mkswap /dev/nvme0n1p7```

now mount them partitions ```mount /dev/nvme0n1p6 /mnt```
Create `mnt` directory inside and name it `boot`
```mkdir /mnt/boot```

```mount /dev/nvme0n1p5 /mnt/boot```

Enable the swap ```swapon /dev/nvme0n1p7```

type lsblk 

![image](https://github.com/user-attachments/assets/1817e966-65a6-43bd-b1da-564104546497)

and it should look a bit like this.

Now finally install Arch linux to root partition type ```pacstrap -i /mnt base base-devel linux linux-headers linux-firmware amd-ucode sudo git nano htop cmake make and bluez bluez-utils networkmanager gcc cargo mpv```
Accept the defaults and it'll start installing base of Arch Linux.

now to create (FSTAB) File System Table 
type in ```genfstab -U /mnt >> /mnt/etc/fstab```

Then chroot into live session by typing ```arch-chroot /mnt```

![image](https://github.com/user-attachments/assets/68589ef3-1771-449e-b108-55d5c173366d)

Create Root password typing ```passwd``` and set the root password.

Adding a User ```useradd -m -g users -G wheel,storage,video,audio -s /bin/bash USER_NAME``` replace the USER_NAME with your name (eg. Jason) 

Then set the user password by entering ```passwd USER_NAME```

now enable wheel group to allow the user to access to root privileges by entering ```EDITOR=nano visudo```

scroll all the way down till you see %wheel ALL=(ALL:ALL) ALL then uncomment the % symbol and save changes with CTRL + O and CTRL + X to exit 

![image](https://github.com/user-attachments/assets/21126637-1440-4e8d-8792-4e643ea03e6c)

Now Setup the timezone/region using ```ln -sf /usr/share/zoneinfo/``` and then press tab to view the list and choose the timezone (eg. ```ln -sf /usr/share/zoneinfo/Asia/Singapore/etc/localtime```)
Sync the current time to hardware clock with ```hwclock --systohc```

Setup System Language
Type ```nano /etc/loccale.gen```
scroll all the way down until you see your preferred language for example mine is American English 

![image](https://github.com/user-attachments/assets/f44884c7-af51-47c8-9d39-f3b1c7bb60ca)

so you wanna uncomment the # to choose your preferred language then type CTRL + O to Save and CTRL + X to exit.
now generate loccale & config file
by typing ```locale-gen``` and then ```echo "LANG=en_US.UTF-8" >> /etc/locale.conf```

Setup Host Name you can name it any name you want but for my sake I'm gonna name it archlinux
```echo "archlinux" >> /etc/hostname```
then type  ``` nano /etc/hosts ``` add the lines below:

![image](https://github.com/user-attachments/assets/515b3e4e-c386-497a-8268-1ad8735794f8)


Green is your hostname save changes.

Installing GRUB boot loader

```pacman -S grub efibootmgr dosfstools mtools```

```grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB```

```grub-mkconfig -o /boot/grub/grub.cfg```

Enabling services for bluetooth and wifi \\

```systemctl enable bluetooth```
```systemctl enable NetworkManager```

Now Exit from chroot ```umount -lR /mnt```
 then ```shutdown now ``` and remove the USB Drive

Once you turn on the PC you shoud be able to see the grub menu and you can only boot into arch for now let's fix that \\
boot into arch type enter your username and password type ```sudo nmcli dev wifi connect WIFI_NAME password WIFI_PASSWORD ``` (eg. sudo nmcli dev wifi connect IAMSNDE@spectrum password THEPASSWORDISLEE)
Update pacman database with sudo pacman -Sy

Installing GUI / DE for Arch Linux 
I chose Plasma KDE for example. ```sudo pacman -S xorg sddm plasma-meta plasma-workspace kde-applications noto-fonts noto-font-emoji ttf-dejavu ttf-font-awesome``` then ```sudo systemctl enable sddm```
Then Reboot ```sudo reboot now```

After Rebooting you'll see the login screen enter your username and passsword once again and go into your terminal by Pressing CTRL + ALT + T and type in ```sudo pacman -Sy flatpak``` this will install flatpak and it will show in the Discover app.

if you're using NVIDIA you can install their drives using ```sudo pacman -Sy nvidia```

ADDING Windows Entry to GRUB in order to for grub to show the boot loader between choosing arch linux and windows 10
First install the OS-Prober with ```sudo pacman -Sy os-prober```
Next edit the grub file by ```sudo nano /etc/default/grub```
Set the GRUB_TIMEOUT=20 for 20seconds.
scroll down to the bottom and uncomment # at #GRUB_DISABLE_OS_PROBER=false

![image](https://github.com/user-attachments/assets/3f7366c3-f43c-4053-8319-ec5329978915)

save using CTRL + O an Exit CTRL + X
Finally run ```grub-mkconfig -o /boot/grub/grub.cfg``` to update grub and check that windows boot manager is detectedd

![image](https://github.com/user-attachments/assets/2be72e4d-531e-4696-9999-45b08b62f6bd)

Finally you are free to use arch linux. 

   



   



