# Persistence Storage for a bootable Kali USB drive

## Without Encryption

1. Create a Bootable Kali USB using `Rufus` or `Win32 Disk Imager` or `Universal USB Installer`.
2. Use `parted` to create an extra partition. You can use `fdisk -l` command to check the current partitions of the drives.
```
fdisk -l

parted

print devices
select /dev/sdb
print

mkpart primary <start> <end>
quit

fdisk -l
```

3. Create an `ext4` file system in the partition and label it “Persist”.
```
mkfs.ext4 -L Persist /dev/sdb3
e2label /dev/sdb3 Persist
```

4. Mount the partition and create a `persistence.conf` so changes persist across reboots. Finally, unmount the partition.
```
mkdir -p /mnt/Persist
mount /dev/sdb3 /mnt/Persist
echo "/ union" > /mnt/Persist/persistence.conf
umount /dev/sdb3
```

## With LUKS Encryption

Follow Steps 1 and 2 from above and then follow the steps below to create an encrypted persistence.

1. Encrypt the partition with LUKS.

`cryptsetup --verbose --verify-passphrase luksFormat /dev/sdb3`

2. Open the encrypted partition.

`cryptsetup luksOpen /dev/sdb3 encPersist`

3. Create an `ext4` file system in the partition and label it “encPersist”.
```
mkfs.ext4 -L encPersist /dev/sdb3
e2label /dev/sdb3 encPersist
```

4. Mount the partition and create a `persistence.conf` so changes persist across reboots. Finally, unmount the partition.
```
mkdir -p /mnt/encPersist
mount /dev/sdb3 /mnt/encPersist
echo "/ union" > /mnt/encPersist/persistence.conf
umount /dev/sdb3
```
