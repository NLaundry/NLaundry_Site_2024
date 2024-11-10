+++
title = 'ZFS Boot Menu - I put that sh\*t on everything (and how I do it)'
date = 2024-11-10T17:19:54-05:00
draft = false
+++

#BasedOn: https://docs.zfsbootmenu.org/en/v2.3.x/guides/debian/bookworm-uefi.html
#BasedOn: https://openzfs.github.io/openzfs-docs/Getting%20Started/Debian/Debian%20Bookworm%20Root%20on%20ZFS.html


### 1. create debian live install media
#MediaSource: https://www.debian.org/CD/live/ - choose the "default" don't need a DE
#Installer: https://etcher.balena.io/ (works on linux, mac, and windows)
#Requirements: install media (from debian), and an 8gb or larger USB

Once these are both installed:
1. Flash from file
2. Select target -> your USB stick
3. Flash!
4. wait for validation you speed fiend. Do not skip!

### 2. Boot from live media

1. plug that sucker in
2. it'll bring you to a screen like: #TODO add screenshot

### 3. Prepare packages

We're adding extra sources to apt's packages. 
To do so, we edit a file at /etc/apt/sources.list
I'm going to use vi for this, you can use nano or cat it in idc.

1. switch into super user `sudo su`
2. `vi /etc/apt/sources.list`
3. add contrib and non-free-firmware

`deb http://deb.debian.org/debian/ bookworm main contrib non-free-firmware`
leave the rest as is.

#Note: if yours doesn't say "bookworm" (maybe it's trixie), 
you're probably reading this tutorial past 2024, when bookworm was the 
current debian version. I can't guarantee that this tutorial will work 
anymore but it likely isn't too different. Use at your own risk.

4. update `apt update`

### 4. Install SSH (Optional but highly recommended)

You don't want to have to type a bunch of zfs commands yourself
- they're error prone
- they're lengthy
- save yourself the hassle. SSH in with another machine.

1. Install SSH  `apt install --yes openssh-server`
2. Restart the SSH service `systemctl restart ssh`
3. Find your ip address for SSH: `ip addr`

you'll get something like #TODO add screenshot
it's probably enp<NUMBER>s0 - where number is likely the lowest one
you can tell which is active because it won't say "NO-CARRIER"
and you'll likely have inet6 values too

4. ssh in with `ssh user@<IP ADDRESS>`
5. password is: live
6. switch back into root: `sudo su`


### 5. Create the ID var for later use

#DirectlyFrom ZFSBootMenu tutorial
Source /etc/os-release

The file /etc/os-release defines variables that describe the running distribution. In particular, the $ID variable defined within can be used as a short name for the filesystem that will hold this installation.

```sh
source /etc/os-release
export ID
```

### 6. Install Necessary Packages

we're installing: 
1. deboostrap - installs a minimal debian system later
2. gdisk - manages disks
3. dkms - dynamic kernel module support (need this to add zfs stuff I think)
4. linux-headers - .h files for linux kernel kind of like linux APIs #LearnMore below
5. zfsutils-linux - for configuring zfs on linux

```sh
apt install debootstrap gdisk dkms linux-headers-$(uname -r)
apt install zfsutils-linux
```

#LearnMore: linux headers: https://thelinuxcode.com/install-kernel-headers-linux/

### 7. generate hostid

"Creates /etc/hostid file and stores the host ID in it. If hostid was provided, validate and store that value. Otherwise, randomly generate an ID."

`zgenhostid -f 0x00bab10c`

#LearnMore: https://openzfs.github.io/openzfs-docs/man/master/8/zgenhostid.8.html

### 8. Set environment vars for devices


"Verify your target disk devices with lsblk. /dev/sda, /dev/sdb and /dev/nvme0n1 used below are examples."

`lsblk`
#TODO: add screenshot

For Boot 
```sh
export BOOT_DISK="/dev/nvme0n1"
export BOOT_PART="1"
export BOOT_DEVICE="${BOOT_DISK}p${BOOT_PART}"
```

For Boot 
```sh
export POOL_DISK="/dev/nvme0n1"
export POOL_PART="2"
export POOL_DEVICE="${POOL_DISK}p${POOL_PART}"
```

### 9. Prepare Disks
1. Wipe Partitions

In case the drive was used for anything before. Do this anyway just in case.

