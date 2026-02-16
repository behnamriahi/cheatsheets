# چیت‌شیت Chrony

Chrony یک پیاده‌سازی از پروتکل Network Time Protocol (NTP) است که ساعت سیستم را با دقت بالا همگام‌سازی می‌کند.

## نصب

```bash
# Debian/Ubuntu
sudo apt install chrony

# RHEL/CentOS/Fedora
sudo dnf install chrony

# فعال‌سازی و راه‌اندازی سرویس
sudo systemctl enable chronyd
sudo systemctl start chronyd
```

## فایل پیکربندی

فایل اصلی پیکربندی: `/etc/chrony/chrony.conf` یا `/etc/chrony.conf`

### گزینه‌های رایج پیکربندی

#### منابع زمان

```bash
# سرورهای NTP
server 0.pool.ntp.org iburst
server 1.pool.ntp.org iburst
server 2.pool.ntp.org iburst
server 3.pool.ntp.org iburst

# مجموعه‌ای از سرورها
pool pool.ntp.org iburst maxsources 4

# اولویت دادن به سرور خاص
server ntp.example.com iburst prefer
```

#### کنترل دسترسی

```bash
# اجازه دسترسی کلاینت NTP از شبکه محلی
allow 192.168.1.0/24
allow 10.0.0.0/8

# اجازه به همه (برای امنیت توصیه نمی‌شود)
allow all

# رد دسترسی شبکه خاص
deny 192.168.2.0/24
```

#### منبع زمان محلی

```bash
# عمل به عنوان سرور stratum 10 هنگام قطع ارتباط
local stratum 10

# سطح stratum جدی‌تر
local stratum 8
```

#### انحراف و تصحیح

```bash
# فایل ذخیره داده‌های انحراف
driftfile /var/lib/chrony/drift

# اعمال تنظیم پلکانی اگر اختلاف بیشتر از 1 ثانیه باشد
makestep 1.0 3

# همگام‌سازی ساعت سخت‌افزاری (RTC)
rtcsync
```

#### لاگ‌گیری

```bash
# دایرکتوری لاگ
logdir /var/log/chrony

# ثبت اندازه‌گیری‌ها، آمار، و ردیابی
log measurements statistics tracking
```

#### تنظیمات شبکه

```bash
# شماره پورت (پیش‌فرض 123)
port 123

# اتصال به رابط خاص
bindaddress 192.168.1.10

# پورت دستوری (پیش‌فرض 323)
cmdport 323
```

## دستورات Chronyc

`chronyc` رابط خط فرمان برای chrony است.

### وضعیت و نظارت

```bash
# نمایش وضعیت فعلی
chronyc tracking

# نمایش منابع زمان
chronyc sources

# نمایش منابع زمان با خروجی کامل
chronyc sources -v

# نمایش جزئیات منابع NTP
chronyc sourcestats

# نمایش کلاینت‌های متصل به سرور
chronyc clients

# نمایش آمار سرور و همتایان
chronyc serverstats

# بررسی وضعیت زمان سیستم
chronyc activity
```

### تنظیم زمان

```bash
# تنظیم پلکانی فوری
chronyc makestep

# همگام‌سازی اجباری
chronyc burst 4/4

# تنظیم دستی زمان
chronyc manual on
chronyc settime HH:MM:SS
```

### مدیریت سرور

```bash
# اضافه کردن سرور NTP جدید
chronyc add server ntp.example.com

# حذف سرور
chronyc delete ntp.example.com

# حالت آنلاین/آفلاین
chronyc online
chronyc offline

# دامپ همه اندازه‌گیری‌ها
chronyc dump
```

### مدیریت منابع

```bash
# نمایش انتخاب منبع
chronyc sources -v

# انتخاب مجدد منبع
chronyc reselect

# نمایش داده‌های NTP
chronyc ntpdata
```

## مدیریت سرویس

