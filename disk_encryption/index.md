

## General

### Variables

```sh
DISK="/dev/sdc"
PARTITION="/dev/sdc2"
PARTITION_NAME="Backup"
PARTITION_MOUNT_PATH="temp_mnt"
```

### Veryfing progress

```sh
# Shows disks and partitions 
lsblk
```

## Setting up the encrypted disk:

### 1. Encryption Layer Setup

```sh
# Create a new partition
# This process is interactive!
sudo fdisk "$DISK"
# Possible flow:
#   #`d`            #//delete partition
#   #   #Select Partition "$PARTITION"
#   #`n`            #//new partition
#   #   #1          #//Primary
#   #   #Select Partition "$PARTITION"
#   #   #Up to you
#   #   #Up to you
#   #   #Up to you
#   #   ...
#   #`q`            #//quit

# Make the partition encrypted
# This process is interactive!
sudo cryptsetup luksFormat "$PARTITION"
# Possible flow:
#   #YES
#   #PASSWORD       #//MANDATORY!
#   #PASSWORD       #//MANDATORY!
#   #...

```

### 2. File System Setup *- ext4 example*

```sh
# Open a mapper to the decrypted partition
sudo cryptsetup open "$PARTITION" "$PARTITION_NAME"

# Chosing the filesystem (ext4 presented here)
# This process is interactive!
sudo mkfs.ext4 "/dev/mapper/$PARTITION_NAME"
# ...

# Close the mapper to the decrypted partition
sudo cryptsetup close "$PARTITION_NAME"
```

## Daily use

### Mounting specific disk partition

```sh
# Open a mapper to the decrypted partition
sudo cryptsetup open "$PARTITION" "$PARTITION_NAME"

# OPTIONAL LATER: Make sure that the $PARTITION_MOUNT_PATH exist
mkdir -p "$PARTITION_MOUNT_PATH"

# Mount the mapper
sudo mount "/dev/mapper/$PARTITION_NAME" "$PARTITION_MOUNT_PATH"
```

### Making the mount accessible by a normal user

```sh
sudo chown -R "$(id -un):$(id -gn)" "$PARTITION_MOUNT_PATH"
sudo chmod 766 "$PARTITION_MOUNT_PATH"
```

### Unmounting specific disk partition

```sh
# Unmount the mapper
sudo umount "$PARTITION_MOUNT_PATH"

# Close the mapper to the decrypted partition
sudo cryptsetup close "$PARTITION_NAME"
```