```sh
zpool labelclear -f "$POOL_DISK"

wipefs -a "$POOL_DISK"
wipefs -a "$BOOT_DISK"

sgdisk --zap-all "$POOL_DISK"
sgdisk --zap-all "$BOOT_DISK"
```

2. Create EFI boot partition

```sh
sgdisk -n "${BOOT_PART}:1m:+512m" -t "${BOOT_PART}:ef00" "$BOOT_DISK"
```

3. Create zpool partition

```sh
sgdisk -n "${POOL_PART}:0:-10m" -t "${POOL_PART}:bf00" "$POOL_DISK"
```

At this point, our disk has two partitions: 
one for booting, and one for storing the zpool which will house the system
Note how little of the disk the boot partition takes up.

#LearnMore: `man sgdisk`

### 10. Create Zpool

```sh
zpool create -f -o ashift=12 \
 -O compression=lz4 \
 -O acltype=posixacl \
 -O xattr=sa \
 -O relatime=on \
 -o autotrim=on \
 -o compatibility=openzfs-2.1-linux \
 -m none zroot "$POOL_DEVICE"
```

Note how we use the $POOL_DEVICE environment var from earlier
#LearnMore: `man zpool create`

Here’s what each flag does in the `zpool create` command:
- `-f`: Forces the creation of the pool, even if the device appears in use.
- `-o ashift=12`: Sets the pool's sector size to 4K (2^12 bytes), optimizing for modern disks.
- `-O compression=lz4`: Enables LZ4 compression for better space efficiency.
- `-O acltype=posixacl`: Uses POSIX ACLs for fine-grained file permissions.
- `-O xattr=sa`: Stores extended attributes in a more efficient system attribute space.
- `-O relatime=on`: Updates file access times only if they are newer than the modification time.
- `-o autotrim=on`: Enables automatic trimming of free space for SSDs.
- `-o compatibility=openzfs-2.1-linux`: Ensures compatibility with OpenZFS 2.1 on Linux systems.
- `-m none`: Disables automatic mounting of the pool's root dataset.
- `zroot`: Names the new ZFS pool as "zroot".
- `"$POOL_DEVICE"`: Specifies the device to use for the pool creation.

Why this matters
- **`ashift=12`**: Aligns with modern disk sectors, enhancing performance.
- **`compression=lz4`**: Reduces disk space usage with minimal CPU overhead.
- **`acltype=posixacl` & `xattr=sa`**: Ensures compatibility with Linux file permissions and efficient storage of extended attributes.
- **`relatime=on`**: Minimizes disk writes by updating access times only when necessary.
- **`autotrim=on`**: Maintains SSD performance by automatically freeing unused space.
- **`compatibility=openzfs-2.1-linux`**: Ensures the pool is compatible with the specified OpenZFS version on Linux.
- **`-m none`**: Prevents conflicts with the root filesystem's mount points during installation.

### 11. Create ZFS Datasets for Home + Root

ZFS refers to datasets and filesystems as synonyms

1. Create datasets for home and root - ROOT is a container, 
```sh
zfs create -o mountpoint=none zroot/ROOT
zfs create -o mountpoint=/ -o canmount=noauto zroot/ROOT/${ID}
zfs create -o mountpoint=/home zroot/home
```

2. Set Boot Dataset/filesystem

`zpool set bootfs=zroot/ROOT/${ID} zroot`


#LearnMore:
These commands create a ZFS dataset structure for a root filesystem and a separate `/home` directory, typically used when setting up a system with ZFS as the root filesystem. Here's a detailed explanation of each command:

1. **`zfs create -o mountpoint=none zroot/ROOT`**:
   - Creates a ZFS dataset named `zroot/ROOT`.
   - The `-o mountpoint=none` option specifies that this dataset should not be directly mounted. This is usually done because `zroot/ROOT` acts as a container for other datasets that will be used as root filesystems (e.g., different system versions or installations).

2. **`zfs create -o mountpoint=/ -o canmount=noauto zroot/ROOT/${ID}`**:
   - Creates a ZFS dataset under `zroot/ROOT` with the name `${ID}` ID = debian in our case
   - `-o mountpoint=/` sets this dataset to be mounted at the root (`/`) of the filesystem.
   - `-o canmount=noauto` means this dataset will not be automatically mounted at boot but can be mounted manually. This is typically used to manage boot environments, allowing control over which dataset is mounted as the root filesystem.

