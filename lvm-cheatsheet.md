# 💾 LVM Cheatsheet (Logical Volume Manager)

## 📚 مفاهیم اولیه

- **Physical Volume (PV)**: دیسک فیزیکی یا پارتیشن
- **Volume Group (VG)**: گروهی از PVها
- **Logical Volume (LV)**: پارتیشن منطقی که از VG ساخته می‌شود
- **Physical Extent (PE)**: کوچکترین واحد تخصیص در LVM

------------------------------------------------------------------------

## 🔍 مشاهده وضعیت فعلی

``` bash
pvs                 # خلاصه Physical Volumes
vgs                 # خلاصه Volume Groups
lvs                 # خلاصه Logical Volumes

pvdisplay           # جزئیات کامل PVها
vgdisplay           # جزئیات کامل VGها
lvdisplay           # جزئیات کامل LVها

lsblk               # نمایش درختی دیسک‌ها و پارتیشن‌ها
df -h               # فضای استفاده‌شده filesystem
```

------------------------------------------------------------------------

## 🆕 ساخت Physical Volume (PV)

``` bash
pvcreate /dev/sdb                    # ساخت PV از دیسک کامل
pvcreate /dev/sdc1                   # ساخت PV از پارتیشن
pvcreate /dev/sdb /dev/sdc           # ساخت چند PV همزمان

pvremove /dev/sdb                    # حذف PV (باید از VG خارج شود)
```

------------------------------------------------------------------------

## 📦 ساخت Volume Group (VG)

``` bash
vgcreate my_vg /dev/sdb              # ساخت VG از یک PV
vgcreate my_vg /dev/sdb /dev/sdc     # ساخت VG از چند PV

vgextend my_vg /dev/sdd              # اضافه کردن PV به VG موجود
vgreduce my_vg /dev/sdd              # حذف PV از VG

vgremove my_vg                       # حذف کامل VG (باید خالی باشد)
vgrename my_vg new_vg                # تغییر نام VG
```

------------------------------------------------------------------------

## 🎯 ساخت Logical Volume (LV)

``` bash
lvcreate -L 10G -n my_lv my_vg       # ساخت LV با حجم مشخص
lvcreate -l 100%FREE -n my_lv my_vg  # استفاده از تمام فضای باقی‌مانده
lvcreate -l 50%VG -n my_lv my_vg     # استفاده از 50 درصد VG

lvremove /dev/my_vg/my_lv            # حذف LV
lvrename my_vg my_lv new_lv          # تغییر نام LV
```

------------------------------------------------------------------------

## 📏 تغییر اندازه (Resize)

### بزرگ کردن LV

``` bash
lvextend -L +5G /dev/my_vg/my_lv     # افزایش 5 گیگابایت
lvextend -L 20G /dev/my_vg/my_lv     # تنظیم سایز کل به 20 گیگ
lvextend -l +100%FREE /dev/my_vg/my_lv # استفاده از تمام فضای آزاد

# بزرگ کردن filesystem همزمان
lvextend -r -L +5G /dev/my_vg/my_lv  # با -r فایل‌سیستم هم resize می‌شود

# یا دستی:
resize2fs /dev/my_vg/my_lv           # برای ext4
xfs_growfs /mnt/mount_point          # برای xfs
```

### کوچک کردن LV (خطرناک - فقط ext4)

``` bash
umount /mnt/my_lv                    # ابتدا unmount کن
e2fsck -f /dev/my_vg/my_lv           # چک فایل‌سیستم
resize2fs /dev/my_vg/my_lv 8G        # کوچک کردن filesystem
lvreduce -L 8G /dev/my_vg/my_lv      # کوچک کردن LV
mount /dev/my_vg/my_lv /mnt/my_lv    # دوباره mount کن
```

------------------------------------------------------------------------

## 📸 Snapshot

``` bash
lvcreate -L 2G -s -n my_snapshot /dev/my_vg/my_lv  # ساخت snapshot با حجم 2 گیگ

lvs                                  # مشاهده snapshot و درصد استفاده

# بازگشت به snapshot
lvconvert --merge /dev/my_vg/my_snapshot  # بعد از ریبوت اعمال می‌شود

# حذف snapshot
lvremove /dev/my_vg/my_snapshot
```

------------------------------------------------------------------------

## 💧 Thin Provisioning

``` bash
# ساخت Thin Pool
lvcreate -L 50G --thinpool my_thin_pool my_vg

# ساخت Thin Volume
lvcreate -V 100G --thin -n thin_vol1 my_vg/my_thin_pool

# مشاهده وضعیت
lvs -a                               # نمایش همه volumeها شامل thin pool
```

------------------------------------------------------------------------

## 🔧 مدیریت و تنظیمات پیشرفته

``` bash
# تغییر اندازه PE (فقط در ساخت VG)
vgcreate -s 8M my_vg /dev/sdb        # تنظیم PE size به 8MB

# فعال/غیرفعال کردن
vgchange -a y my_vg                  # فعال‌سازی VG
vgchange -a n my_vg                  # غیرفعال‌سازی VG

# Export/Import VG
vgexport my_vg                       # export کردن VG
vgimport my_vg                       # import کردن VG

# Scan کردن دیسک‌ها
pvscan                               # اسکن PVها
vgscan                               # اسکن VGها
lvscan                               # اسکن LVها
```

