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
To find and connect to your network type: ```iwctl device list``` then ```iwctl station wlan0 get-networks```connect to wifi typing ```iwctl station wlan0 connect WIFI_NAME``` (example: WIFI_IAMSNDE@spectrum)
Once you're connected ```ping google.com``` to check your connection after checking disable the ping by pressing CTRL + C
Then type ```pacman -Sy``` to sync package database and ```pacman -S archlinux-keyring```
Now create partitions for Arch linux typing ```lsblk``` to list drives. Identify the main drive you chose now for me it was nvme0n1 which is main drive for windows 10 and SDA should be the bootable USB 

![image](https://github.com/user-attachments/assets/328bdc1e-0317-4efd-847d-28d4a71aceda)

Then Create partitions for Arch linux using free space from earlier
Typing ```cfdisk /dev/nvme0n1```

![image](https://github.com/user-attachments/assets/efc3ac12-ea74-447c-a22a-de731b2efb2c)

Create 3 partitions: EFI, ROOT and Swap by using arrow keys into New with Type and Write
EFI partition with type EFI System should be around 800MB.
SWAP partition with type Linux SWAP should be around 1GB to 2GB
ROOT partition with type Linux file system

![image](https://github.com/user-attachments/assets/5c65c3d1-2c18-4d98-b22d-627ca77bf4ba)







   



   



