# ArchLinux Installation
## Arch Linux 

Fisrt time, I'll install ArchLinux inside of VirtualBox.

```
  | Name              |                      Link                               |  
  |-------------------|---------------------------------------------------------|
  | ArchLinux ISO     |   https://www.archlinux.org/download/                   |
  | VirtualBox        |   https://www.virtualbox.org/wiki/Downloads             | 
  | All mirror list   |   https://www.archlinux.org/mirrorlist/all/             |
 ```

### Step 1 - Check Internet Connection and start 

```ping -c 3 www.archlinux.org```

### Step 2 - Partitioning

- ####  Type [ef02] on type definition for BIOS partition

![Screenshot](Images/01.png)

- ##### BIOS partition

![Screenshot](Images/02.png)

```
  | Reference | Mounting point    |  Size                        |   File system    |
  |-----------|-------------------|------------------------------|------------------|
  | /dev/sda1 |  /boot            |  512 M                       |  ext2            |
  | /dev/sda2 |                   |  RAM size or more            |  swap            |
  | /dev/sda3 |  /                |  20 Go                       |  ext4            |
  | /dev/sda4 |  /home            | The rest of the disc         |  ext4            | 
 ```
 
 ``` cfdisk /dev/sda ``` 

![Screenshot](Images/03.png)


### Step 3- BIOS - Formatting

```sh
$ mkfs.ext4 /dev/sda1
$ mkfs.ext4 /dev/sda2
$ mkswap /dev/sda3
$ swapon /dev/sda3
$ mkfs.ext4 /dev/sda4
```

#### Step 4 - BIOS - Mounting

```sh
$ mount /dev/sda2 /mnt
$ mkdir -p /mnt/{boot,home}
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda4 /mnt/home
$ mount /dev/sda3 /mnt
```

- #### Type [ef00] on type definition for EFI partition

![Screenshot](Images/04.png)

```cgdisk /dev/sda```

```
  | Reference | Mounting point    |  Size                        |   File system    |Style     |
  |-----------|-------------------|------------------------------|------------------|----------|
  | /dev/sda1 |  /boot            |  512 M                       |  EFI             |  EF00    |
  | /dev/sda2 |                   |  RAM size or more            |  swap            |  8200    |
  | /dev/sda3 |  /                |  20 Go                       |  ext4            |  8300    |
  | /dev/sda4 |  /home            | The rest of the disc         |  ext4            |  8300    |
 ```

![Sceenshot](Images/05.png)

### Step 3-EFI  - Formatting 
```sh
$ mkfs.fat -F32	/dev/sda1
$ mkswap /dev/sda2
$ swapon /dev.sda2
$ mkfs.ext4 /dev/sda3
$ mkfs.ext4 /dev/sda4
```

#### Step 4 - EFI - Mounting 
```sh
$ mount /dev/sda3 /mnt
$ mkdir -p /mnt/{boot,home}
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda4 /mnt/home
```

### Step 5 - MirrorList

![Screenshot](Images/06.png)

```sh
$ nano /etc/pacman.d/mirrorlist
```
###### Find your country, and move it to the top of the list

### Step 6 - Install Base

```sh
$ pacstrap /mnt base base-devel 
```

### Step 7 - fstab
```sh
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```

### Step 8 - chroot
```sh
$ arch-chroot /mnt
```

### Step 9 - Locale



```sh
$ nano /etc/locale.gen

...
[en_US.UTF-8 UTF-8] 
...

```

### Step 10 - locale-gen
```sh
$ locale-gen
```

### Step 11 - Language setting
```sh
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
$ export LANG=en_US.UTF-8
```

### Step 12 - TimeZone & Hardware Clock
```sh
$ ln -sf /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime
$ hwclock --systohc --utc
```

### Step 13 - Hostname
```sh
$ echo "YourArch" > /etc/hostname
$ cat /etc/hostname
    YourArch
```

### Step 14 - Password
```sh
$ passwd
```

### Step 15 - BootLoader

##### BIOS
```sh
$ pacman -S grub
$ grub-install --recheck /dev/sda
$ grub-mkconfig -o /boot/grub/grub.cfg
```

##### EFI

![Screenshot](Images/07.png)

```sh
$ mkinitcpio -p linux
$ pacman -S bash-completion
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

### Step 16 - Add User
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

### Step 17 - Add archlinuxfr
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

After run:   ```pacman -Syy```

### Step 18 - NetworkManager and other
```sh
$ pacman -S networkmanager wpa_supplicant dialog network-manager-applet gnome-keyring
$ systemctl enable NetworkManager.service
$ systemctl start NetworkManager.service
```

```sh
$ pacman -S vim tmux git 
```

### Step 20 - Unmount & Reboot

```sh
$ exit
$ umount -R /mnt
$ reboot
```
Arch Machine Setup is Done ! Let's take a coffee break


`--------------------------------------------------------------------------`


##After Installing Arch 
### Step 1 - zsh
```sh
$ pacman -S zsh
$ chsh
$ /bin/zsh
```

### Step 2 - xorg
```sh
$ pacman -S xorg-server xorg-server-utils xorg-xinit xorg-xclock xterm
```

### Step 3 - Slim (wallpaper)
```sh
$ pacman -S slim archlinux-themes-slim slim-themes
$ vim /etc/slim.conf
```
#### Edit & Uncomment like this
```sh
login_cmd exec /bin/zsh -l ~/.xinitrc %session
daemon yes
default_user duyhenry
focus_password yes
current_theme archlinux-simplyblack
```

### Step 4 - enable Slim for Login Screen
```sh
$ systemctl enable slim.service
```

### Step 5 - Xfce
```sh
$ pacman -S xfce4 xfce4-goodies gamin
```

### Step 6 - Start X at Login
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
### Step 7 - Login with user
```sh
$ su user
$ cd
$ chsh
$ /bin/zsh
```

### Step 8 - enable Mouse & Keyboard
```sh
$ pacman -S xorg-drivers
```

### Step 9 - enable Disk Mount
```sh
$ pacman -S gvfs
```

### Step 10 - Virtualbox Guest Additions
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

### Step 11 - Install Chromium firefox
```sh
$ pacman -S chromium firefox flashplayer  
```

run these commands
```sh
$ pacman -S --refresh yaourt
```