------------------------------------------------------------------------

## 🔄 جابجایی داده (Data Migration)

``` bash
# جابجایی داده از یک PV به سایر PVهای VG
pvmove /dev/sdb                      # انتقال تمام داده از sdb

pvmove /dev/sdb /dev/sdc             # انتقال از sdb به sdc

pvmove -n /dev/my_vg/my_lv /dev/sdb /dev/sdc  # انتقال فقط یک LV خاص
```

------------------------------------------------------------------------

## 💾 Backup & Restore

``` bash
# Backup تنظیمات LVM
vgcfgbackup my_vg                    # ذخیره در /etc/lvm/backup/
vgcfgbackup -f /backup/my_vg.conf my_vg  # ذخیره در مسیر دلخواه

# Restore تنظیمات
vgcfgrestore my_vg                   # بازیابی از backup اتوماتیک
vgcfgrestore -f /backup/my_vg.conf my_vg  # بازیابی از فایل خاص

# Backup داده واقعی (با dd)
dd if=/dev/my_vg/my_lv of=/backup/my_lv.img bs=4M status=progress
```

------------------------------------------------------------------------

## 🛠 Troubleshooting

``` bash
# بررسی سلامت
lvs -a -o +devices                   # نمایش دستگاه‌های زیربنایی
vgs -o +pv_name                      # نمایش PVهای هر VG

# فعال‌سازی LV غیرفعال
lvchange -a y /dev/my_vg/my_lv

# تعمیر متادیتا
vgck my_vg                           # چک کردن consistency

# لاگ‌های LVM
journalctl -u lvm2*                  # مشاهده لاگ‌های سیستمی
dmesg | grep -i lvm                  # پیام‌های kernel
```

------------------------------------------------------------------------

## 📝 فرآیند کامل: از صفر تا اجرا

``` bash
# 1. آماده‌سازی دیسک‌ها
fdisk /dev/sdb                       # پارتیشن‌بندی (نوع: 8e Linux LVM)

# 2. ساخت PV
pvcreate /dev/sdb1 /dev/sdc1

# 3. ساخت VG
vgcreate data_vg /dev/sdb1 /dev/sdc1

# 4. ساخت LV
lvcreate -L 50G -n app_data data_vg

# 5. ساخت فایل‌سیستم
mkfs.ext4 /dev/data_vg/app_data

# 6. Mount کردن
mkdir -p /mnt/app_data
mount /dev/data_vg/app_data /mnt/app_data

# 7. Mount دائمی در /etc/fstab
echo "/dev/data_vg/app_data /mnt/app_data ext4 defaults 0 2" >> /etc/fstab
```

------------------------------------------------------------------------

## 🏭 Production Best Practices

- ✅ همیشه قبل از عملیات حساس، backup بگیر
- ✅ از snapshot برای تست تغییرات استفاده کن
- ✅ فضای خالی کافی در VG نگه دار (حداقل 20%)
- ✅ از LV naming convention استاندارد استفاده کن
- ✅ monitoring فضای snapshot (ممکن است پر شود)
- ⚠️ کوچک کردن LV خطرناک است - همیشه backup داشته باش
- ⚠️ XFS را نمی‌توان کوچک کرد (فقط بزرگ می‌شود)
- 📊 از `lvs`, `vgs`, `pvs` برای monitoring استفاده کن

------------------------------------------------------------------------

## 🚀 نکات پیشرفته

### RAID در LVM

``` bash
# ساخت LV با Mirror (RAID1)
lvcreate --type raid1 -m 1 -L 10G -n mirror_lv my_vg

# ساخت LV با Stripe (RAID0)
lvcreate --type raid0 -i 2 -L 10G -n stripe_lv my_vg

# بررسی وضعیت RAID
lvs -a -o +raid_sync_action,raid_mismatch_count
```

### Cache Volume

``` bash
# ساخت cache pool با SSD
lvcreate -L 10G -n cache_pool my_vg /dev/nvme0n1
lvconvert --type cache-pool my_vg/cache_pool

# اضافه کردن cache به LV
lvconvert --type cache --cachepool my_vg/cache_pool my_vg/my_lv
```

------------------------------------------------------------------------

## 🔐 امنیت: رمزنگاری با LUKS

``` bash
# رمزنگاری LV
cryptsetup luksFormat /dev/my_vg/my_lv
cryptsetup luksOpen /dev/my_vg/my_lv my_lv_encrypted

# ساخت filesystem روی volume رمزشده
mkfs.ext4 /dev/mapper/my_lv_encrypted
mount /dev/mapper/my_lv_encrypted /mnt/secure
```

------------------------------------------------------------------------

## 📊 Monitoring و اطلاعات

``` bash
# نمایش اطلاعات با فرمت سفارشی
pvs -o pv_name,vg_name,pv_size,pv_free
vgs -o vg_name,vg_size,vg_free,lv_count
lvs -o lv_name,vg_name,lv_size,lv_attr,devices

# نمایش همه فیلدهای ممکن
pvs -o help
vgs -o help
lvs -o help
```
