## Installation Process

To install my Arch Linux system, the [Arch Wiki Installation Guide](https://wiki.archlinux.org/title/Installation_guide) was used.

An Arch Linux .iso file was received via flash drive to load the .iso into VMWare Fusion. From here, the installation guide follows through the steps to setup a Linux system.

### Installing Arch Linux
This section will cover every command used to install and configure Arch Linux.

First, the boot mode was verified to be UEFI with the command:
```markdown
ls /sys/firmware/efi/efivars
```
Successful running of this command verifies that the system is in UEFI mode.

Next, internet connection was checked with the commands:
```markdown
ip link
ping archlinux.org
```
The network interfaces are listed and packets were returned, confirming internet connection.

Verify time and date with the command:
```markdown
timedatectl status
```
To partition the disk, first check the current partition status:
```markdown
fdisk -l
```
To create a new partition table, choose the disk and create a table:
```markdown
fdisk /dev/sda
g
```
Then, partitions can be added with the 'n' option and then specifying an ID, first sector, and second sector value.
Three partitions were created in this installation:
1. 300MiB for efi
ID:1 First sector: default (2048) Second sector: +300M

2. 1GiB for swap
ID:2 First sector: default (616448) Second sector: +1G

3. Rest of Space (root partition)
ID:3 First sector: default (2713600) Second sector: default (16777182)

Two of the partitions need to have their type changed from the default Linux Filesystem type.
Partition 1 is changed to type EFI System with the command (still inside fdisk /dev/sda):
```markdown
t
1 (part ID)
1 (type for EFI)
```
Partition 2 is changed to type Swap with the command (still inside fdisk /dev/sda):
```markdown
t
2 (part ID)
19 (type for swap)
```
Finally, use option 'w' to write changes and exit the fdisk prompt.
The partitions have now been created, and next need to be formatted.

To format the partitions, different commands are used depending on which file system is fit for the partition type.

Partition sda1 is an EFI System so it must be FAT32:
```markdown
mkfs.fat -F 32 /dev/sda1
```
Partition sda2 is a swap partition, so it will be swap:
```markdown
mkswap /dev/sda2
```
Partition sda3 is a linux filesystem so it will use generic Ext4:
```markdown
mkfs.ext4 /dev/sda3
```
With the partitions all formatted, they are ready for mounting.
For each of the partitions and file systems:

Mount sda3 (linux filesystem root partition) to /mnt:
```markdown
mount /dev/sda3 /mnt
```
Mount sda1 (EFI partition) to /mnt/boot (need to create directory):
```markdown
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```
Swap partition sda2 gets initialized using swapon:
```markdown
swapon /dev/sda2
```
To check that mounts have been successful:
```markdown
lsblk -f
```
Here you can see the partitions with there types and filesystems, as well as verify they are mounted to the correct directories.

Next, run this command to install essential packages:
```markdown
pacstrap /mnt base linux linux-firmware
```
Then generate the fstab file with:
```markdown
genfstab -U /mnt >> /mnt/etc/fstab
```
Now, chroot into the new system:
```markdown
arch-chroot /mnt
```
Set Timezone to America Chicago for CST:
```markdown
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
```
Run this command to generate /etc/adjtime:
```markdown
hwclock --systohc
```
Install nano text editor for editing files:
```markdown
pacman -S nano
```
Localization:
Edit the locale.gen file by uncommenting en_US.UTF-8 UTF-8.
```markdown
nano /etc/locale.gen
```
Then generate locales with:
```markdown
locale-gen 
```
Create locale.conf file and set LANG variable:
```markdown
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
```
Create the hostname file, here naming the host 'myarchlinux':
```markdown
echo "myarchlinux" >> /etc/hostname
```
Now download networkmanager, it will be enabled later:
```markdown
pacman -S networkmanager
```
Set the root password:
```markdown
passwd
```
Install a bootloader (using GRUB as bootloader here):
```markdown
pacman -S grub
pacman -S efibootmgr
```
Install GRUB UEFI application:
```markdown
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```
Make grub config file:
```markdown
grub-mkconfig -o /boot/grub/grub.cfg
```
Enable micro-code updates using intel-ucode:

Install package for intel processors and remake grub config to automatically enable:
```markdown
pacman -S intel-ucode
grub-mkconfig -o /boot/grub/grub.cfg
```
Now exit chroot and reboot the system to start in the new system:
```markdown
exit
reboot
```

### In the New System
Sign in as root with root password:
```markdown
(Jiu Li, Do the thing!)
```
Test network with ping: //this failed so I had to fix it below
```markdown
ping 8.8.8.8
```
To fix this, enable networkmanager: //this will make it run on boot
```markdown
systemctl enable NetworkManager.service
```
Download desktop environment LXDE:
```markdown
pacman -S lxde
```
Run desktop with:
```markdown
lxdm
```
Set DE to start at boot:
```markdown
systemctl enable lxdm
```
Install sudo package for sudo command:
```markdown
pacman -S sudo
```
Create users:
For myself, create user jarow:
```markdown
useradd -m -s /bin/bash jarow
passwd jarow
(set password)
```
Create user sal and set password to change on login:
```markdown
useradd -m -s /bin/bash sal
passwd sal
passwd -e sal
```
Create user codi and set password to change on login:
```markdown
useradd -m -s /bin/bash codi
passwd codi
passwd -e codi
```
Add users to sudoers:
```markdown
EDITOR=nano visudo
```
(Add line "username ALL=(ALL) ALL" under user privilege specification for each user)

Install new shell (zsh):
```markdown
pacman -S zsh
```
Can switch into new shell with:
```markdown
chsh
/bin/zsh (on prompt)
su - username (of current user)
```
Install openssh:
```markdown
pacman -S openssh
```
ssh into class gateway:
```markdown
ssh -p53997 jgm5877@129.244.245.21
(Enter password)
ssh -p53997 admin@192.168.2.33
(Enter password)
```
For web browsing, download Links browser:
```markdown
pacman -S links
```
Color coding the terminal:
Follow this link https://averagelinuxuser.com/linux-terminal-color/
to download files and move them into appropriate locations to add color coding.
This requires unzip, downloaded using:
```markdown
pacman -S unzip
```
Add some aliases to ~/.bashrc (actually to /etc/bash.bashrc so they are universal to all users):
```markdown
nano /etc/bash.bashrc
```
Added these aliases in the file:
```markdown
alias ls='ls --color=auto'
alias ll='ls -la'
alias l.='ls -d .* --color=auto'
alias ..='cd ..'
alias gping='ping 8.8.8.8'
```


## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/jarowmyers/jarowmyers.github.io/edit/main/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/jarowmyers/jarowmyers.github.io/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
