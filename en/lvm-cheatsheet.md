````markdown
# 💾 LVM Cheatsheet (Logical Volume Manager)

## 📚 Basic Concepts

- **Physical Volume (PV)**: Physical disk or partition
- **Volume Group (VG)**: Group of PVs
- **Logical Volume (LV)**: Logical partition created from VG
- **Physical Extent (PE)**: Smallest unit of allocation in LVM

------------------------------------------------------------------------

## 🔍 View Current State

``` bash
pvs                 # Summary of Physical Volumes
vgs                 # Summary of Volume Groups
lvs                 # Summary of Logical Volumes

pvdisplay           # Complete details of PVs
vgdisplay           # Complete details of VGs
lvdisplay           # Complete details of LVs

lsblk               # Tree view of disks and partitions
df -h               # Used filesystem space
```

------------------------------------------------------------------------

## 🆕 Create Physical Volume (PV)

``` bash
pvcreate /dev/sdb                    # Create PV from entire disk
pvcreate /dev/sdc1                   # Create PV from partition
pvcreate /dev/sdb /dev/sdc           # Create multiple PVs simultaneously

pvremove /dev/sdb                    # Remove PV (must be removed from VG)
```

------------------------------------------------------------------------

## 📦 Create Volume Group (VG)

``` bash
vgcreate my_vg /dev/sdb              # Create VG from one PV
vgcreate my_vg /dev/sdb /dev/sdc     # Create VG from multiple PVs

vgextend my_vg /dev/sdd              # Add PV to existing VG
vgreduce my_vg /dev/sdd              # Remove PV from VG

vgremove my_vg                       # Completely remove VG (must be empty)
vgrename my_vg new_vg                # Rename VG
```

------------------------------------------------------------------------

## 🎯 Create Logical Volume (LV)

``` bash
lvcreate -L 10G -n my_lv my_vg       # Create LV with specific size
lvcreate -l 100%FREE -n my_lv my_vg  # Use all remaining space
lvcreate -l 50%VG -n my_lv my_vg     # Use 50% of VG

lvremove /dev/my_vg/my_lv            # Remove LV
lvrename my_vg my_lv new_lv          # Rename LV
```

------------------------------------------------------------------------

## 📏 Resize

### Extend LV

``` bash
lvextend -L +5G /dev/my_vg/my_lv     # Increase by 5 gigabytes
lvextend -L 20G /dev/my_vg/my_lv     # Set total size to 20 gig
lvextend -l +100%FREE /dev/my_vg/my_lv # Use all free space

# Extend filesystem simultaneously
lvextend -r -L +5G /dev/my_vg/my_lv  # With -r filesystem is also resized

# Or manually:
resize2fs /dev/my_vg/my_lv           # For ext4
xfs_growfs /mnt/mount_point          # For xfs
```

### Shrink LV (Dangerous - ext4 only)

``` bash
umount /mnt/my_lv                    # First unmount
e2fsck -f /dev/my_vg/my_lv           # Check filesystem
resize2fs /dev/my_vg/my_lv 8G        # Shrink filesystem
lvreduce -L 8G /dev/my_vg/my_lv      # Shrink LV
mount /dev/my_vg/my_lv /mnt/my_lv    # Mount again
```

------------------------------------------------------------------------

## 📸 Snapshot

``` bash
lvcreate -L 2G -s -n my_snapshot /dev/my_vg/my_lv  # Create snapshot with 2 gig size

lvs                                  # View snapshot and usage percentage

# Revert to snapshot
lvconvert --merge /dev/my_vg/my_snapshot  # Applied after reboot

# Remove snapshot
lvremove /dev/my_vg/my_snapshot
```

------------------------------------------------------------------------

## 💧 Thin Provisioning

``` bash
# Create Thin Pool
lvcreate -L 50G --thinpool my_thin_pool my_vg

# Create Thin Volume
lvcreate -V 100G --thin -n thin_vol1 my_vg/my_thin_pool

# View status
lvs -a                               # Display all volumes including thin pool
```

------------------------------------------------------------------------

## 🔧 Advanced Management and Configuration

``` bash
# Change PE size (only during VG creation)
vgcreate -s 8M my_vg /dev/sdb        # Set PE size to 8MB

# Activate/Deactivate
vgchange -a y my_vg                  # Activate VG
vgchange -a n my_vg                  # Deactivate VG

# Export/Import VG
vgexport my_vg                       # Export VG
vgimport my_vg                       # Import VG

# Scan disks
pvscan                               # Scan PVs
vgscan                               # Scan VGs
lvscan                               # Scan LVs
```

