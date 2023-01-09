###################################### UBUNTU 20.04 ##################################
#--1. Install QEMU
sudo apt install lib32ncurses5 qemu-user-static git pv libncurses5-dev -y

#--2. Download the Ubuntu root filesystem
mkdir ubuntu20
cd ubuntu20
wget -c http://cdimage.ubuntu.com/ubuntu-base/releases/20.04.4/release/ubuntu-base-20.04.4-base-armhf.tar.gz
sudo -s
mkdir rootfs
tar -xvf ubuntu-base-20.04.4-base-armhf.tar.gz -C rootfs

#--3. Copy qemu-user-static and firmwares
cp /usr/bin/qemu-arm-static rootfs/usr/bin/

#--4. Modify etc/apt/sources.list to un-comment all the repositories except the ones starting with deb-src
sed -i 's%^# deb %deb %' rootfs/etc/apt/sources.list

#--5. chroot
wget https://github.com/bigdolphin/chroot/raw/main/ch2root.sh
chmod a+x ch2root.sh
./ch2root.sh -m rootfs/

#--6. Update the repositories
chmod 1777 /tmp
apt update

#--7. Install minimal packages
apt install -y language-pack-en-base sudo bash-completion dialog vim nano lsof udev lsb-base htop psmisc locate rsyslog
apt install -y bison flex bc build-essential cmake automake autoconf cmake-curses-gui pkg-config yasm tmux git
apt install -y net-tools ethtool iputils-ping alsa-utils unzip net-tools netbase ifupdown network-manager ntp usbutils ssh whois
apt install -y apt-utils subversion graphviz espeak i2c-tools evtest sox onboard device-tree-compiler alsamixergui

#--8. Exit chroot and unmount proc, sys, dev, dev/pts
exit
./ch2root.sh -u rootfs/

#--9. Copy rootfs to sd
rsync -aP rootfs/ img_mount/
sync
ls -l img_mount/
umount img_mount/