```bash
# راه‌اندازی سرویس
sudo systemctl start chronyd

# توقف سرویس
sudo systemctl stop chronyd

# راه‌اندازی مجدد سرویس
sudo systemctl restart chronyd

# بررسی وضعیت سرویس
sudo systemctl status chronyd

# فعال‌سازی در بوت
sudo systemctl enable chronyd

# غیرفعال‌سازی در بوت
sudo systemctl disable chronyd

# بارگذاری مجدد پیکربندی
sudo systemctl reload chronyd
```

## توضیح خروجی Chronyc Sources

```bash
chronyc sources
```

ستون‌های خروجی:
- `M` - حالت: `^` = سرور، `=` = همتا، `#` = ساعت مرجع محلی
- `S` - وضعیت: `*` = همگام‌سازی فعلی، `+` = ترکیب‌شده، `-` = ترکیب نشده، `?` = غیرقابل دسترس
- `Name/IP` - نام میزبان یا آدرس IP
- `Stratum` - فاصله از ساعت مرجع
- `Poll` - بازه نظرسنجی (log2 ثانیه)
- `Reach` - رجیستر دسترسی‌پذیری (هشت‌هشتی)
- `LastRx` - زمان از آخرین بسته دریافتی
- `Last sample` - اختلاف، خطای تخمینی

## خروجی Chronyc Tracking

```bash
chronyc tracking
```

نمایش:
- شناسه و نام مرجع
- سطح Stratum
- اختلاف زمان سیستم
- تأخیر و پراکندگی ریشه
- تنظیم فرکانس
- زمان آخرین به‌روزرسانی

## عیب‌یابی

```bash
# بررسی اجرای chronyd
systemctl status chronyd

# تأیید همگام‌سازی زمان
chronyc tracking

# بررسی منابع قابل دسترس
chronyc sources

# مشاهده لاگ‌های chrony
sudo journalctl -u chronyd

# بررسی خطاها
sudo journalctl -u chronyd -p err

# همگام‌سازی فوری اجباری
sudo chronyc -a makestep
```

## بهترین روش‌ها

1. **استفاده از منابع زمان متعدد** - حداقل 3-4 سرور NTP پیکربندی کنید
2. **استفاده از گزینه iburst** - همگام‌سازی اولیه را تسریع می‌کند
3. **امن‌سازی دسترسی** - از `allow`/`deny` برای محدودسازی دسترسی کلاینت استفاده کنید
4. **فعال‌سازی همگام‌سازی RTC** - از `rtcsync` برای همگام نگه‌داشتن ساعت سخت‌افزاری استفاده کنید
5. **نظارت منظم** - به طور دوره‌ای `chronyc tracking` را بررسی کنید
6. **استفاده از pool** - دستور pool را برای قابلیت اطمینان بیشتر ترجیح دهید
7. **پیکربندی makestep** - اجازه تنظیمات پلکانی برای اختلاف‌های زمانی بزرگ

## پیکربندی نمونه

```bash
# /etc/chrony/chrony.conf

# سرورهای NTP
pool 2.debian.pool.ntp.org iburst
pool ntp.ubuntu.com iburst maxsources 2

# مرجع محلی در صورت قطع ارتباط
local stratum 10

# اجازه به شبکه محلی
allow 192.168.1.0/24

# فایل انحراف
driftfile /var/lib/chrony/drift

# همگام‌سازی RTC
rtcsync

# تنظیم پلکانی اگر اختلاف > 1 ثانیه در 3 به‌روزرسانی اول
makestep 1.0 3

# دایرکتوری لاگ
logdir /var/log/chrony
```

## مرجع سریع

| دستور | توضیحات |
|---------|-------------|
| `chronyc tracking` | نمایش وضعیت همگام‌سازی زمان فعلی |
| `chronyc sources` | لیست منابع زمان |
| `chronyc sources -v` | اطلاعات کامل منابع |
| `chronyc sourcestats` | نمایش آمار منابع |
| `chronyc clients` | نمایش کلاینت‌های متصل به این سرور |
| `chronyc makestep` | تنظیم فوری زمان |
| `chronyc activity` | نمایش فعالیت سرور |
| `systemctl restart chronyd` | راه‌اندازی مجدد سرویس chrony |

---

*برای اطلاعات بیشتر، به `man chrony.conf` و `man chronyc` مراجعه کنید*