------------------------------------------------------------------------

## 🔄 Data Migration

``` bash
# Move data from one PV to other PVs in VG
pvmove /dev/sdb                      # Transfer all data from sdb

pvmove /dev/sdb /dev/sdc             # Transfer from sdb to sdc

pvmove -n /dev/my_vg/my_lv /dev/sdb /dev/sdc  # Transfer only specific LV
```

------------------------------------------------------------------------

## 💾 Backup & Restore

``` bash
# Backup LVM configuration
vgcfgbackup my_vg                    # Save to /etc/lvm/backup/
vgcfgbackup -f /backup/my_vg.conf my_vg  # Save to custom path

# Restore configuration
vgcfgrestore my_vg                   # Restore from automatic backup
vgcfgrestore -f /backup/my_vg.conf my_vg  # Restore from specific file

# Backup actual data (with dd)
dd if=/dev/my_vg/my_lv of=/backup/my_lv.img bs=4M status=progress
```

------------------------------------------------------------------------

## 🛠 Troubleshooting

``` bash
# Health check
lvs -a -o +devices                   # Display underlying devices
vgs -o +pv_name                      # Display PVs of each VG

# Activate inactive LV
lvchange -a y /dev/my_vg/my_lv

# Repair metadata
vgck my_vg                           # Check consistency

# LVM logs
journalctl -u lvm2*                  # View system logs
dmesg | grep -i lvm                  # Kernel messages
```

------------------------------------------------------------------------

## 📝 Complete Process: From Zero to Running

``` bash
# 1. Prepare disks
fdisk /dev/sdb                       # Partition (type: 8e Linux LVM)

# 2. Create PV
pvcreate /dev/sdb1 /dev/sdc1

# 3. Create VG
vgcreate data_vg /dev/sdb1 /dev/sdc1

# 4. Create LV
lvcreate -L 50G -n app_data data_vg

# 5. Create filesystem
mkfs.ext4 /dev/data_vg/app_data

# 6. Mount
mkdir -p /mnt/app_data
mount /dev/data_vg/app_data /mnt/app_data

# 7. Permanent mount in /etc/fstab
echo "/dev/data_vg/app_data /mnt/app_data ext4 defaults 0 2" >> /etc/fstab
```

------------------------------------------------------------------------

## 🏭 Production Best Practices

- ✅ Always backup before sensitive operations
- ✅ Use snapshots to test changes
- ✅ Keep sufficient free space in VG (at least 20%)
- ✅ Use standard LV naming convention
- ✅ Monitor snapshot space (may fill up)
- ⚠️ Shrinking LV is dangerous - always have backups
- ⚠️ XFS cannot be shrunk (only grows)
- 📊 Use `lvs`, `vgs`, `pvs` for monitoring

------------------------------------------------------------------------

## 🚀 Advanced Tips

### RAID in LVM

``` bash
# Create LV with Mirror (RAID1)
lvcreate --type raid1 -m 1 -L 10G -n mirror_lv my_vg

# Create LV with Stripe (RAID0)
lvcreate --type raid0 -i 2 -L 10G -n stripe_lv my_vg

# Check RAID status
lvs -a -o +raid_sync_action,raid_mismatch_count
```

### Cache Volume

``` bash
# Create cache pool with SSD
lvcreate -L 10G -n cache_pool my_vg /dev/nvme0n1
lvconvert --type cache-pool my_vg/cache_pool

# Add cache to LV
lvconvert --type cache --cachepool my_vg/cache_pool my_vg/my_lv
```

------------------------------------------------------------------------

## 🔐 Security: Encryption with LUKS

``` bash
# Encrypt LV
cryptsetup luksFormat /dev/my_vg/my_lv
cryptsetup luksOpen /dev/my_vg/my_lv my_lv_encrypted

# Create filesystem on encrypted volume
mkfs.ext4 /dev/mapper/my_lv_encrypted
mount /dev/mapper/my_lv_encrypted /mnt/secure
```

------------------------------------------------------------------------

## 📊 Monitoring and Information

``` bash
# Display information with custom format
pvs -o pv_name,vg_name,pv_size,pv_free
vgs -o vg_name,vg_size,vg_free,lv_count
lvs -o lv_name,vg_name,lv_size,lv_attr,devices

# Display all possible fields
pvs -o help
vgs -o help
lvs -o help
```

````