3. **`zfs create -o mountpoint=/home zroot/home`**:
   - Creates a ZFS dataset named `zroot/home`.
   - `-o mountpoint=/home` sets this dataset to be mounted at `/home`, providing a separate filesystem for user home directories. This allows flexibility and easier management, such as snapshotting, for user data independently from the root filesystem.

Overall, these commands set up a ZFS pool (`zroot`) structure where you can manage multiple boot environments (under `zroot/ROOT`) and a separate `/home` dataset for user data.

### 12. Export then Reimport the pool

```sh
zpool export zroot
zpool import -N -R /mnt zroot
zfs mount zroot/ROOT/${ID}
zfs mount zroot/home
```

Got a weird error here:
1. when I export zroot, the machine forgets about the pool
2. the pool was still there but I had to update the line to go fetch it directly from the device
`zpool import -N -R /mnt -d /dev/nvme0n1p1 zroot`

Verify everything mounted correctly:
```sh
mount | grep mnt
#Output
zroot/ROOT/debian on /mnt type zfs (rw,relatime,xattr,posixacl)
zroot/home on /mnt/home type zfs (rw,relatime,xattr,posixacl)
```

### 13. Update device symlinks

`udevadm trigger`

The udevadm trigger command is used to instruct the Linux udev system to re-process device events, which can help update the system's view of hardware devices. Specifically, it scans for hardware changes and generates events as if the devices were newly connected or detected.

### 14. Install an EXTREMELY MINIMAL Debian on mnt
`debootstrap bookworm /mnt`

### 15. Copy config files

cp /etc/hostid /mnt/etc
cp /etc/resolv.conf /mnt/etc

### 16. Chroot into the new Debian Install

After this step, we're running on the actual system disk with the 
debian install we just set up.

```sh 
mount -t proc proc /mnt/proc
mount -t sysfs sys /mnt/sys
mount -B /dev /mnt/dev
mount -t devpts pts /mnt/dev/pts
chroot /mnt /bin/bash
```

### 17. Basic Configuration of Debian 

1. hostname
echo 'YOURHOSTNAME' > /etc/hostname
echo -e '127.0.1.1\tYOURHOSTNAME' >> /etc/hosts

2. set password
passwd

3. Apt Sources

```txt
deb http://deb.debian.org/debian bookworm main contrib non-free-firmware
deb-src http://deb.debian.org/debian bookworm main contrib non-free-firmware

deb http://deb.debian.org/debian-security bookworm-security main contrib non-free-firmware
deb-src http://deb.debian.org/debian-security/ bookworm-security main contrib non-free-firmware

deb http://deb.debian.org/debian bookworm-updates main contrib non-free-firmware
deb-src http://deb.debian.org/debian bookworm-updates main contrib non-free-firmware

deb http://deb.debian.org/debian bookworm-backports main contrib non-free-firmware
deb-src http://deb.debian.org/debian bookworm-backports main contrib non-free-firmware
```

4. Additional base packages
apt install locales keyboard-configuration console-setup
dpkg-reconfigure locales tzdata keyboard-configuration console-setup

### 18. ZFS setup

1. install necessary packages
`apt install linux-headers-amd64 linux-image-amd64 zfs-initramfs dosfstools`

2. Force something
echo "REMAKE_INITRD=yes" > /etc/dkms/zfs.conf

#LearnMore: 
Here’s a one-line description of each package and the `echo` command:

1. **`linux-headers-amd64`**: Provides the header files for building kernel modules on AMD64 systems.
2. **`linux-image-amd64`**: Installs the latest Linux kernel image for AMD64 systems.
3. **`zfs-initramfs`**: Adds ZFS support to the initial RAM filesystem (initramfs) for booting from ZFS pools.
4. **`dosfstools`**: Contains tools for creating and managing FAT filesystems (commonly used for EFI partitions).
5. **`echo "REMAKE_INITRD=yes" > /etc/dkms/zfs.conf`**: Configures DKMS to rebuild the initramfs automatically when ZFS kernel modules are updated.

The line `echo "REMAKE_INITRD=yes" > /etc/dkms/zfs.conf` is necessary to ensure that the `initramfs` (initial RAM filesystem) is automatically rebuilt whenever ZFS kernel modules are updated or recompiled by DKMS (Dynamic Kernel Module Support). 

