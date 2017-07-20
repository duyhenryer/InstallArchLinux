# ArchLinux Installation
My Arch Linux Installution

This time, I'll install ArchLinux inside of VirtualBox ;)

### - ArchLinux ISO [Here](https://www.archlinux.org/download/)

### - VirtualBox [Here](https://www.virtualbox.org/wiki/Downloads)

### - All mirror list [Here](https://www.archlinux.org/mirrorlist/all/)

## Step 1 - Check Internet Connection
```sh
$ ping -c 3 www.archlinux.org
```

## Step 2 - Partitioning
#### type [ef02] on type definition for BIOS partition
TODO: Add Screenshot
- /dev/sda1 - boot
- /dev/sda2 - /
- /dev/sda3 - BIOS
- /dev/sda4 - home

#### type [ef00] on type definition for EFI partition
TODO: Add Screenshot
- FreeSpace
- /dev/sda1 - boot
- /dev/sda2 - EFI
- /dev/sda3 - /
- /dev/sda4 - home

#### - For example with EFI
```aidl

cgdisk /dev/sda

	    /dev/sda1       		EFI		                 ef00
	    /dev/sda2       		swap	                8200
	    /dev/sda3	       	filesystem	          8300
    /dev/sda4       filesystem          8300
``` 

## Step 3 - Formatting
#### BIOS
```sh
$ mkfs.ext4 /dev/sda1
$ mkfs.ext4 /dev/sda2
$ mkswap /dev/sda3
$ swapon /dev/sda3
$ mkfs.ext4 /dev/sda4
```

#### EFI
```sh
$ mkfs.fat -F32	/dev/sda1
$ mkswap /dev/sda2
$ swapon /dev.sda2
$ mkfs.ext4 /dev/sda3
$ mkfs.ext4 /dev/sda4
```

## Step 4 - Mounting
#### BIOS
```sh
$ mount /dev/sda2 /mnt
$ mkdir -p /mnt/{boot,home}
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda4 /mnt/home
$ mount /dev/sda3 /mnt
```

#### EFI
```sh
$ mount /dev/sda3 /mnt
$ mkdir -p /mnt/{boot,home}
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda4 /mnt/home
```

## Step 5 - MirrorList
```sh
$ nano /etc/pacman.d/mirrorlist
```
#### Find your country, and move it to the top of the list

## Step 6 - Install Base
#### BIOS
```sh
$ pacstrap /mnt base base-devel 
```

#### EFI
```sh
$ pacstrap /mnt base base-devel 
```

## Step 7 - fstab
```sh
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```

## Step 8 - chroot
```sh
$ arch-chroot /mnt
```

## Step 9 - Locale
```sh
$ nano /etc/locale.gen
```
#### uncomment [en_US.UTF-8 UTF-8] and your language

## Step 10 - locale-gen
```sh
$ locale-gen
```

## Step 11 - Language setting
```sh
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
$ export LANG=en_US.UTF-8
```

## Step 12 - TimeZone & Hardware Clock
```sh
$ ln -s /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime
$ hwclock --systohc --utc
```

## Step 13 - Hostname
```sh
$ echo "MyArch" > /etc/hostname
$ cat /etc/hostname
    MyArch
```

## Step 14 - Password
```sh
$ passwd
```

## Step 15 - BootLoader
#### BIOS
```sh
$ pacman -S grub
$ grub-install --recheck /dev/sda
```

#### EFI
```sh
$ pacman -S bash-completion
$ mkinitcpio -p linux
$ pacman -S intel-ucode
$ bootctl install
$ nano /boot/loader/entries/archlinux.conf
 add more:
	        title ArchLinux
	        linux /vmlinuz-linux
	        initrd /intel-ucode.img
	        initrd /initramfs-linux.img
	        options root=/dev/sda3 rw
```

## Step 16 - Setup GRUB main config
```sh
$ grub-mkconfig -o /boot/grub/grub.cfg
```

## Step 17 - NetworkManager
```sh
$ pacman -S networkmanager wpa_supplicant dialog network-manager-applet gnome-keyring
$ systemctl enable NetworkManager.service
$ systemctl start NetworkManager.service
```

## Step 18 - Add User
```sh
$ useradd -m -g users -G wheel,storage,power -s /bin/bash username
$ passwd username
$ pacman -S sudo
$ EDITOR=nano visudo
    ...
    	        root 	  ALL =(ALL) ALL
add more:	username  ALL =(ALL) ALL
    ...

	#Uncomment
	wheel ALL=(ALL) ALL
```

## Step 19 - Unmount & Reboot
```sh
$ exit
$ umount -R /mnt
$ reboot
```
Arch Machine Setup is Done !
Let's take a coffee break

=== After Installing Arch ===
## Step 1 - zsh
```sh
$ pacman -S zsh
$ chsh
$ /bin/zsh
```

## Step 2 - xorg
```sh
$ pacman -S xorg-server xorg-server-utils xorg-xinit xorg-xclock xterm
```

## Step 3 - Slim (wallpaper)
```sh
$ pacman -S slim archlinux-themes-slim slim-themes
$ vim /etc/slim.conf
```
#### Edit & Uncomment like this
```sh
login_cmd exec /bin/zsh -l ~/.xinitrc %session
daemon yes
default_user yhoshino11
focus_password yes
current_theme archlinux-simplyblack
```

## Step 4 - enable Slim for Login Screen
```sh
$ systemctl enable slim.service
```

## Step 5 - Xfce
```sh
$ pacman -S xfce4 xfce4-goodies gamin
```

## Step 6 - Start X at Login
```sh
$ cp /etc/X11/xinit/xinitrc ~/.xinitrc
$ vim ~/.xinitrc
```

replace this
```sh
twm &
xclock -geometry 50x50-1+1 &
xterm -geometry 80x50+494+51 &
xterm -geometry 80x20+494-0 &
exec xterm -geometry 80x66+0+0 -name login
```

to this
```sh
exec startxfce4
```

copy to duyhenry's home directory
```sh
$ cp ~/.xinitrc /home/duyhenry/
```
## Step 7 - Login with yhoshino11
```sh
$ su yhoshino11
$ cd
$ chsh
$ /bin/zsh
```

## Step 8 - enable Mouse & Keyboard
```sh
$ pacman -S xorg-drivers
```

## Step 9 - enable Disk Mount
```sh
$ pacman -S gvfs
```

## Step 10 - Virtualbox Guest Additions
```sh
$ pacman -S virtualbox-guest-utils
$ modprobe -a vboxguest vboxsf vboxvideo # try to copy things from host to guest with the clipboard to see if it works
$ VBoxClient-all
$ systemctl enable vboxservice
```

in ~/.xinitrc, add this line before exec startxfce4
```sh
# Read More https://wiki.archlinux.org/index.php/VirtualBox#Launch_the_VirtualBox_guest_services
/usr/bin/VBoxClient-all
```

## Step 11 - Install Chromium
```sh
$ pacman -S chromium firefox 
```

## Step 12 - Yaourt
```sh
$ vim /etc/pacman.conf
```
```sh
[multilib]
Include = /etc/pacman.d/mirrorlist # Uncomment
```

add this on bottom of file
```sh
[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch
```

run these commands
```sh
$ pacman -S --refresh yaourt
```