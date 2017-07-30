# Installing Arch Linux using default guide

# find out boot mode
ls /sys/fireware/efi/efivars
if not present -> BIOS mode


lsblk
lsblk -Sp

## check internet connection
## openconnect if behind CISCO vpn
ping -c 5 google.com

## update system clock
timedatectl set-ntp true

## disk partition
fdisk /dev/sda

1. clear disk table ( o )
2. create a 400MB space for /boot ( n )
3. make that partition bootable ( a )
4. change the type (t 8E)
5. save changes (w)

mkfs.ext2 /dev/sda1
mkfs.ext4 /dev/sda2

mkdir /mnt/boot
mkdir /mnt/home
mount /dev/sda2 /mnt
mount /dev/sda1 /mnt/boot

pacstrap -i /mnt base
genfstab -U -p /mnt >> /mnt/etc/fstab

arch-chroot /mnt

pacman -S openssh grub-bios linux-header linux-lts linux-lts-header wpa_supplicant wireless_tools

vi /etc/mkinitcpio.conf 
(( add encrypt lvm2 )) between block and filesystems
mkinitcpio -p linux
mkinitcpio -p linux-lts

vi /etc/locale.gen
(( remove # for en_US-UTF8))
locale-gen
echo LANG=en_US.UTF-8 >> /etc/locale.conf
export LANG=en_US.UTF-8

ln -s /usr/share/zoneinfo/Iran /etc/localtime
hwclock –systohc –utc

echo “my-hostname” >> /etc/hostame
vi /etc/hosts

add “my-hostname” to the end of each line

grup-install –target=i386-pc –reckeck /dev/sda
cp /usr/share/locale/en\@quo/LC_M.../grub.mo /boot/grub/locale/grub.mo
grup-mkconf -o /boot/grub/grub.cfg
useradd -m -g users -s /bin/bash king110

passwd
passwd king110

exit
umount /mnt/boot
umount /mnt/home
umount /mnt

reboot

free -m
blkid >> /etc/fstab

pacman conf >> multilib
packman -Sy wpa_actiond dialog 	xf86-input-libinput xorg-server xorg-xinit xorg-server-utils mesa
pacman -S xf86-video-intel lib32-intel-dri lib32-mesa lib32-libgl
cp /etc/netctl/examples/wireless-wpa /etc/netctl/wireless-name

localectl set-locale LANG="en_US.UTF8"