- **Booting from ZFS**: If you're using ZFS for your root filesystem, the `initramfs` must include the ZFS kernel modules so that the system can mount the root filesystem during boot. 
- **Kernel Updates**: When the kernel is updated, DKMS automatically rebuilds kernel modules (including ZFS). However, without the `REMAKE_INITRD=yes` setting, the `initramfs` might not be updated to include the newly built ZFS modules, potentially leading to boot failures.
- **Ensures Consistency**: By setting `REMAKE_INITRD=yes`, the system ensures that any changes to the ZFS modules are properly included in the `initramfs`, maintaining a consistent environment for booting from a ZFS pool.

### 19. Enable Systemd ZFS Services

```sh
systemctl enable zfs.target
systemctl enable zfs-import-cache
systemctl enable zfs-mount
systemctl enable zfs-import.target
```

#LearnMore:
1. **`systemctl enable zfs.target`**: Enables the main ZFS target, coordinating the activation of ZFS-related services during boot.
2. **`systemctl enable zfs-import-cache`**: Enables the service to import ZFS pools based on a cache file (`/etc/zfs/zpool.cache`) during boot.
3. **`systemctl enable zfs-mount`**: Enables the service to mount ZFS datasets automatically at boot.
4. **`systemctl enable zfs-import.target`**: Enables the target that depends on importing ZFS pools, ensuring they are imported before other ZFS services start.

### 20. Rebuild initramfs
update-initramfs -c -k all

### 21. Setup and Install ZFSBootMenu
#### 21.1. Assign command-line arguments to be used when booting the final kernel. 
Because ZFS properties are inherited, assign the common properties to the ROOT dataset so all children will inherit common arguments by default.

`zfs set org.zfsbootmenu:commandline="quiet" zroot/ROOT`

#### 21.2. Create VFat filesystem for booting

`mkfs.vfat -F32 "$BOOT_DEVICE"`

```sh 
cat << EOF >> /etc/fstab
$( blkid | grep "$BOOT_DEVICE" | cut -d ' ' -f 2 ) /boot/efi vfat defaults 0 0
EOF

mkdir -p /boot/efi
mount /boot/efi
```

#LearnMore
1. **`mkfs.vfat -F32 "$BOOT_DEVICE"`**: Formats the specified device as a FAT32 filesystem, suitable for an EFI system partition.
2. **`cat << EOF >> /etc/fstab ... EOF`**: Appends an entry to `/etc/fstab` to mount the EFI partition automatically at boot.
3. **`mkdir -p /boot/efi`**: Creates the mount point directory for the EFI partition if it doesn't already exist.
4. **`mount /boot/efi`**: Mounts the EFI partition at `/boot/efi` to make it accessible.

#### 21.3. Install ZFSBootMenu
`apt install curl` - we use CURL to go fetch the zfsbootmenu online

Fetch a prebuilt ZFSBootMenu EFI executable, saving it to the EFI system partition:

```sh
mkdir -p /boot/efi/EFI/ZBM
curl -o /boot/efi/EFI/ZBM/VMLINUZ.EFI -L https://get.zfsbootmenu.org/efi
cp /boot/efi/EFI/ZBM/VMLINUZ.EFI /boot/efi/EFI/ZBM/VMLINUZ-BACKUP.EFI
```
IF YOUR SYSTEM NEEDS DEFAULT PATH for BOOT
sudo cp /boot/efi/EFI/ZBM/VMLINUZ.EFI /boot/efi/EFI/BOOT/bootx64.efi

#### 21.4. Confgiure Boot Entries
mount -t efivarfs efivarfs /sys/firmware/efi/efivars


1. **`mount -t efivarfs efivarfs /sys/firmware/efi/efivars`**: Mounts the EFI variable filesystem (`efivarfs`), allowing access to UEFI firmware variables under `/sys/firmware/efi/efivars`, which is necessary for managing UEFI boot entries.


#### 21.5. install and configure efi boot manager
1. apt install efibootmgr

2. 
efibootmgr -c -d "$BOOT_DISK" -p "$BOOT_PART" \
  -L "ZFSBootMenu (Backup)" \
  -l '\EFI\ZBM\VMLINUZ-BACKUP.EFI'

efibootmgr -c -d "$BOOT_DISK" -p "$BOOT_PART" \
  -L "ZFSBootMenu" \
  -l '\EFI\ZBM\VMLINUZ.EFI'

YOU MAY NEED TO ADD a default path

sudo cp /mnt/boot/efi/EFI/ZBM/VMLINUZ.EFI /mnt/boot/efi/EFI/BOOT/bootx64.efi

efibootmgr -c -d "$BOOT_DISK" -p "$BOOT_PART" \
  -L "ZFSBootMenu" \
  -l '\EFI\BOOT\bootx64.efi'


#LearnMore:

**`apt install efibootmgr`**: Installs `efibootmgr`, a tool used to create, modify, and manage UEFI boot entries in the system's firmware.
Here’s a breakdown of each `efibootmgr` command:

1. **First `efibootmgr` Command:**
   - **`efibootmgr -c -d "$BOOT_DISK" -p "$BOOT_PART"`**: Creates a new UEFI boot entry (`-c`) on the specified boot disk (`-d`) and partition (`-p`).
   - **`-L "ZFSBootMenu (Backup)"`**: Sets the label of this boot entry as "ZFSBootMenu (Backup)" for identification in the UEFI boot menu.
   - **`-l '\EFI\ZBM\VMLINUZ-BACKUP.EFI'`**: Specifies the path to the EFI executable to be used for booting, in this case, a backup kernel located on the EFI partition.

2. **Second `efibootmgr` Command:**
   - **`efibootmgr -c -d "$BOOT_DISK" -p "$BOOT_PART"`**: Similar to the first command, creates another UEFI boot entry on the same boot disk and partition.
   - **`-L "ZFSBootMenu"`**: Sets the label of this boot entry as "ZFSBootMenu."
   - **`-l '\EFI\ZBM\VMLINUZ.EFI'`**: Specifies the path to the main EFI executable for booting.

These commands add boot entries to the UEFI firmware to allow selection between the main and backup boot options provided by ZFSBootMenu.

#### 21.6. Sign the boot manager for SecureBoot
shim sign ?? need to figure this out
No, your chatgpt history doesn't have the right answer for this. I'm not sure how shimsign works. Look into it??

You can turn off secureboot from an ubunut live install media 
- this might work

### 22. Installing Full Debian Systems


apt install systemd-timesyncd
apt dist-upgrade --yes
tasksel --new-install
apt install net-tools

Creat User
username=YOUR_USERNAME
adduser $username

cp -a /etc/skel/. /home/$username
chown -R $username:$username /home/$username
usermod -a -G audio,cdrom,dip,floppy,netdev,plugdev,sudo,video $username

#### 22.2 Additional networking diagnostic tools

install all: apt install iproute2 isc-dhcp-client iputils-ping traceroute curl wget dnsutils ethtool ifupdown tcpdump nmap nano vim

1. **`iproute2`**: Provides tools like `ip` and `ss` for more advanced network configuration and diagnostics.
   ```sh
   apt install iproute2
   ```

2. **`dhclient`**: Installs the DHCP client to automatically obtain an IP address if not already present.
   ```sh
   apt install isc-dhcp-client
   ```

3. **`ping` & `traceroute`**: Essential for testing connectivity to other network devices and diagnosing network paths.
   ```sh
   apt install iputils-ping traceroute
   ```

4. **`curl` and `wget`**: Useful for testing HTTP/S connectivity and downloading files directly from the command line.
   ```sh
   apt install curl wget
   ```

5. **`dnsutils`**: Contains `dig` and `nslookup` for diagnosing DNS issues.
   ```sh
   apt install dnsutils
   ```

6. **`ethtool`**: Allows you to view and change Ethernet device settings, useful for checking link status and speed.
   ```sh
   apt install ethtool
   ```

7. **`ifupdown`**: Provides commands like `ifup` and `ifdown` for manually managing network interfaces.
   ```sh
   apt install ifupdown
   ```

8. **`tcpdump`**: A powerful tool for capturing network packets to analyze traffic and troubleshoot network problems.
   ```sh
   apt install tcpdump
   ```

9. **`nmap`**: Useful for network scanning to check for open ports and connectivity to other devices.
   ```sh
   apt install nmap
   ```

#### 22.3. other stuff
apt install htop vim openssh-server git tmux

### 23. Exit and reboot
exit

Now we're back in the live install machine

mount | grep -v zfs | tac | awk '/\/mnt/ {print $3}' | \
    xargs -i{} umount -lf {}
zpool export -a

